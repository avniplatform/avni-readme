---
title: Introduction to excel based import [Deprecated]
excerpt: >-
  Supported entities: Individual (aka Subject Registration) | Encounter (aka
  Subject Encounter) | ProgramEnrolment | ProgramEncounter | Checklist (aka
  Vaccinations) | Relationship (aka IndividualRelationship, SubjectRelationship)
deprecated: true
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: importing-excel-data
      title: Importing Excel data
---
> ❗️ Avni does not support Excel based import any longer, please refer to Admin App based approach to upload data [Bulk Data Upload page](https://avni.readme.io/docs/upload-data#is-the-order-of-values-important)

<br />

We can Import transactional data from excel files. Data can be Subject Registration, Enrolment, Encounters, relationships between Subjects, Vaccinations, etc. The data file, ideally, should have columns like RegistrationDate, FirstName, LastName, DOB, .. in case of Registration, and SubjectUUID, DateOfEnrolment, Program, .. in case of Enrolment, and SubjectUUID, EnrolmentUUID, EncounterType, Name, .. for Encounters. Along with these default fields, all the observations specific to the implementation should be present in the data file.

The definition of those forms cannot be imported this way. Only the data recorded against those forms can be imported this way.

We need a metaData.xlsx file that would work as an adapter between the data.xlsx file and the avni system.  
The data.xlsx file will be provided by the org-admin which should have consistent and tabular data. The metaData.xlsx file defines the relationship between each column and its corresponding field in the avni system/implementation.

## Structure of metaData.xlsx file:

The following are the various spreadsheets within a metaData.xlsx file.

### Sheets

Sheets represent a logical sheet of data. A physical sheet of data can be mapped to multiple logical sheets of data.

[block:parameters]
{
  "data": {
    "h-0": "Column",
    "h-1": "Description",
    "0-0": "File Name",
    "0-1": "The data migration service is used by supplying the metadata excel file, a data excel file, and a fileName (since the server reads the data excel file via a stream it doesn't know the name of the file originally uploaded hence it needs to be explicitly provided).  \n  \nOnly the sheets which have the file name matching the fileName via the API would be imported.",
    "1-0": "User File Type",
    "1-1": "This is the unique name given to the file of specific types. There can be more than one physical file of the same type, in which case the user file type will be the same but file names will be different.",
    "2-0": "Sheet Name",
    "2-1": "This is the name of the actual sheet in the data file uploaded where the data should be read.",
    "3-0": "Entity Type, Program Name and Visit Type, Address",
    "3-1": "Core but optional data to be provided depending on the type of data being imported",
    "4-0": "Active",
    "4-1": "During data migration, it is possible that there are a lot of files and mapping metadata definition for those files may not be complete. Active flag (Yes or No) can be used to disable sheets that need not be considered for migration when uploaded.",
    "5-0": "Name of fields",
    "5-1": "One can add multiple columns after this such that it matches the name of a System Field and provides the default value for the entire virtual sheet."
  },
  "cols": 2,
  "rows": 6,
  "align": [
    "left",
    "left"
  ]
}
[/block]

#### Sample

| File Name                          | User File Type | Sheet Name | Entity Type      | Program Name | Visit Type | Active | Date of Birth Verified | SubjectTypeUUID                          | Registration Date | Enrolment Date |
| ---------------------------------- | -------------- | ---------- | ---------------- | ------------ | ---------- | ------ | ---------------------- | ---------------------------------------- | ----------------- | -------------- |
| master\_data\_district\_wise\.xlsx | Registration   | AhmedNagar | Individual       |              |            | No     |                        | 8a9b0ef8\-325b\-4f75\-8453\-daeaf59df29d | YYYY\-MM\-DD      |                |
| master\_data\_district\_wise\.xlsx | Enrolment      | AhmedNagar | ProgramEnrolment | GDGS 2019    |            | No     |                        |                                          |                   | YYYY\-MM\-DD   |

### Fields

The mapping for non-calculated fields

[block:parameters]
{
  "data": {
    "h-0": "Column",
    "h-1": "Description",
    "0-0": "User File Type",
    "0-1": "This is the same as in Sheets.",
    "1-0": "Form Type",
    "1-1": "[IndividualProfile, Encounter, ProgramEncounter, ProgramEnrolment, ProgramExit, ProgramEncounterCancellation, ChecklistItem, IndividualRelationship]",
    "2-0": "System Field",
    "2-1": "The concept name is specified in the form.  \nOr Default field (this can be seen in different importers, See below ).",
    "3-0": "User Field",
    "3-1": "Name of the column from data.xlsx file"
  },
  "cols": 2,
  "rows": 4,
  "align": [
    "left",
    "left"
  ]
}
[/block]

#### Default fields for each entity as of Dec 2019

| Subject Registration   | Encounter          | Enrolment       | ProgramEncounter | Checklist       | Relationship     |
| ---------------------- | ------------------ | --------------- | ---------------- | --------------- | ---------------- |
| First Name             | Individual UUID    | Enrolment UUID  | Enrolment UUID   | Enrolment UUID  | EnterDateTime    |
| Last Name              | UUID               | Individual UUID | UUID             | Base Date       | ExitDateTime     |
| Age                    | Visit Type         | Enrolment Date  | Visit Type       | Checklist Name  | IndividualAUUID  |
| Date of Birth          | Encounter DateTime | Address         | Visit Name       | Item Name       | IndividualBUUID  |
| Date of Birth Verified | User               | Exit Date       | Earliest Date    | Completion Date | RelationshipType |
| Gender                 | Voided             | User            | Actual Date      | User            | User             |
| Registration Date      |                    | Voided          | Max Date         | Voided          | Voided           |
| Address Level          |                    |                 | Address          |                 |                  |
| AddressUUID            |                    |                 | Cancel Date      |                 |                  |
| Individual UUID        |                    |                 | User             |                 |                  |
| Catchment UUID         |                    |                 | Voided           |                 |                  |
| SubjectTypeUUID        |                    |                 |                  |                 |                  |
| User                   |                    |                 |                  |                 |                  |
| Voided                 |                    |                 |                  |                 |                  |

Along with these, the implementation-specific observations are also to be mapped.

#### Sample

| User File Type | Form Type         | System Field                         | User Field                     |
| -------------- | ----------------- | ------------------------------------ | ------------------------------ |
| Registration   | IndividualProfile | Individual UUID                      | SiteUUID                       |
| Registration   | IndividualProfile | First Name                           | Site                           |
| Registration   | IndividualProfile | AddressUUID                          | VillageUUID                    |
| Registration   | IndividualProfile | Type of waterbody                    | Type                           |
| Registration   | IndividualProfile | Concerned Govt\. Dept\.              | Concerned Govt\. Dept\.        |
| Enrolment      | ProgramEnrolment  | Silt Estimation as per the work plan | Estimated quantity of Silt cum |
| Enrolment      | ProgramEnrolment  | Individual UUID                      | SiteUUID                       |
| Enrolment      | ProgramEnrolment  | Enrolment UUID                       | EnrolmentUUID                  |

[An example of Metadata.xlsx file](https://docs.google.com/spreadsheets/d/1M0QvcgZ7TagcHvMnTSo3qt-sZHwUDHEiN0T2hlKTn9Y/edit?usp=sharing)  
[An example of Data.xlsx file](https://docs.google.com/spreadsheets/d/19aCEIlODNvJMR68_mGl4Q-Kx6n3qI0Dk4hL0aQ8dwAo/edit?usp=sharing)

> 🚧 UUIDs in Data.xlsx file
> 
> Note that
> 
> - Individual UUID (aka Subject UUID, in this example called SiteUUID), EnrolmentUUID, or any `<Transactional-data UUID>` will have to be manually assigned by the developer before import.
>   - Use tools like uuid: `npm i -g uuid`.
>   - `for n in {1..100}; do uuidgen -r; done` `#to get 100 uuids from CLI`
> - AddressUUID (or Village UUID) will not be available when the data file is provided by the Implmentation. And has to be determined from the `Full Address details` (see example Data.xlsx).
>   - For this get all locations and it's uuid into a `Ref Sheet` in data.xlsx file
>   - do vlookup for uuid by `full address details`

### Google Drive Files

For uploading files (images/documents) you can put the URL of the file. Please follow the following steps:

- Ensure the drive file is shared without any restrictions
- Copy the file link and use this website to get the link that can be put into the excel file to be uploaded - [https://sites.google.com/site/gdocs2direct/?pli=1](https://sites.google.com/site/gdocs2direct/?pli=1)
- Copy the link generated by the above website for your file and put it in the excel/CSV cell.

**Technical link for Avni Team**

_The above website uses the following http request behind the scenes_

`curl 'https://www.google-analytics.com/g/collect?v=2&tid=G-KV5S9LK4WB&gtm=2oe1a1&_p=437198370&gdid=dZWRiYj&cid=1650660276.1673947139&ul=en-gb&sr=1440x900&uaa=x86&uab=64&uafvl=Not%253FA_Brand%3B8.0.0.0%7CChromium%3B108.0.5359.124%7CGoogle%2520Chrome%3B108.0.5359.124&uamb=0&uam=&uap=macOS&uapv=10.14.6&uaw=0&_s=1&sid=1673947138&sct=1&seg=1&dl=https%3A%2F%2Fsites.google.com%2Fsite%2Fgdocs2direct%2F%3Fpli%3D1&dr=https%3A%2F%2Fwww.google.com%2F&dt=Google%20Drive%20Direct%20Link%20Generator&en=page_view&_ee=1' 
  -X 'POST' 
  -H 'authority: www.google-analytics.com' 
  -H 'accept: _/_' 
  -H 'accept-language: en-GB,en;q=0.9,hi-IN;q=0.8,hi;q=0.7,en-US;q=0.6,de;q=0.5' 
  -H 'content-length: 0' 
  -H 'dnt: 1' 
  -H 'origin: https://sites.google.com' 
  -H 'referer: https://sites.google.com/' 
  -H 'sec-ch-ua: "Not?A_Brand";v="8", "Chromium";v="108", "Google Chrome";v="108"' 
  -H 'sec-ch-ua-mobile: ?0' 
  -H 'sec-ch-ua-platform: "macOS"' 
  -H 'sec-fetch-dest: empty' 
  -H 'sec-fetch-mode: no-cors' 
  -H 'sec-fetch-site: cross-site' 
  -H 'user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36' 
  --compressed`