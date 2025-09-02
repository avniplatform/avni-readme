---
title: New Longitudinal export
excerpt: Guide for New Longitudinal Export
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Introduction

The “New Longitudinal export” feature allows for an Implementation Admin user to extract data in Longitudinal format for a specific Subject Type. All invoked requests are listed at the bottom of the “New Longitudinal export” screen, which also includes Status information. The export requests are processed asynchronously in the backend and upon completion they are uploaded to cloud and are available for download in the same screen in-line with the request status details.

New longitudinal export fixes the following issues with old export.

- Inability to fetch data across different forms for the same subject. eg: Fetch data from two different encounter types on the same program
- Inability to fetch group/household information
- Inability to fetch only selected fields from different forms

### Limitations

- There is a limit of maximum of 10,000 Individuals data that could be exported at once, as part of a single Longitudinal export request

## Presupposition

In-order for an Implementation admin user to be able to successfully invoke a “New Longitudinal export” request, he / she would need to have the following:

- Basic understanding of JSON syntax
- Understanding of Avni Entity Types and their inter-relationships

## Preparation

Implementation Admin would need to come up with a list of UUIDs corresponding to Entity Types and Concepts whose data should be included in the exported file.

In-order to fetch this, the most accessible approach is the Avni Webapp. The concept uuid is shown in the address bar, where-as the Entity type (Individual) UUIDs are available on inspecting the network response, as shown in the screenshot below.

![Reference Screen-shot for fetching UUID for Subject type](https://files.readme.io/487a1fa-Screenshot_2023-05-30_at_6.43.42_PM.png)

For Avni Internal team members, they can connect to the DB and invoke appropriate SQL queries to fetcht the UUID information.

```Text SQL
#Query to fetch concept names and UUIDs
set role <org_db_user>;
select
    f.name  as FormName
    -- ,fm.entity_id
    -- ,fm.observations_type_entity_id
    -- ,fm.organisation_id
        ,
    feg.name,
    fe.name as "Form Element",
    c2.name as "Concept"
from form f
         inner join form_element_group feg on feg.form_id = f.id
         inner join form_element fe on fe.form_element_group_id = feg.id
         inner join concept c2 on fe.concept_id = c2.id
order by
    f.name
       , feg.display_order asc
       , fe.display_order asc;
```

## Request Payload Format

```sql JSON
{
  "individual": {
    "uuid": "<Specify Subject Type's UUID>",
    "fields": [
      "id",
      "uuid",
      "firstName",
      "registrationDate",
      "gender",
      "dateOfBirth"
    ],
    "filters": {
      "addressLevelIds": [],
      "date": {
        "from": "2020-01-12",
        "to": "2022-05-04"
      }
    },
    "encounters": [
      {
        "uuid": "<Specify Encounter type's UUID>",
        "fields": [
          "id",
          "encounterDateTime",
          "cancelDateTime",
          "uuid",
          "name",
          "voided",
          "<Specify Encounter's Concept UUID>"
        ],
        "filters": {
          "includeVoided": true,
          "date": {
            "from": "2020-01-12",
            "to": "2022-05-04"
          }
        }
      }
    ],
    "groups": [
      {
        "uuid": "<Specify Group Subject Type's UUID>",
        "fields": [
          "id",
          "uuid",
          "firstName"
        ],
        "encounters": [
          {
            "uuid": "<Specify Group Subject's Encounter Type UUID>",
            "fields": [
              "id"
            ]
          }
        ]
      }
    ],
    "programs": [
      {
        "uuid": "<Specify Program's UUID>",
        "fields": [
          "id",
          "uuid",
          "enrolmentDateTime"
        ],
        "encounters": [
          {
            "uuid": "<Specify Program Encounter's UUID>",
            "fields": [
              "id",
              "uuid",
              "name",
              "encounterDateTime",
              "cancelDateTime",
              "voided",
              "<Specify Program Encounter's Concept 1 UUID>",
              "<Specify Program Encounter's Concept 2 UUID>"
            ],
            "filters": {
              "includeVoided": true
            }
          }
        ]
      }
    ]
  },
  "timezone": "Asia/Calcutta"
}

```

## Description of elements that can be used to compose a Export request

```c \<ROOT> (The root JSON element)
- "individual" : "<Specify Subject Type request details>"
- "timezone" : "<Specify timezone to adhere while displaying date fields>"
```

```c "individual” (Request details of the Subject Type for which data has to be extracted)
- "uuid" : "<Specify Subject Type's UUID>"
- "fields" : "<Specify fields on subjects to be included in the export>"
- "filters" : "<Specify filters applicable on subjects to be included in the export>"
- "encounters" : "<Specify General Encounter Types request details>"
  -- "uuid" : "<Specify Encounter Type's UUID>"
  -- "fields" : "<Specify fields on Encounters to be included in the export>"
  -- "filters" : "<Specify filters applicable on Encounters to be included in the export>"
  -- "maxCount" : "<Specify maximum count of Encounters to be included in the export>"
- "groups" : "<Specify Group Subject request details>"
  -- "uuid" : "<Specify Group Subject’s UUID>"
  -- "fields" : "<Specify fields on Group Subject’s to be included in the export>"
  -- "filters" : "<Specify filters applicable on Group Subject to be included in the export>"
  -- "maxCount" : "<Specify maximum count of Group to be included in the export>"
  -- "encounters" : "<Specify Group Subject Encounter Type’s request details>"
- "programs" : "<Specify Program request details>"
  -- "uuid" : "<Specify Program's UUID>"
  -- "fields" : "<Specify fields on Program Enrolment’s to be included in the export>"
  -- "filters" : "<Specify filters applicable on Program Enrolments to be included in the export>"
  -- "maxCount" : "<Specify maximum count of Program Enrolment to be included in the export>"
  -- "encounters" : "<Specify Program Encounter Types request details>"
```

### Allowed list of Individual fields that could be included in the export file ("fields" within “individual” or "groups" element )​

```c Fields
"id"  
"uuid"  
"firstName"  
"middleName"  
"lastName"
"dateOfBirth"  
"registrationDate"  
"gender"  
"createdBy"  
"createdDateTime"  
"lastModifiedBy"  
"lastModifiedDateTime"  
"voided"  
"registrationDate"  
"registrationLocation"
"gender"  
"dateOfBirth"  
"concept_uuid" : "<Specify Individual’s Concept UUID>"
```

### Allowed list of filters that could be applied to an entity ( "filters" within any entity “individual”, “encounters”, “groups”, “programs”)

```c Filters
"addressLevelIds" : "<Specify Array of Address Level Ids>"  
"date" : "<Specify date range to filter data>"  
"includeVoided" : "<Specify whether voided fields should be included, Allowed values are a. true and b.false >"
```

### Allowed fields with-in "date" element nested inside other entities(Used to restrict the data fetch to have registrationDate or encounterDateTime within the range specified)

```c Date
"from" : Format => "yyyy-MM-dd" Ex: "2020-01-12" (Mandatory)  
"to" : Format => "yyyy-MM-dd" Ex: "2020-01-12" (Mandatory)
```

### Allowed list of Encounter fields that could be included in the export file  ("fields" within "encounters", "program encounters" and  "group subject encounters" element)

```c Fields
"id"  
"uuid"  
"name"  
"earliestVisitDateTime"  
"maxVisitDateTime" 
"encounterDateTime"  
"encounterLocation"
"cancelLocation"
"cancelDateTime"
"createdBy"  
"createdDateTime"  
"lastModifiedBy"  
"lastModifiedDateTime"  
"Voided"
"concept_uuid" : "<Specify Encounter’s Concept UUID>"
```

### Allowed list of enrolment fields that could be included in the export file ("fields" within "enrolment" element )

```c Fields
"id"  
"uuid"  
"name"  
"enrolmentDateTime"  
"programExitDateTime"
"enrolmentLocation"
"exitLocation"
"createdBy"  
"createdDateTime"  
"lastModifiedBy"  
"lastModifiedDateTime"  
"Voided"
"concept_uuid" : "<Specify Enrolment’s Concept UUID>"
```

## Sample Payload

```c JSON
{
   "individual": {
       "uuid": "d22027ff-e019-4d1c-9352-bd740efccc38",
       "fields": ["id", "uuid", "firstName", "registrationDate", "gender", "dateOfBirth"],
       "filters": {
           "addressLevelIds": [],
           "date": {
               "from": "2020-01-12",
               "to": "2022-05-04"
           }
       },
       "encounters": [
           {
               "uuid": "16a3be1b-18a1-45e9-bfc8-f7915898abef",
               "fields": ["id", "encounterDateTime", "cancelDateTime", "uuid", "name", "voided",
                               "1f51e7f7-6db0-41ea-a372-e7b553ccb857",
                               "a6a6d4c0-4339-4ef0-b152-6d1c23eaf7c2",
                               "a44678fd-ee6d-4dc5-b103-f5534eb0f338",
                               "ab095140-b090-4f59-98ac-89b6479df471"],
               "filters": {
                           "includeVoided": true,
                           "date": {
                               "from": "2020-01-12",
                               "to": "2022-05-04"
                           }
                       }
           }
       ],
       "groups": [
           {
               "uuid": "e524b328-c0ad-4232-9fcb-2cf8c126a2c6",
               "fields": ["id", "uuid", "firstName"],
               "encounters": [
                   {
                       "uuid": "0c823f64-b2ec-420b-9e28-5e953b66b6d1",
                       "fields": ["id"]
                   }
               ]
           }
       ],
       "programs": [
           {
               "uuid": "9d6cd285-fb85-48f0-badc-6f004b9024d8",
               "fields": ["id", "uuid", "enrolmentDateTime"],
               "encounters": [
                   {
                       "uuid": "b2f419dc-209a-4285-b74c-29d93f2a628e",
                       "fields": ["id", "uuid", "name","encounterDateTime", "cancelDateTime","voided",
                           "45f02196-217b-4772-8085-3d17c41244da",
                           "d1774f83-ee28-41b8-9cb8-309098ee0f16",
                           "82efa85a-46a9-4c75-8c53-c488b8c48c54",
                           "84a99b8c-f9bb-4436-9d83-d79a60a0b450",
                           "74745370-ee9e-4f58-b25e-57ebac69d75d",
                           "2da75202-7f70-4a76-a8eb-cd9b289cdf8a",
                           "d9f8ee0c-960f-43d7-9b02-aa2557a9aa10",
                           "3e092c91-8e32-42b1-ac26-045b846e3893",
                           "80d88c23-1e44-423a-96bf-5ddaf105042e",
                           "e9190320-3211-4d9f-a72c-288f42cf830c",
                           "1cae9bd0-0dba-4479-954a-2d569c58d711",
                           "ac4d5664-0b5f-467f-a3c9-c0e4c8c221b7",
                           "8f67d53a-07bf-4652-b7ad-f2f6ef6bdfa2",
                           "44a608f8-54d3-4a8b-96b8-7175c65e1d01",
                           "a9f45a38-99a7-4fd8-8e28-1291434eace0",
                           "dfdc75c1-5a47-4aae-887c-3ee9f050d75e",
                           "c78e883a-60de-4629-8d85-8e4512cd13d5",
                           "0fc3b733-0ee0-4554-b316-e5e29c1978d2",
                           "83f01615-04b1-4115-84a5-48e89c9aff54",
                           "5e4d8a9d-28a5-49ec-a4c9-cd9cfd4dd134",
                           "89bf3601-d8ab-4353-85a3-8070a959394e",
                           "8263f129-5851-4f9d-a909-818dacacd862",
                           "5592def2-fe5e-4234-9253-ca5fd0322e26"],
                       "filters": {
                           "includeVoided": true
                       }
                   }
               ]
           }
       ]
   },
   "timezone": "Asia/Calcutta"
}

```