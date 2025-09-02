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
**What does form mapping do?**\
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

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Form Type
      </th>

      <th>
        entity_id
      </th>

      <th>
        observations_type_entity_id
      </th>

      <th>
        subject_type_id
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        Individual
      </td>

      <td>
        null
      </td>

      <td>
        null
      </td>

      <td>
        subject\_type.id
      </td>
    </tr>

    <tr>
      <td>
        Encounter
      </td>

      <td>
        null
      </td>

      <td>
        encounter\_type.id
      </td>

      <td>
        subject\_type.id
      </td>
    </tr>

    <tr>
      <td>
        Program Enrolment
      </td>

      <td>
        program.id
      </td>

      <td>
        null
      </td>

      <td>
        subject\_type.id
      </td>
    </tr>

    <tr>
      <td>
        Program Encounter
      </td>

      <td>
        program.id
      </td>

      <td>
        encounter\_type.id
      </td>

      <td>
        subject\_type.id
      </td>
    </tr>

    <tr>
      <td>
        Program Exit
      </td>

      <td>
        program.id
      </td>

      <td>
        null
      </td>

      <td>
        subject\_type.id
      </td>
    </tr>
  </tbody>
</Table>
