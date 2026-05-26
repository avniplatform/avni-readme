---
title: Attendance
excerpt: Enable first-class attendance on Group Subject Types, with per-attendance-type reason vocabulary, follow-up encounters, and optional WhatsApp share.
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
  pages: []
---
## Overview

Avni's **Attendance** feature is a first-class way to record presence and absence for members of a Group Subject Type (for example, students in a school's `Class`, or beneficiaries in a daily-meeting group). It replaces the older practice of capturing attendance as a subject-multiselect on a custom encounter form, and it lets reports distinguish "the form wasn't filled today" from "no class was expected today".

The model has three moving parts:

* **Attendance Types** — named attendances per Group Subject Type. A school taking three separate attendances per day (Morning Prayer, Math Class, Afternoon Reading) configures three Attendance Types. A group taking one daily attendance configures one.
* **Sessions** — one Session is created when a worker marks attendance (or marks that the class did not happen) for a given (group, date, attendance type) triple. Sessions are created **lazily** — no row exists until something is recorded.
* **Attendance Records** — one row per group member per Session, with status `Present` or `Absent` and an optional reason.

Each Attendance Type carries its own behavioural config — reason vocabulary, follow-up workflow, share template — so different attendances within the same Subject Type can behave differently.

## When to use it

Configure attendance when your organisation needs to:

* Track presence/absence of members of a group on specific dates.
* Distinguish "did not happen" from "happened but not marked" in reports.
* Auto-create a follow-up encounter (for example, a Home Visit) for any absent member whose absence reason is unknown.
* Optionally share an attendance summary on WhatsApp from the field worker's phone.

If your organisation only needs occasional presence tracking embedded in another form, attendance configuration may be overkill — a regular Encounter Type might be simpler.

## Prerequisites

Before enabling attendance on a Subject Type, make sure:

* **A calendar exists.** See [Calendars](doc:calendars). Attendance uses the calendar to determine which dates are expected sessions.
* **A Group Subject Type and its member Subject Type both exist.** Attendance is configured on the Group Subject Type; the roster on the Android dashboard shows members of the member Subject Type. See [Subject types](doc:subject-types).
* **Two coded concepts are ready:**
  * A *Session Outcome Reason* concept — the answer-set for "why didn't the class happen" / "why was attendance not recorded".
  * An *Absence Reason* concept — the answer-set for "why was this individual student absent today".
  * The two examples in the [Example coded concepts](#example-coded-concepts) section below show typical answer sets.
  * See [Concepts](doc:concepts) for how to create coded concepts.

## Enabling attendance on a Subject Type

### Step 1 — Open the Subject Type's Advanced section

Navigate to **App Designer → Subject Types**, pick the Group Subject Type (for example, `Class`), and switch to the **Advanced** tab. You will see a new **Attendance** sub-block.

<!-- TODO: upload `disabled-attendance.png` from /Users/himeshr/Desktop/ — Subject Type Advanced section showing the Attendance sub-block with the "Enable attendance" toggle in its initial off state; clean starting point before any types are configured -->
<Image align="center" width="900px" src="" alt="Subject Type Advanced section showing the Attendance sub-block with the Enable attendance toggle in its off (initial) state" />

### Step 2 — Toggle Enable attendance

Toggle **Enable attendance** on. Avni seeds a default Attendance Type named `Attendance` so the types list always has at least one row to edit. The seed row is **incomplete** — it has no reason concepts set yet — and the page shows a warning banner under the types list naming the incomplete type.

<!-- TODO: upload `partial-attendance-default-config-with-warning.png` from /Users/himeshr/Desktop/ — Attendance sub-block with Enable attendance toggled on, showing the seeded "Attendance" row in the types list, an amber warning banner reading "The following attendance types are incomplete and must be configured: Attendance", and the ADD ATTENDANCE TYPE button -->
<Image align="center" width="900px" src="" alt="Types list with the seeded Attendance row and an incomplete-types warning banner naming the row that needs reason concepts" />

### Step 3 — Edit the seed Attendance Type

Tap **EDIT** on the seed row. A modal opens with the per-Attendance-Type configuration fields. Fill the two required reason concepts (Session Outcome Reason and Absence Reason) and save. The warning banner clears once every Attendance Type has both reason concepts filled.

> **Important:** Saving the Subject Type is **blocked** until every non-voided Attendance Type has both reason concepts filled. You will see a structured error pointing at any incomplete type.

## Configuring an Attendance Type

The per-Attendance-Type Edit modal has the following fields:

* **Name** (required) — for example, "Morning Prayer", "Math Class", or simply "Attendance".
* **Sort order** — controls the display order of types on the field worker's dashboard.
* **Session Outcome Reason concept** (required) — answers populate the Session-level reason picker. This is used in three flows: when the worker marks "Didn't Happen" (the class didn't take place), when the worker uses Mark anyway on a holiday/weekly-off day, and when the worker records "attendance not recorded" retrospectively.
* **Absence Reason concept** (required) — answers populate the inline per-student reason dropdown on the roster, shown whenever a student is toggled to Absent.
* **Follow-up encounter type** (optional) — when set, an Absent student whose reason was left blank automatically generates a follow-up encounter of this type (due today; max date today + 2 days). When unset, no auto follow-up is created.
* **Share rule (inline JS)** (optional, Android only) — a JavaScript snippet that builds the share content for a saved Session. See [Example share rule](#example-share-rule) below.
* **Auto-share on save** (optional, Android only) — when on, the Android share sheet pops up automatically after every saved Session of this type. When the share rule is empty, the built-in default template is used.

<!-- TODO: upload `edit-attendance-type-modal.png` from /Users/himeshr/Desktop/ — Edit Attendance Type modal for "Math Class", all fields populated: Name = Math Class, Sort Order = 3, Session Outcome Reason Concept = Session Outcome, Absence Reason Concept = Absence Reason, Follow-up Encounter Type dropdown open showing "None" + "Absence Followup" options, inline JS share rule snippet, Auto-share on save toggle (off), CANCEL / DONE buttons -->
<Image align="center" width="800px" src="" alt="Edit Attendance Type modal for Math Class with all fields populated and the Follow-up Encounter Type dropdown open" />

> **Tip — using the same concept for both reason fields.** You can pick the same coded concept for *Session Outcome Reason* and *Absence Reason* on a single Attendance Type. The modal will show a warning banner ("Both reason pickers will show the same answer set in this attendance type") but the save is allowed — useful when an organisation's session-level and per-student reasons genuinely overlap.

<!-- TODO: upload `same-concept-warning.png` from /Users/himeshr/Desktop/ — Edit Attendance Type modal showing the amber warning banner "Both reason pickers will show the same answer set in this attendance type" at the top, with both Session Outcome Reason Concept and Absence Reason Concept dropdowns set to the same concept ("Session Outcome") -->
<Image align="center" width="800px" src="" alt="Edit Attendance Type modal with the warning banner shown when both reason pickers are set to the same coded concept" />

### Example — Gubbachi school's three-attendances-per-day model

A Gubbachi school configures three Attendance Types on its `Class` Subject Type:

* **Morning Prayer** — Session Outcome Reason: "School Session Outcome"; Absence Reason: "Student Absence Reason"; Follow-up encounter type: *(none)*; Auto-share: off.
* **Math Class** — same reasons as Morning Prayer, plus Follow-up encounter type: "Home Visit"; Share rule configured; Auto-share: on.
* **Afternoon Reading** — same as Math Class but with Follow-up encounter type: "Phone Call".

Each Attendance Type carries its own config, so they can differ even though they live inside the same Subject Type.

<!-- TODO: upload `list-attendance-types.png` from /Users/himeshr/Desktop/ — Attendance sub-block with three configured attendance types in the list: Morning Prayer, Afternoon Reading, Math Class (each row with drag handle, edit pencil, delete bin) plus the ADD ATTENDANCE TYPE button and the calendar-resolution note below -->
<Image align="center" width="900px" src="" alt="Attendance types list with three configured types: Morning Prayer, Afternoon Reading, Math Class" />

## Example coded concepts

The two reason concepts you pick on each Attendance Type need to have sensible answer sets. Below are the recommended starter sets — adapt these to your organisation's vocabulary.

### Session Outcome Reason concept

This concept's answers populate the **Session-level** reason picker. Recommended answer set spans three semantic groups so a single picker can serve every Session-level flow for this attendance type:

* **Class did not happen** — used when the worker marks "Didn't Happen":
  * Teacher absent
  * Power cut
  * Strike / civil disruption
  * Weather
  * Equipment failure
  * Holiday not on calendar
* **Class held outside expectation** — used when the worker uses Mark anyway on a holiday/weekly-off day:
  * Make-up class
  * Special event / workshop
  * Festival celebration
  * Other override
* **Attendance not recorded** — used when the worker retrospectively marks a past working-day session as not-recorded (forgot to mark, lost app access, etc.):
  * Marker absent
  * Marker unable to access app
  * Forgot to mark
  * Other no-data reason

### Absence Reason concept

This concept's answers populate the **per-student** dropdown that appears beneath any roster row toggled to Absent. The recommended answer set is simpler:

* Sick
* Family event
* Transport
* Personal
* Other

Leaving the reason blank for an Absent student counts as *"reason unknown"* and triggers an automatic follow-up encounter on save (if the Attendance Type has a follow-up encounter type configured).

> **Tip:** Different Attendance Types within the same Subject Type can pick different reason concepts — for example, Morning Prayer could use a simple absence-reason set while Math Class uses a richer set that includes academic-specific reasons. The flexibility is intentional.

## Example share rule

If you want a custom WhatsApp/Text share message instead of Avni's default template, configure a share rule on the Attendance Type. The rule is inline JavaScript that runs when the field worker taps Share (or when auto-share fires) on a saved Session.

Here is a copy-pasteable starter:

```js
({ params }) => {
  const { entity: session, attendanceRecords, summary } = params;

  // Build a list of absent students with their reason (if recorded).
  const absent = attendanceRecords
    .filter(r => r.status === 'Absent')
    .map(r => r.studentName + (r.reasonName ? ' (' + r.reasonName + ')' : ''));

  const text =
    '📋 ' + summary.groupName + ' — ' + summary.attendanceTypeName + '\n' +
    'Date: ' + summary.scheduledDate + '\n' +
    'Present: ' + summary.presentCount + ' · Absent: ' + summary.absentCount + '\n\n' +
    (absent.length
      ? 'Absent students:\n• ' + absent.join('\n• ')
      : 'Everyone present 🎉');

  return { text };
};
```

The rule receives `params` with:

* `entity` — the saved Session.
* `attendanceRecords` — an array of `{ studentName, status, reasonName?, followUpEncounterUUID? }` rows, one per group member.
* `summary` — a derived helper object with `{ groupName, attendanceTypeName, scheduledDate, presentCount, absentCount, presentNames, absentNames, sessionStatus, sessionNotes }`.
* `services` and other common-rule helpers (same as other Avni rules).

The rule returns `{ text?, data? }`:

* `text` — used when the user picks "Share as Text". If omitted, Avni falls back to a built-in default text template.
* `data` — used when the user picks "Share as PDF". If omitted, Avni falls back to a built-in default Session PDF template.

For more on writing JavaScript rules in Avni, see [Writing rules](doc:writing-rules).

## How field workers mark attendance (Android)

This section is a brief tour of the end-user experience. The exact look-and-feel may evolve — focus on the behaviour rather than pixel-perfect layouts.

### Opening the attendance sheet

The field worker opens a group's dashboard and taps the **Attendance** button. This button is visible only when the group's Subject Type has attendance enabled and the user has edit-subject privilege.

<!-- TODO: upload `grp-subject-dashboard.png` from /Users/himeshr/Desktop/ — Android group-subject dashboard for "Bal 1" (Sample village) showing the REGISTRATION DETAILS section and below it the new "Attendance · 3 attendance types" section with the "Attendance →" entry point -->
<Image align="center" width="400px" src="" alt="Android group-subject dashboard with the Attendance section and entry point" />

### Picking a date and an attendance type

The sheet opens with a short horizontal date strip (last 14 days plus today) and a row per configured Attendance Type. Each row shows the status for the selected date: "Not marked yet" with MARK / DIDN'T HAPPEN buttons, or "Held — X of Y" with an Edit link, or "Didn't happen — [reason]". Tapping any past day on the strip lets the worker mark retroactively.

<!-- TODO: upload `client-attendance-sheet.png` from /Users/himeshr/Desktop/ — Attendance sheet for "Bal 1" with the Jump-to-date row, horizontal date strip (Wed 20 to Tue 26, dots indicating Held / mixed / weekly-off states), a "Special duty — region-wide working day" banner, and three attendance-type rows: Morning Prayer (Held — 1 of 2 present, expanded showing a Void/Edit/Share action row plus the roster preview), Afternoon Reading (Not marked yet), Math Class (Not marked yet) -->
<Image align="center" width="400px" src="" alt="Attendance sheet showing the date strip, day-status banner, and the three-row attendance-type picker with one type already marked Held" />

### Marking a Held session

Tapping **MARK** opens the roster — one row per group member with a Present (default) / Absent toggle. When a student is toggled to Absent, an inline **Reason for absence** dropdown appears beneath the row, sourced from this Attendance Type's Absence Reason concept. The reason is optional; leaving it blank means "unknown" and triggers auto follow-up creation on save (if configured).

<!-- TODO: upload `morning-prayer-filling.png` from /Users/himeshr/Desktop/ — Roster screen for "Morning Prayer" — header reading "Tap to toggle Present → Absent" + "Mark all present" shortcut; first member "gana esha #1" toggled to Absent with the inline Reason for absence dropdown showing "Unwell" selected; second member "gowri shankara #2" Present; summary line "1 with reason · 0 without reason → 0 follow-ups will be created"; optional Session notes textarea; teal Save attendance button -->
<Image align="center" width="400px" src="" alt="Roster with one student toggled to Absent and the inline absence-reason dropdown showing the selected reason; summary line indicating no follow-ups will be created" />

### Marking Didn't Happen

The **DIDN'T HAPPEN** action opens a single-screen reason picker — a required reason dropdown sourced from this Attendance Type's Session Outcome Reason concept, plus optional notes. Save creates a Session with status `DidntHappen`.

### Mark anyway on holidays and weekly-offs

If the selected date is a public holiday or weekly-off per the resolved calendar, the type-picker rows show "Class not in session" with a **Mark anyway** action. Mark anyway proceeds to the marking flow but requires a reason (Session Outcome Reason concept) to capture *why* the override was made — for example, "Make-up class" on a Diwali.

## Auto-created follow-ups for unknown absences

When the worker saves a Held session, Avni inspects each Absent student's reason. For any student where the reason was left blank AND this Attendance Type has a follow-up encounter type configured, the server (or the Android client, offline-first) creates a follow-up encounter on that student in the same transaction. The encounter is due today; the max-date is today + 2 days.

A confirmation dialog appears after the save, listing the students for whom a follow-up was created.

<!-- TODO: upload `absentee-followup-created.png` from /Users/himeshr/Desktop/ — Android post-save "Follow-ups created" dialog reading "1 follow-up encounters have been created for absent students whose reason was blank." then listing gowri shankara · Roll 1 · Absence Followup · Due today · Max date: Thu 28 May 2026, with a single OK action -->
<Image align="center" width="400px" src="" alt="Android post-save Follow-ups created dialog listing the absent student for whom a follow-up encounter was auto-created" />

If the worker later corrects a Session (re-marks an Absent-no-reason student as Present, or fills in their reason), the previously-created follow-up encounter is automatically voided in the same transaction — **unless** the follow-up encounter has already been filled in (it has Observations), in which case it is left untouched and the dialog surfaces a warning.

> **Note:** Each Attendance Type's follow-up encounter type is honoured independently. A student absent in two attendance types on the same day, where both types have follow-up configured, gets two follow-up encounters (one per type).

## Sharing an attendance summary (Android only)

On any saved Session card on the Android dashboard, a **Share** button is visible. Tapping it opens a bottom sheet with "Share as PDF" and "Share as Text" options, which then invokes the Android system share sheet (WhatsApp, etc.). The share rule on the Attendance Type (if any) builds the content; otherwise a sensible default template is used.

If the Attendance Type has **Auto-share on save** enabled, the share sheet pops up automatically after every save of this type — the worker does not need to tap Share separately.

> The Share button and Auto-share are **Android only**. They do not appear on the webapp.

## Voiding a session

Each saved Session card has an overflow menu with a **Void** action. Voiding a Session also voids:

* All its Attendance Records (cascading).
* Any auto-created follow-up encounters linked from those records that are still unfilled (have no Observations).

Filled-in follow-up encounters are left untouched.

## Bundle transferability

Attendance configuration — the Subject Type's `enable attendance` toggle, the list of Attendance Types, and each type's full config (reason concept references, follow-up encounter type reference, share rule, auto-share toggle) — rides along with Avni's app bundle export/import. Operational data — recorded Sessions and Attendance Records — does **not** travel via bundles.

## Common questions

**Why is saving my Subject Type failing?**
You probably have one or more incomplete Attendance Types. Open the Attendance sub-block, check the warning banner under the types list, edit each named type to fill its Session Outcome Reason and Absence Reason concepts, then save.

**Can I change an Attendance Type's follow-up encounter type later?**
Yes. Prior auto-created encounters keep their original type (the link is preserved). New sessions saved after the change use the new type.

**What if a class doesn't meet on a particular day?**
Two options. If the day is region-wide off (holiday or weekly off): mark a date marker on the calendar — see [Calendars](doc:calendars). If the day is off only for this one school (teacher absent, equipment failure): the field worker marks the session as **Didn't Happen** with a reason from the Session Outcome Reason concept. There is no per-group "meeting days" override.

**Can attendance be marked on holidays?**
Yes, via the **Mark anyway** affordance on the field worker's dashboard. A reason is required to capture why the override was made.

**Can different attendance types share the same reason concept?**
Yes. The two reason fields on each Attendance Type are independent — you can pick the same coded concept for multiple types, or pick different ones for each.

**Does auto-share work on the webapp?**
No. Both the manual Share button and the auto-share-on-save toggle are Android-only.

## What's next

* [Calendars](doc:calendars) — configure the working pattern and public holidays that attendance reports use.
* [Concepts](doc:concepts) — create the coded concepts for Session Outcome Reason and Absence Reason.
* [Writing rules](doc:writing-rules) — author a custom share rule.
* [Access Control](doc:access-control) — grant `ManageCalendars` to user groups responsible for calendar maintenance.
