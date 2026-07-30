---
title: Hiding the Visit Date in Encounters
deprecated: false
hidden: true
metadata:
  robots: index
---
Sometimes we want to hide the visit date and auto-capture it instead of letting the field user see and change it. Avni gives you the facility to do this with a single organisation-wide setting.

## Enabling "Hide Visit Date"

![]()

![](https://files.readme.io/d24ef9bc95bc7b79f60a66ab8fa043b71680eaf14f45c3ed9c1daa70aefd1fae-Screenshot_2026-07-30_at_2.51.10_PM.png)

<br />

1. Open the **Admin** panel.
2. Go to the **Organisation details** (Organisation settings) tab.
3. Turn on **Hide Visit Date** and save.

Once enabled, the Visit Date field no longer appears on any encounter form in the mobile app — for **both general and program encounters**. The date is still captured and saved (and shows up in reports/exports); it just isn't shown or editable.

# How the date is captured

The visit date is **always** recorded — only its visibility changes. What value gets saved depends on the situation:

| Situation                         | Visit date saved                                                                |
| --------------------------------- | ------------------------------------------------------------------------------- |
| **New / unscheduled encounter**   | Today — the day the form is filled                                              |
| **Editing an existing encounter** | Its original saved date, kept unchanged                                         |
| **Scheduled / planned visit**     | Today — the day the form is filled (auto-captured, since the user can't set it) |

<br />

<br />
