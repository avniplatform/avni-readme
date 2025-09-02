---
title: Avni Bahmni Integration
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
Since Avni Bahmni integration is reusable integration, hence this page. Some Avni integration modules are specific to a project, hence they will not be documented here.

### Conceptual Background 

The purpose Avni Bahmni integration is to make the data of Avni available to the hospital users and the Bahmni data available to the field workers using Avni. The integration allows control over which data is made available on the other side. Once the data is available the existing mechanisms present in the both the system can be used to view this data.

The integration respects the concept of immutability of health data - as is present in both Avni and Bahmni. Immutability implies that most patient is not updated over time (the concept of Encounter) and new data is created instead. The integration hence is implemented such that Avni data will not update Bahmni data and vice-versa.

The objective of the integration is not to create work flows across the two systems. e.g. if a person is registered in Bahmni then automatically register the person in Avni. OR, if such an such form is filled in Bahmni then automatically enrol them in Avni is some program. Apart from immutability the reason this has not been done is to:

- Get into conflict resolution of data across two systems
- Keep field program and hospitals decoupled from each other. The field worker and hospital users both can be make use the data but what to do is not dictated by the integration.

### Technical Background

When you start the server, a few metadata will be created by the flyway migration. These are present in mapping_group and mapping_type tables. You will be creating mapping entities using these. Note that you cannot change the name of these (as they are also enums based on which the integration is done). So for this module the Mapping Group and Mapping Type screen in admin app should be treated as read only.

There are four types of conceptual entities - Individual, Program Enrolment/Exit, Program Encounter, and General Encounter across Avni and Bahmni. In OpenMRS - Program Enrolment, Program Encounter and General Encounter are not natively supported. Bahmni provides support for this using convention.

The integrator works over OpenMRS API and not Bahmni. Due to this _the mapping_ between Bahmni and Avni should provide the information about whether an Encounter in Bahmni maps to Program Enrolment, Program Encounter, or General Encounter. (Mapping tab in the web application can be used for finding and creating these mappings.)

# Mapping and MetaData Configuration

**General info about all type of mapping.**

- The mapping should be created via the admin app UI.
- In the instructions below it has been mentioned to create form, encounter types, program etc in Avni or Bahmni - but if you already have them then you can skip those steps.
- There are no separate instruction for addition of new form elements to existing forms
- For concepts, note that you also need to map the answer concepts
- Avni external API uses name, for MetaData, to identify and OpenMRS uses UUID - hence in most places you will be using name for Avni and UUID for OpenMRS when providing mapping etc.

### Constants

Currently there is no user interface in admin app for this - hence these need to be set directly in the database. Following are the name of constant with description of what should be the value.

- **BahmniIdentifierPrefix** - If the patient identifier in Bahmni has any prefix, else this should be empty string.
- **IntegrationBahmniIdentifierType** - Identifier type for above
- **IntegrationBahmniProvider** - The integration doesn't pass the user information from one system to another - hence this constant provides the name of the provider as present in OpenMRS.  Ideally you should create a new provider in OpenMRS for this.
- **IntegrationBahmniEncounterRole** - OpenMRS expects a role name provided along with the provider.
- **IntegrationBahmniLocation** - The UUID of the location in OpenMRS where the visit, encounter, identifiers should be created in.  Ideally you should create a new location in OpenMRS for this.
- **IntegrationBahmniVisitType** - Since all encounters are recorded in OpenMRS under some visit, this is the UUID of the visit type. Ideally you should create a new visit type in OpenMRS for this.
- **OutpatientVisitTypes** - If you are integrating lab results and prescriptions, then this is visit type UUIDs where they can be found and picked from. Since the lab tests are prescriptions can be a lot for in-patient visits - this allows for controlling what doesn't get synced to Avni. Multiple visit type uuids can be provided as multiple rows with the same key.
- **IntegrationAvniSubjectType** - The Avni subject type name that maps to Patient.

### Core mappings

Create following mappings and corresponding meta data in relevant system.

| Mapping Group | Mapping Type                     | Bahmni Value                                                                                                                | Avni Value                                                  |
| :------------ | :------------------------------- | :-------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------- |
| Common        | BahmniUUID_Concept               |                                                                                                                             | Name of the concept that will hold the OpenMRS entity uuids |
| Common        | AvniUUID_Concept                 | UUID of the concept of data type string that holds the Avni entity uuids                                                    |                                                             |
| Common        | AvniEventDate_Concept            | UUID of the concept that holds event date (like encounter date, registration date etc                                       |                                                             |
| Common        | AvniProgramData_Concept          | UUID of the concept of data type text  that holds program enrolment and exit information. a string formatted text is stored |                                                             |
| Common        | AvniEventDate_VisitAttributeType | UUID of the visit attribute type of type date, where the Avni event date (as described earlier) is stored                   |                                                             |
| Common        | AvniUUID_VisitAttributeType      | UUID of the visit attribute type of type string, where the Avni entity UUID (as described earlier) is stored                |                                                             |

_\*\* A new visit is created for each program enrolment and subject general encounters._

### Map of Concepts

For reference from other points below.

(for each concept in the form) Mapping Group=Observation, Mapping Type=Concept, Avni value=Name of the concept, Bahmni value=UUID of the concept, Select coded if it is a coded concept.

### Map Subject/Patient Identifier

Mapping Group=PatientSubject; Mapping Type=PatientIdentifier_Concept; Avni value=Name of concept; Bahmni value=UUID of patient identifier type

### Map Avni Subject to Bahmni

Avni Subject maps to Bahmni encounters.  
Please note that the integrator also creates a patient in Bahmni with name, date of birth, gender and patient identifier. Other than patient identifier the other fields are mapped from Subject core fields, hence without any mapping required.

- Create a form in Bahmni (using the concept set approach)
- Create an encounter type in Bahmni
- Mapping Group=PatientSubject, Mapping Type=Subject_EncounterType, Avni value=Name of subject type, Bahmni value=UUID of encounter type
- Map concepts in the form

### Map Patient to Avni

Patient maps to Avni general encounter.

- Create an encounter type by the name `Bahmni Patient Registration`, and a form in Avni for the Subject type corresponding to the patient.
- MappingGroup=PatientSubject; MappingType=PersonAttributeConcept; Avni value=Concept name (from the form); Bahmni Value=Person attribute type UUID.

### Map Avni Program Enrolment form

- Create a form in Bahmni (using the concept set approach)
- Create an encounter type in Bahmni

| Mapping Group    | Mapping Type                         | Bahmni Value           | Avni Value   |
| :--------------- | :----------------------------------- | :--------------------- | :----------- |
| ProgramEnrolment | CommunityEnrolment_EncounterType     | UUID of encounter type | Program name |
| ProgramEnrolment | CommunityEnrolment_BahmniForm        | UUID of form concept   | Program name |
| ProgramEnrolment | CommunityEnrolment_VisitType         | Visit type uuid        | Program name |
| ProgramEnrolment | CommunityEnrolmentExit_EncounterType | UUID of encounter type | Program name |
| ProgramEnrolment | CommunityEnrolmentExit_BahmniForm    | UUID of form concept   | Program name |

- map concepts

### Map Avni's program encounter form (or Bahmni encounter to Avni Program Encounter)

- Create a form in Bahmni (using the concept set approach) and Avni
- Create an encounter type in Bahmni and Avni

| Mapping Group    | Mapping Type                     | Bahmni Value           | Avni Value          |
| :--------------- | :------------------------------- | :--------------------- | :------------------ |
| ProgramEncounter | CommunityEncounter_EncounterType | UUID of encounter type | Encounter type name |
| ProgramEncounter | CommunityEncounter_BahmniForm    | UUID of form concept   | Encounter type name |

- map concepts

### Map Avni's subject encounter form (or Bahmni encounter to Avni subject encounter)

- Create a form in Bahmni (using the concept set approach) and Avni
- Create an encounter type in Bahmni and Avni

| Mapping Group    | Mapping Type                     | Bahmni Value           | Avni Value          |
| :--------------- | :------------------------------- | :--------------------- | :------------------ |
| GeneralEncounter | CommunityEncounter_EncounterType | UUID of encounter type | Encounter type name |
| GeneralEncounter | CommunityEncounter_BahmniForm    | UUID of form concept   | Encounter type name |

- map concepts

### Map Drug Prescriptions

| Mapping Group    | Mapping Type           | Bahmni Value | Avni Value                |
| :--------------- | :--------------------- | :----------- | :------------------------ |
| GeneralEncounter | DrugOrderEncounterType |              | Encounter type name       |
| GeneralEncounter | DrugOrderConcept       |              | Concept name of type text |

### Map Lab Results

| Mapping Group    | Mapping Type     | Bahmni Value | Avni Value          |
| :--------------- | :--------------- | :----------- | :------------------ |
| GeneralEncounter | LabEncounterType |              | Encounter type name |

all lab order concepts must be created in Avni and mapped.

## Atom feeds

Avni Bahmni integration module uses atom feeds provided by Bahmni. You can see atom feed client related tables in the database. The failed events are not used as the integration stops at the first failed event. The error handling is managed by the integration module and not the atom feed library.