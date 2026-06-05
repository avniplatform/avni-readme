---
title: Edge AI Model Integration
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
The Avni Android client can run AI/ML models directly on the field worker's device — fully offline. Form rules trigger inference (for example, on a photo captured in the form) and the decoded result is written into form observations, so AI verdicts flow through the normal Avni data pipeline: validations, sync, reports.

> 🚧 Beta — build-time model inclusion
>
> Models are currently bundled into the APK at build time, per app flavour. Shipping a new or updated model requires producing a new APK build, and the bundled inference runtime adds ~16 MB to the app's download size. A remote model delivery mechanism is planned — see [Roadmap](#roadmap-remote-model-delivery) below.

## How it works

Every capability is driven by a declarative **model registry** — a JSON file (`assets/models/registry.json`) bundled with the app. The registry describes each model: which inference engine runs it, how its input image is preprocessed, and how its raw output tensor is decoded into a result. The native bridge is engine- and model-agnostic; per-model semantics live entirely in this configuration. Onboarding a new model that uses existing preprocessor/decoder plugins requires **no app-code change** — only a registry entry and the model asset.

### Model registry

```json
{
  "models": {
    "screening-model-v1": {
      "asset": {
        "type": "encrypted",
        "path": "models/screening-model-v1.bin",
        "encryptionKey": "<base64 AES-256 key>",
        "sha256OfPlaintext": "<sha256 of the decrypted model bytes>"
      },
      "override": {
        "engine": "pytorch",
        "input": {
          "preprocessor": "imagenet-rgb-chw",
          "params": { "size": [224, 224], "interpolation": "bilinear" }
        },
        "output": {
          "decoder": "argmax-labels",
          "params": { "labels": ["Class A", "Class B", "Class C"] }
        }
      }
    }
  },
  "defaultModel": "screening-model-v1"
}
```

Each model entry declares:

| Block | Purpose |
| --- | --- |
| `asset` | Where the model bytes live and how to load them. `type: "plain"` loads the asset directly; `type: "encrypted"` decrypts an AES-GCM-encrypted blob (with SHA-256 integrity verification of the decrypted bytes) before loading. |
| `override.engine` | Which inference runtime executes the model (the model's runtime dependency). |
| `override.input` | Named preprocessor plugin + its parameters. |
| `override.output` | Named decoder (post-processor) plugin + its parameters. |
| `defaultModel` | The model key used when a rule doesn't specify one. |

The override block is **pure data** — no executable code — so a model's complete behaviour is auditable from JSON alone.

## Engines (runtime dependencies)

| Engine key | Runtime |
| --- | --- |
| `pytorch` | PyTorch Mobile 1.13.1 |

The engine layer is plugin-based (a small `InferenceEngine` interface on the native side), so additional runtimes such as TensorFlow Lite or ONNX Runtime can be added without changes to the bridge or to existing registry entries.

## Preprocessors

Preprocessors turn the captured image into the input tensor the model expects. Two are available today; each is fully parameterised from the registry:

### `imagenet-rgb-chw`

Standard ImageNet-style normalisation: resize → scale to `[0, 1]` → per-channel mean/std normalisation → RGB CHW tensor.

| Param | Default | Meaning |
| --- | --- | --- |
| `size` | `[224, 224]` | Target width × height |
| `channels` | `3` | Channel count |
| `scale` | `1/255` | Pixel scaling factor |
| `mean` | `[0.485, 0.456, 0.406]` | Per-channel mean |
| `std` | `[0.229, 0.224, 0.225]` | Per-channel std-dev |
| `interpolation` | `"bilinear"` | Resize interpolation (`bilinear`, `cubic`, `nearest`) |

### `mean-target-bgr-rounded`

A per-image dynamic white-balance pipeline (gray-world style): resize → scale each channel so its mean hits `mean_target` → clip → round → uint8 cast → scale to `[0, 1]` → CHW tensor in the configured channel order. Useful for models trained against this exact preprocessing math.

| Param | Default | Meaning |
| --- | --- | --- |
| `size` | `[256, 256]` | Target width × height |
| `interpolation` | `"bilinear"` | Resize interpolation (`bilinear`, `cubic`, `nearest`) |
| `channel_order` | `"BGR"` | Channel write order (`RGB` or `BGR`) |
| `layout` | `"CHW"` | Tensor layout |
| `scale` | `1/255` | Final scaling factor (applied after the uint8 cast) |
| `mean_target` | `128` | Per-channel target mean |
| `round_decimals` | `1` | Decimal places to round to before the cast |
| `uint8_cast` | `true` | Truncate to uint8 before final scaling |

Adding a new preprocessing pipeline means dropping a new plugin class into the app's preprocessor registry — the bridge and config format stay unchanged.

## Post-processors (decoders)

Decoders turn the model's raw output tensor into a structured result for rules:

| Decoder key | Use case | Params | Result |
| --- | --- | --- | --- |
| `argmax-labels` | Multi-class classification | `labels: [...]` | `{ label, confidence (softmax prob), classIndex, raw }` |
| `sigmoid-binary` | Single-logit binary classification | `threshold` (default `0.5`), `labels: [negative, positive]` | `{ label, confidence (sigmoid prob), logit, threshold, raw }` |
| `raw-floats` | Regression heads, multi-label, anything custom | — | `{ raw: number[], shape }` — post-process in the rule |

Like preprocessors, decoders are plugins: new output semantics = a new decoder class, registered by name.

## Using models from form rules

Rules access inference through `edgeModelService` (available via `params.services`):

**Synchronous (raw result returned to the rule):**

```js
const result = await params.services.edgeModelService.runInferenceOnImage(
  'screening-model-v1', imagePath
);
// result.label, result.confidence, ...
```

**Asynchronous, result written to an observation** — the rule returns immediately; when inference resolves, the (optionally label-mapped) verdict is written to the target observation and the form re-renders:

```js
params.services.edgeModelService.scheduleImageInference(
  'screening-model-v1', imagePath, encounter, 'AI Screening Result',
  { 'Positive': 'Suspicious', 'Negative': 'Not Suspicious' }   // optional labelMap
);
```

**Target inside a Repeatable Question Group row:**

```js
params.services.edgeModelService.scheduleImageInferenceIntoGroup(
  'screening-model-v1', imagePath, encounter,
  'Lesion Group', 'AI Screening Result', rowIdx,
  labelMap
);
```

The async path deduplicates in-flight jobs (form-element rules re-fire on every observation change), and detects when the user retakes a photo — re-running inference instead of returning the stale verdict.

### Ensemble (soft-vote) inference

Anywhere a model key is accepted, an **array of model keys** runs a soft-vote ensemble — useful for cross-validation folds of the same model:

```js
params.services.edgeModelService.scheduleImageInference(
  ['model-fold-1', 'model-fold-2', 'model-fold-3'], imagePath, encounter,
  'AI Screening Result', labelMap
);
```

All models run in parallel; their outputs combine via `mean-prob` (average sigmoid probabilities, default) or `mean-logit` (average logits, then sigmoid). Threshold and labels default to the folds' shared decoder config from the registry. The combined result is shaped like a single model's, plus a `perModel` breakdown.

## Multi-model support & lifecycle

- Any number of models can be registered and used **simultaneously** — each rule call selects its model(s) by key.
- Models **lazy-load on first use** and stay warm for the app's lifetime.
- Under OS **memory pressure**, loaded models are evicted and transparently **self-heal** on the next inference call — rules never need to handle reloading.

## Secure model packaging

Model assets can be bundled **AES-GCM-encrypted**, with the decrypted bytes verified against a SHA-256 checksum before the model is loaded. This prevents casual extraction of a proprietary model from the APK.

## Current limitation — build-time model inclusion

Models are included in the APK at **build time**, per app flavour:

- Shipping a new or updated model requires building and distributing a new APK.
- Models are per-build, not per-tenant — every installation of a flavour carries the same models.
- The bundled inference runtime adds ~16 MB to the app download size.

## Roadmap — remote model delivery

A remote model delivery mechanism is planned: tenant-specific models (one or several, as needed) will be downloaded to the device during first-time sync and dropped into place, after which rules use them exactly as they use bundled models today. This decouples model rollout and updates from app releases entirely.
