---
title: Calendars
excerpt: Configure working days, public holidays, and region-specific overrides so attendance reports distinguish expected absences from missed entries.
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
---
## Overview

A **Calendar** in Avni captures the working pattern of a region — which days of the week are working days — plus the public holidays and region-wide working-overrides that apply to subjects in that region.

The calendar is the foundation that the [Attendance](doc:attendance) feature builds on. When attendance is enabled on a Group Subject Type (for example, a school's `Class`), Avni uses the resolved calendar to decide which days are "expected sessions" — so reports can correctly distinguish "the form wasn't filled today" from "no class was expected today (it was a public holiday)".

If your organisation does not need attendance, you may not need to configure a calendar at all. If you are setting up attendance, configure the calendar first.

<!-- TODO: high-level diagram showing a calendar with marked dates feeding into the Attendance "expected sessions" view -->
<Image align="center" width="700px" src="" alt="Calendar feeds the attendance expected-sessions view" />

## Global vs. per-location calendars

An organisation can be in **exactly one** of two calendar modes at any given time:

* **Global mode** — one calendar applies to every subject in the organisation. Use this when the organisation operates entirely in one region with one set of public holidays.
* **Per-location mode** — multiple calendars, each attached to a specific address-level node. Each subject resolves to its applicable calendar by walking up its address-level chain (leaf to root) and picking the first calendar attached.

For example, an organisation operating in Karnataka and Tamil Nadu would use per-location mode — one calendar attached to Karnataka and one attached to Tamil Nadu — so that the public-holiday lists for each state can differ.

The two modes are **mutually exclusive within an organisation**. To switch modes, all existing calendars must first be deleted.

**Global mode** — one calendar applies org-wide; the single row carries a `GLOBAL` badge and a `DEFAULT` badge:

<!-- TODO: upload `list-global.png` from /Users/himeshr/Desktop/ — Calendars list page with a single global calendar named "Safal Global" carrying GLOBAL + DEFAULT badges -->
<Image align="center" width="800px" src="" alt="Calendars list in Global mode — one row, GLOBAL + DEFAULT badges" />

**Per-location mode** — one calendar per location node; each row's Scope column shows the address-level name:

<!-- TODO: upload `list-local.png` from /Users/himeshr/Desktop/ — Calendars list page with two per-location calendars (Muniguda, Harichandanpur), Scope column showing "Per-location" for each -->
<Image align="center" width="800px" src="" alt="Calendars list in Per-location mode — two rows for Muniguda and Harichandanpur" />

## Setting up your first calendar

### Step 1 — Open the Calendars page

Navigate to **App Designer → Calendars** in the left navigation. This menu entry is visible only to users in a group that has the `ManageCalendars` privilege (see the section on privileges below).

<!-- TODO: upload `republic-day-off-modal.png` from /Users/himeshr/Desktop/ — App Designer left navigation showing the Calendars entry (the marker popover in the same image is reused in the "Two ways to add a marker" section below) -->
<Image align="center" width="500px" src="" alt="App Designer left navigation showing the Calendars entry" />

### Step 2 — Add the calendar

Tap **+ ADD CALENDAR**. The Add Calendar modal opens with three fields:

* **Name** — a free-text name. For per-location calendars, use the location name (for example, "Karnataka").
* **Scope** — a radio between *Global* and *Per-location*. When you create your first calendar in an empty organisation, both options are available. After the first calendar is saved, the scope is **locked** to that mode for any subsequent calendars; switching modes requires deleting all existing calendars first.
* **Location** — an address-level picker, shown only when Scope is *Per-location*. Pick the address-level node that this calendar should apply to. Each location can hold at most one calendar.
* **Weekly working pattern** — a 7-row × 5-column grid of checkboxes. The rows are the days of the week (Mon to Sun); the columns are the occurrence within the month (1st, 2nd, 3rd, 4th, 5th). Tick a cell to mark that occurrence as a working day. Defaults to Mon–Fri all ticked (full Monday-to-Friday working week).

<!-- TODO: upload `calendar-monthly-pattern.png` from /Users/himeshr/Desktop/ — Edit Calendar modal for Muniguda (Per-location), Location = Muniguda (Block), Weekly working pattern grid with Mon–Fri all ticked across columns 1–5 and Sat columns 2 and 4 ticked (Sat 1/3/5 left unticked) -->
<Image align="center" width="700px" src="" alt="Edit Calendar modal — Muniguda per-location, weekly working-pattern grid with Mon–Fri all on and Sat columns 2 and 4 ticked" />

### Step 3 — Save

Tap **SAVE**. The new calendar appears in the list.

### Example — partial-Saturday working patterns

Many local block schedules don't follow a simple Mon–Fri or full-Mon–Sat pattern. The 7×5 grid lets you tick individual Saturday occurrences:

* **Muniguda** (shown in the screenshot above) works on **2nd and 4th Saturdays** — tick `Sat` columns 2 and 4; leave `Sat` columns 1, 3, 5 unticked.
* **Bengaluru schools** typically work on **1st, 3rd, and 5th Saturdays** — tick `Sat` columns 1, 3, 5; leave 2 and 4 unticked.

Adapt the same idea for any custom pattern your organisation needs.

## Marking holidays and working overrides

Once a calendar exists, you can mark individual dates as exceptions to the weekly pattern. There are two kinds of marker:

* **Off (public holiday)** — a date that is normally working per the pattern, but is *not* working in practice. Example: Republic Day (26 January) falls on a weekday but no schools open.
* **Working (override)** — a date that is normally *not* working per the pattern, but *is* working in practice. Example: a state-mandated make-up Saturday on a 2nd or 4th Saturday.

### Drill into a calendar's grid view

From the Calendars list, click the calendar's name to drill in. The grid view shows a fully-rendered 12-month calendar for the selected year.

* Plain cells are working days.
* Lightly-shaded cells are weekly off (per the working pattern).
* Red-dotted cells are public holidays (Off markers).
* Green-dotted cells are working overrides.

<!-- TODO: upload `calendar-global-show.png` from /Users/himeshr/Desktop/ — Drill-in grid view for "Safal Global" (GLOBAL + DEFAULT badges) for year 2026 showing the 12-month layout with a few green-dot working-override markers in May -->
<Image align="center" width="900px" src="" alt="Calendar grid view for Safal Global (2026) — 12-month layout with green-dot working-override markers visible in May" />

### Two ways to add a marker

**Click any date cell** in the grid. A small popover opens with an Off/Working radio and a Name field. The radio defaults to the right direction — clicking a working day defaults to *Off*; clicking a weekly-off day defaults to *Working*. Type a name (for example, "Republic Day") and save.

*Off variant* — clicking a working-day cell (e.g. 26 January) defaults the radio to **Off**:

<!-- TODO: upload `republic-day-off-modal.png` from /Users/himeshr/Desktop/ — Popover marker editor for 2026-01-26 with marker name "Republic day" typed and Off radio selected -->
<Image align="center" width="500px" src="" alt="Popover marker editor — Off variant, marking 26 January as Republic day" />

*Working variant* — clicking a weekly-off cell (e.g. a Sunday) defaults the radio to **Working**:

<!-- TODO: upload `calendar-local-show-with-working-sunday-modal.png` from /Users/himeshr/Desktop/ — Per-location calendar (Harichandanpur) grid view with the popover marker editor open on a Sunday (2026-08-16), marker name "Cleanlisness" typed and Working radio selected -->
<Image align="center" width="900px" src="" alt="Popover marker editor — Working variant, marking a Sunday as a working override" />

**Tap + ADD MARKER** in the page header. This opens a modal with a date picker, allowing you to mark a date in a different year without scrolling. The modal has a Date picker (defaults to today), an Off/Working radio (defaults to Off — most markers are public holidays), and a Name field. If the picked date is in a year other than the one currently displayed, the grid auto-navigates to that year after save.

### Editing or removing a marker

Click an already-marked cell. The popover shows the marker's name and type, with Edit and Remove actions.

## Switching modes

To switch between global and per-location calendars, all existing calendars must first be deleted. This is intentional — Avni resolves a subject's applicable calendar from its mode-dependent rules, and mixing modes within one organisation would make the resolution ambiguous. After deleting all calendars, the Scope radio in the Add Calendar modal becomes available again.

## Bundle transferability

Calendars and their date markers are transferred via Avni's existing app bundle export/import; voided calendars and markers propagate from UAT to prod so deletions carry over cleanly.

## Privilege

The Calendars page in App Designer and all its CRUD actions are gated by the `ManageCalendars` privilege. Grant this privilege to a User Group via the standard Access Control flow — see [Access Control](doc:access-control).

## What's next

Once you have configured at least one calendar (or are in global mode with a default calendar set up), you can enable attendance on a Group Subject Type. See [Attendance](doc:attendance).
