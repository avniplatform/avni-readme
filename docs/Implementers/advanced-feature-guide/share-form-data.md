---
title: Share Form Data
excerpt: Ability for field users to share form data as pdf or text
deprecated: false
hidden: false
metadata:
  robots: index
---
## What this feature does

Field workers can share a filled form in two different ways:

* As a **PDF** that looks like an official register page — useful for records, filing, or forwarding as an attachment.
* As **WhatsApp-friendly text** — useful when a supervisor or family member should be able to read the contents directly in a chat message, without opening an attachment.

Organisations can decide what each format looks like for any given form. If nothing is customised, sensible defaults are used automatically.

## What the field worker sees

* Tapping **Share** on a saved form opens a small menu with two choices:
  * **Share as PDF**
  * **Share as Text**
* Picking **Share as PDF** opens the phone's share screen with a PDF attached. The worker can send it via WhatsApp, email, drive, etc.
* Picking **Share as Text** opens the phone's share screen with the form's contents pasted as a message, ready to send into WhatsApp, SMS, email, etc.
* The same Share permission covers both options — workers who can share can use both; workers who cannot share will not see either.

## What an implementor can configure

For any form, an implementor can set up either or both of:

* A **custom PDF layout** — for example, one that mimics a government register format the form is meant to feed into.
* A **custom text message** — for example, a friendly WhatsApp summary using bold, italics, and bullets.

Whichever of the two is not customised falls back to the default for that format. Configuring neither is also fine — both options still work using defaults.

Translations of any custom text content can be exported from the webapp the same way other translated content is exported, and re-imported without losing characters or formatting.

## The defaults (when nothing is customised)

**Default PDF:** the standard PDF layout produced by the Share button.

**Default text:** a plain-language summary of the form, including:

* the subject's name, gender, age, and location at the top,
* the form name and date,
* each question and the answer the worker filled in, in order,
* a footer with the app name, the worker's name, and the date and time of sharing.

Photos, audio, and other media answers are skipped in the default text, since they cannot be pasted into a chat message.

## Behaviour notes

* The PDF / Text menu always appears when Share is tapped — there is no path that skips it.
* If a custom PDF layout is configured, **Share as PDF** uses it; otherwise it uses the default PDF.
* If a custom text message is configured, **Share as Text** uses it; otherwise it uses the default text summary.
* For a form with no answers filled in, **Share as Text** still produces the subject header and footer — the message is never empty.
* Photos and audio appear in the PDF (when the PDF layout includes them) but are always skipped in text.
