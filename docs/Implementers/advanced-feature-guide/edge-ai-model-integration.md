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

Models are **delivered over the air**. Nothing model-related ships inside the app: the model is configured by an administrator, downloaded by the device during sync, and then runs entirely offline. Updating a model's weights does **not** require a new app release.

> 📘 Avni supplies the runtime, not the model
>
> Avni provides the on-device inference runtime and the delivery mechanism. The organisation running the programme supplies its own trained model and owns it.

## How it works

Every capability is driven by declarative configuration. An administrator creates a **Downloadable Content** record describing the model: which inference engine runs it, how its input image is preprocessed, and how its raw output tensor is decoded into a result. The native bridge is engine- and model-agnostic; per-model semantics live entirely in this configuration. Onboarding a new model that uses existing preprocessor/decoder plugins requires **no app-code change** — only a configuration record and the model file.

### Configuring a model

In the Avni admin app, go to **App Designer → Downloadable Content** and create a record:

| Field | Purpose |
| --- | --- |
| `name` | A human-readable name for the model. |
| `category` | `edgeModel` for on-device inference models. |
| `sha256` | SHA-256 of the **decrypted** model bytes. This is the content address: it identifies the object in storage, the device's cache entry, the key-store entry, and the post-decrypt integrity check. |
| Blob | The AES-GCM-encrypted model file, uploaded through the same screen. |
| `needsKey` | Whether the device must fetch a decryption key for this model. |
| AES key | Write-only. Sent to the server key store, never stored on the record and never returned to the browser. |
| `payload` | The engine / preprocessor / decoder configuration (below). |

The `payload` block is **pure data** — no executable code — so a model's complete behaviour is auditable from JSON alone:

```json
{
  "engine": "onnx",
  "input": {
    "preprocessor": "imagenet-rgb-chw",
    "params": { "size": [224, 224], "interpolation": "bilinear" }
  },
  "output": {
    "decoder": "argmax-labels",
    "params": { "labels": ["Class A", "Class B", "Class C"] }
  }
}
```

| Block | Purpose |
| --- | --- |
| `engine` | Which inference runtime executes the model. |
| `input` | Named preprocessor plugin + its parameters. |
| `output` | Named decoder (post-processor) plugin + its parameters. |

### How a model reaches the device

1. The encrypted model file is stored in cloud storage — Avni's, or the organisation's own cloud account under its own access control.
2. The AES key is held **only** in a server-side key store. It is never in the app, never on the configuration record, and never in cloud storage alongside the model.
3. On its next sync, the device receives the configuration record, then fetches the encrypted file and the key separately.
4. The model is decrypted on-device and verified against `sha256` before loading. A corrupt or mismatched file is rejected rather than producing a wrong result.
5. From then on, inference runs fully offline.

**Updating weights requires no app release.** New weights produce a new SHA-256; upload the new file, update the record, load the new key, and devices refresh on their next sync. Changing the *preprocessor or decoder code* does still require an app release.

## Engines (runtime dependencies)

| Engine key | Runtime |
| --- | --- |
| `onnx` | ONNX Runtime Mobile 1.22.0 |

Models must be supplied as **ONNX** exports. PyTorch, TensorFlow and scikit-learn all export to ONNX as a standard step.

> 🚧 PyTorch Mobile has been removed
>
> Earlier versions used PyTorch Mobile 1.13.1. Its prebuilt native libraries are 4 KB page-aligned, which Google Play rejects for `targetSdk 35`, and PyTorch Mobile is deprecated upstream with no fix planned. ONNX Runtime's 64-bit libraries are 16 KB-aligned and Play-compliant.

The engine layer is plugin-based (a small `InferenceEngine` interface on the native side), so additional runtimes such as TensorFlow Lite can be added without changes to the bridge or to existing configuration. Any candidate runtime must ship **16 KB page-aligned** native libraries to stay Play-compliant, and must accept a single float32 input tensor and return a single float32 output tensor.

### What the runtime expects of a model

| Aspect | Requirement |
| --- | --- |
| Inputs | Exactly one input tensor, float32 |
| Input shape | `[1, C, H, W]` — NCHW, batch size 1. NHWC models must be transposed at export |
| Outputs | The first output only, and it must be a float32 tensor |
| Quantisation | Internally quantised models with float32 edges are fine; int8 input/output edges are not supported |

Models exported with an argmax or label head emit `int64` and will fail — export logits instead and let a decoder handle them.

## Preprocessors

Preprocessors turn the captured image into the input tensor the model expects. EXIF orientation is applied before preprocessing. Two plugins are available; each is fully parameterised from the configuration:

### `imagenet-rgb-chw`

Standard ImageNet-style normalisation: resize → scale to `[0, 1]` → per-channel mean/std normalisation → RGB CHW tensor.

| Param | Default | Meaning |
| --- | --- | --- |
| `size` | `[224, 224]` | Target width × height |
| `channels` | `3` | Channel count. **Only `3` is supported** — other values are not handled correctly |
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
| `layout` | `"CHW"` | Tensor layout. **Leave as `CHW`** — `HWC` reorders the buffer without changing the reported tensor shape |
| `scale` | `1/255` | Final scaling factor (applied after the uint8 cast) |
| `mean_target` | `128` | Per-channel target mean |
| `round_decimals` | `1` | Decimal places to round to before the cast |
| `uint8_cast` | `true` | Truncate to uint8 before final scaling |
| `uint8_round` | `false` | `true` rounds to nearest before the cast; `false` truncates |

Adding a new preprocessing pipeline means dropping a new plugin class into the app's preprocessor registry — the bridge and config format stay unchanged.

## Post-processors (decoders)

Decoders turn the model's raw output tensor into a structured result for rules:

| Decoder key | Use case | Params | Result |
| --- | --- | --- | --- |
| `argmax-labels` | Multi-class classification | `labels: [...]` | `{ label, confidence (softmax prob), classIndex, raw }` |
| `sigmoid-binary` | Single-logit binary classification | `threshold` (default `0.5`), `labels: [negative, positive]` | `{ label, confidence (sigmoid prob), logit, threshold, raw }` |
| `raw-floats` | Regression heads, multi-label, anything custom | — | `{ raw: number[], shape }` — post-process in the rule |

Like preprocessors, decoders are plugins: new output semantics = a new decoder class, registered by name.

> 📘 `argmax-labels` always returns a label
>
> Multi-class classification picks the highest-scoring category. There is no confidence threshold and no "none of the above" — an unrelated image is still sorted into whichever configured category scores highest. If you need the model to reject unsuitable images, **train an explicit rejection class** (for example `other`) and include it in `labels`.
>
> `labels` is positional and must match your model's output ordering exactly. A mismatch mislabels silently.

## Using models from form rules

Rules access inference through `edgeModelService` (available via `params.services`). The model resolves from the synced configuration — rules do not name it.

**Awaited (raw result returned to the rule)** — call this from a decision rule, which may be asynchronous:

```js
const result = await params.services.edgeModelService.runInferenceOnImage(imagePath);
// result.label, result.confidence, ...
```

**Asynchronous, result written to an observation** — the rule returns immediately; when inference resolves, the (optionally label-mapped) verdict is written to the target observation and the form re-renders. Use this from a form-element rule, which **must** return synchronously:

```js
params.services.edgeModelService.scheduleImageInference(
  imagePath, encounter, 'AI Screening Result',
  { 'Positive': 'Suspicious', 'Negative': 'Not Suspicious' }   // optional labelMap
);
```

**Target inside a Repeatable Question Group row:**

```js
params.services.edgeModelService.scheduleImageInferenceIntoGroup(
  imagePath, encounter,
  'Lesion Group', 'AI Screening Result', rowIdx,
  labelMap
);
```

Rules written against the older signature — which took a leading model key — continue to work. The key is accepted and ignored.

Two things to watch:

- **Coded targets:** the stored value (after `labelMap`) must exactly match an answer concept name of the target concept, or the write is skipped. Text targets store the string verbatim.
- **Repeatable groups:** the row at `rowIdx` must already exist — capture the image into the row first. Rows are not created automatically.

The async path deduplicates in-flight jobs (form-element rules re-fire on every observation change), and detects when the user retakes a photo — re-running inference instead of returning the stale verdict.

### When no verdict can be produced

If the model has not finished downloading, or inference fails, **no verdict is written** — an absent verdict must never read as a negative one. The form raises a validation error on the target element and blocks the user from continuing, rather than letting them proceed on a missing result. Recovery is to sync; inference never downloads at the point of use.

### Ensemble inference

Configuring **several `edgeModel` records** turns them into an ensemble — intended for cross-validation folds of the same model. All folds run against the image and their verdicts combine.

| Combiner | Behaviour |
| --- | --- |
| `unanimous-and` (default, and the only value currently supported) | The result is positive only when **every** fold decodes positive. Reduces false positives relative to a majority vote. |

Set it via `output.params.combine` in the payload. The combined result is shaped like a single model's, plus a `perModel` breakdown; reported confidence is the weakest fold's. If any fold's model file or key has not been cached yet, no verdict is produced.

## Multi-model support & lifecycle

- **All configured `edgeModel` records are treated as folds of one logical model.** There is no per-rule model selection — a rule cannot choose between several distinct models. If you need one form to check for several different things, train a **single multi-class model** and use `argmax-labels`.
- Models **lazy-load on first use** and stay warm for the app's lifetime.
- Under OS **memory pressure**, loaded models are evicted and transparently **self-heal** on the next inference call — rules never need to handle reloading.
- Model size matters on low-end devices. A production deployment holding three ~17 MB models resident required thread-pool and allocator tuning to remain stable on 4-core / 768 MB hardware. MobileNet- or EfficientNet-class classifiers are considerably lighter.

## Security

- The model file is stored **AES-GCM encrypted**, and the decrypted bytes are verified against a SHA-256 checksum before loading.
- The encryption key lives **only in a server-side key store**, delivered to authenticated devices at sync time. It is not in the app, not on the configuration record, and not in cloud storage.
- Because the file and the key are held separately, obtaining the plaintext model requires compromising both.
- Model files can be kept in the **organisation's own cloud account**, separately from its other Avni data.

There is a brief on-disk plaintext window at load time: the runtime loads from a file path, so the decrypted model is written to an app-private file, loaded, and deleted immediately afterwards.
