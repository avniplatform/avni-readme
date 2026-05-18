---
title: Auto Share Filled Forms
excerpt: Prompt field user automatically to share form data on filling it
deprecated: false
hidden: false
metadata:
  robots: index
---
## What this feature does

For specific forms, an organisation can choose to have the share action fire automatically the moment the form is saved. The field worker doesn't need to find or tap the Share button afterwards — the phone's share screen pops up directly.

This is useful when sharing is always expected for a particular form (for example, a household visit form that always needs to be sent to a supervisor or filed into a register).

## What the field worker sees

* For forms where auto-share is **not** turned on: nothing changes. The Share button works exactly as it does for any other form.
* For forms where auto-share **is** turned on: the moment the form is saved, the phone's share screen pops up directly. The worker picks WhatsApp, email, drive, etc., and sends — no extra taps to get there.
* The organisation decides whether each form is shared as a **PDF** or as **text**; the worker does not have to choose each time.
* If the worker dismisses the share screen without sending, nothing breaks. The app continues normally.

## What an implementor configures

The implementor decides, for each form independently:

1. **Whether** the form should auto-share when saved (for example: "Household Visit" yes, "Member Registration" no).
2. **Which format** is sent out — PDF or text.
3. Optionally, **what the shared content looks like** — using the same Share rule mechanism that powers the manual Share button. If no custom format is set up, sensible defaults are used.

This is configured once on the webapp; field workers never see this configuration.

## Behaviour notes

* The format (PDF or text) sent out matches exactly what the implementor configured for the form.
* If a custom share format is set up, the custom format is used; otherwise the default is used.
* The manual Share button continues to work as normal on forms that do not have auto-share turned on. The two paths do not interfere with each other.
* Dismissing the share screen does not cause the app to retry, get stuck, or lose the saved form. The save itself is unaffected by whether the share is completed.
