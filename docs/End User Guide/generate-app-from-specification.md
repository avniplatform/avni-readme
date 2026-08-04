---
title: Generate app from specification
deprecated: false
hidden: false
metadata:
  robots: index
---
Now, you can turn Avni modelling([sample sheet](https://docs.google.com/spreadsheets/d/1HbfYMV9RgFHkYjvpNSZ0tsz9swd51RZzc8MFeS-r2No/edit?usp=sharing)) and scoping([sample sheet](https://docs.google.com/spreadsheets/d/1xhdPMguUjNwKE8IndRbJwqjv9FjOvYkDx7bRyCNT2lM/edit?usp=sharing)) Excel documents into a ready-to-upload Avni bundle ZIP in a couple of minutes.

Sheets are parsed and issues like below are flagged to user:
- field names longer than 255 chars,
- duplicate field names within a form
- and duplicate coded concepts across forms with different options.
  Each proposed rename is shown to the user for confirmation before being applied.

Below rule generation are currently supported:
1. Form element rule
2. Decision rule
3. Visit schedule rule
4. Edit form rule
5. Validation rule

Via chat interface as well, above rule types can be generated.

Demo: [screen recording of how it works](https://drive.google.com/file/d/1eS7CKgKALokv4qZUwgn_V5xawQ4SRtHU/view?usp=sharing).

---
# Setup of scoping sheet for uploading in a deployed environment

Reference: [sample scoping sheet](https://docs.google.com/spreadsheets/d/1xhdPMguUjNwKE8IndRbJwqjv9FjOvYkDx7bRyCNT2lM/edit?usp=sharing).

### Form level rules

The scoping workbook needs to include a `Rules` (or `Form Rules`) tab. Tab format — one row per form, one column per rule kind. Cells are natural-language intent — no syntax required.
Any subset of the below supported columns may appear:

| Form name | Visit Schedule Rule | Validation Rule | Edit Form Rule | Decision Rule |
|---|---|---|---|---|
| `ANC Followup` | "schedule next visit 30 days later" | "weight must be between 30 and 120 kg" | | |
| `Adult Registration` | | "age must be between 18 and 60" | "only the user who created the record can edit" | "set Age Group to Adult when age ≥ 18" |
| `Pregnancy Exit` | "return empty" | | | |

### Field-level rules — columns on each form sheet

Field-level rules (`formElementRule`) don't use the `Rules` tab — they come from optional per-field columns on the form sheets themselves. Five behaviour columns are recognised (any alias works):

| Column aliases | What the generated rule does |
|---|---|
| `when to show`, `visibility`, `skip logic`, `condition` | show the field only when … |
| `when not to show`, `hide when` | hide the field when … |
| `default_value`, `default value`, `pre-fill` | pre-fill or compute the value |
| `validation`, `validate when`, `block save when` | raise a validation error when … |
| `option condition`, `show options when`, `filter options` | restrict the coded answer options |

Cells are natural language, same as the `Rules` tab. Common visibility patterns (e.g. an "Other — specify" field paired with a coded field's `Others` option) are auto-detected even without any column.
