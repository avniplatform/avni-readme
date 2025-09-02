---
title: Form Mapping
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
**What does form mapping do?**
Forms are an important part of Avni. Before trying to understand forms it is required to understand what are key entities which hold the transaction data in Avni.
  * Individual (or Subject)
  * Encounter
  * Program Enrolment
  * Program Encounter
  * Program Exit

Each of these entities has some core data and configurable data. Configurable or dynamic data is modelled using forms.
  * **Individual** has one form (for Registration) for each subject type.
  * **Encounter** can be of multiple encounter types and for each encounter type, there is one form.
  * An individual can be enrolled in multiple programs hence for each **Program Enrolment** there will be a form. Corresponding to each enrolment there is usually exit data which is captured via forms.
  * An individual can be registered in different programs, and within each program, there can be multiple types of **Program Encounters** (hence forms).

Form Mapping tries to model this relationship between Programs, ProgramEncounters or NonProgram Encounters and their Forms. Form Mapping has the following key fields. In the column header for Program and Encounter Type, we have the current name for the database columns and domain class fields in the brackets which will be renamed to more understandable names, soon.

This is depicted in tabular form below (with an added Form Type dimension). 
[block:parameters]
{
  "data": {
    "h-0": "Form Type",
    "h-1": "entity_id",
    "h-2": "observations_type_entity_id",
    "h-3": "subject_type_id",
    "0-0": "Individual",
    "0-1": "null",
    "0-2": "null",
    "0-3": "subject_type.id",
    "1-0": "Encounter",
    "1-1": "null",
    "1-2": "encounter_type.id",
    "1-3": "subject_type.id",
    "2-0": "Program Enrolment",
    "2-1": "program.id",
    "2-2": "null",
    "3-0": "Program Encounter",
    "3-1": "program.id",
    "3-2": "encounter_type.id",
    "4-0": "Program Exit",
    "4-1": "program.id",
    "4-2": "null",
    "2-3": "subject_type.id",
    "3-3": "subject_type.id",
    "4-3": "subject_type.id"
  },
  "cols": 4,
  "rows": 5
}
[/block]