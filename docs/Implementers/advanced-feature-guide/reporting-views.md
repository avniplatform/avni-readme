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
Avni has a generic data model to support the dynamic creation of forms. For new implementers wanting to write custom reports, this can be overwhelming and complex.
To ease the creation of reports, Avni generates simplified database views with one view corresponding to each form.



[block:api-header]
{
  "title": "Creating / Refreshing Reporting Views"
}
[/block]
You can create views for reporting by going to the `Reporting Views` option in the app designer and clicking on `create/refresh view`. For each form, one view is created with all the questions as the columns in the view. 


[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/f47db05-Screen_Shot_2020-09-04_at_9.28.47_AM.png",
        "Screen Shot 2020-09-04 at 9.28.47 AM.png",
        3356,
        1666,
        "#eff4f9"
      ],
      "caption": "App Designer Reporting Views Screen"
    }
  ]
}
[/block]
You can see the view definition by clicking on the expand button, and delete the view by clicking on the delete button.

The views would be accessible in Metabase or any other reporting tool the implementation may be using.



[block:api-header]
{
  "title": "Naming convention"
}
[/block]
As PostgreSQL doesn't allow identifiers of length more than 63 bytes, we follow these naming conventions as long as the view name is below 63 characters.
[block:parameters]
{
  "data": {
    "h-0": "Form type",
    "h-1": "View name",
    "0-0": "Registration",
    "0-1": "`{UsernameSuffix}_{SubjectTypeName}`",
    "1-0": "Encounter",
    "1-1": "`{UsernameSuffix}_{SubjectTypeName}_{encounterTypeName}`",
    "2-0": "Encounter Cancellation",
    "2-1": "`{UsernameSuffix}_{SubjectTypeName}_{encounterTypeName}_cancel`",
    "3-0": "Program Enrolment",
    "3-1": "`{UsernameSuffix}_{SubjectTypeName}_{ProgramName}`",
    "4-0": "Program Exit",
    "4-1": "`{UsernameSuffix}_{SubjectTypeName}_{ProgramName}_exit`",
    "5-0": "Program Encounter",
    "6-0": "Program Encounter Cancellation",
    "5-1": "`{UsernameSuffix}_{SubjectTypeName}_{ProgramName}_{EncounterTypeName}`",
    "6-1": "`{UsernameSuffix}_{SubjectTypeName}_{ProgramName}_{EncounterTypeName}_cancel`"
  },
  "cols": 2,
  "rows": 7
}
[/block]
If the view name exceeds 63 characters we trim some parts from different entity type names to keep it below 63 characters. For trimming, we follow the below rule.

*{UsernameSuffix}_{First 6 characters of SubjectTypeName}_{First 6 characters of ProgramName}_{First 20 characters of EncounterTypeName}*

Some view names exceed the character limit even after the above optimisation. In such a case we take away the last few characters and replace them with the hashcode of the full name. Hashcode is used so that the name remains unique.