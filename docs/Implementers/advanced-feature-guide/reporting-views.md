---
title: Reporting Views [Deprecated]
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
Avni has a generic data model to support the dynamic creation of forms. For new implementers wanting to write custom reports, this can be overwhelming and complex.\
To ease the creation of reports, Avni generates simplified database views with one view corresponding to each form.

## Creating / Refreshing Reporting Views

You can create views for reporting by going to the `Reporting Views` option in the app designer and clicking on `create/refresh view`. For each form, one view is created with all the questions as the columns in the view. 

<Image title="Screen Shot 2020-09-04 at 9.28.47 AM.png" alt={3356} src="https://files.readme.io/f47db05-Screen_Shot_2020-09-04_at_9.28.47_AM.png">
  App Designer Reporting Views Screen
</Image>

You can see the view definition by clicking on the expand button, and delete the view by clicking on the delete button.

The views would be accessible in Metabase or any other reporting tool the implementation may be using.

## Naming convention

As PostgreSQL doesn't allow identifiers of length more than 63 bytes, we follow these naming conventions as long as the view name is below 63 characters.

<Table align={["left","left"]}>
  <thead>
    <tr>
      <th>
        Form type
      </th>

      <th>
        View name
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        Registration
      </td>

      <td>
        `{UsernameSuffix}_{SubjectTypeName}`
      </td>
    </tr>

    <tr>
      <td>
        Encounter
      </td>

      <td>
        `{UsernameSuffix}_{SubjectTypeName}_{encounterTypeName}`
      </td>
    </tr>

    <tr>
      <td>
        Encounter Cancellation
      </td>

      <td>
        `{UsernameSuffix}_{SubjectTypeName}_{encounterTypeName}_cancel`
      </td>
    </tr>

    <tr>
      <td>
        Program Enrolment
      </td>

      <td>
        `{UsernameSuffix}_{SubjectTypeName}_{ProgramName}`
      </td>
    </tr>

    <tr>
      <td>
        Program Exit
      </td>

      <td>
        `{UsernameSuffix}_{SubjectTypeName}_{ProgramName}_exit`
      </td>
    </tr>

    <tr>
      <td>
        Program Encounter
      </td>

      <td>
        `{UsernameSuffix}_{SubjectTypeName}_{ProgramName}_{EncounterTypeName}`
      </td>
    </tr>

    <tr>
      <td>
        Program Encounter Cancellation
      </td>

      <td>
        `{UsernameSuffix}_{SubjectTypeName}_{ProgramName}_{EncounterTypeName}_cancel`
      </td>
    </tr>
  </tbody>
</Table>

If the view name exceeds 63 characters we trim some parts from different entity type names to keep it below 63 characters. For trimming, we follow the below rule.

*\{UsernameSuffix}*\{First 6 characters of SubjectTypeName}*\{First 6 characters of ProgramName}\_\{First 20 characters of EncounterTypeName}*

Some view names exceed the character limit even after the above optimisation. In such a case we take away the last few characters and replace them with the hashcode of the full name. Hashcode is used so that the name remains unique.
