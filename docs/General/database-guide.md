---
title: Database Guide
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
Avni uses PostgreSQL as the database. It particularly depends on a couple of features quite heavily - JSONB data type and [row-level security](https://www.postgresql.org/docs/9.5/ddl-rowsecurity.html). [JSONB](https://www.postgresql.org/docs/9.5/functions-json.html) allows for user-defined schema and row-level security for achieving multi-tenancy. If you planning to do Avni server-side, product development then reading up on these two concepts is highly recommended. But if you plan to use Avni to implement for your organisation then these two concepts are not necessary reading.

Avni's database schema documentation is available [here](https://dbdocs.io/petmongrels/Avni-with-description), but if you are here for the first time then do read the rest of the documentation here before browsing through the schema documentation. Please also note that since this schema definition is handwritten after generation some of the fields in the table may be absent. For full schema definition, but without description, you may see [this](https://dbdocs.io/petmongrels/Avni-database-schema-dump).

Avni database can be logically broken down into a smaller-cohesive group of tables - which for our purposes of understanding could be called **modules** (to reiterate, this is only for understanding purposes - Avni database doesn't have these modules in the database in any form). The functions of the tables and key-columns are provided in dbdocs.

**An explanation for a few columns which are repeated across the tables**

- organisation_id: This column indicates the organisation to which a row belongs (since Avni uses row-level multi-tenancy.
- is_voided or active: This is used to perform soft delete of data
- id: the primary key of a table
- uuid: identifier via which an externally integrating system can refer to records in Avni. id should not be used for this purpose.
- version: although this column is present, please ignore it because this column is not used as the functionality around it has not been developed fully.

# Foundational modules

## Framework

**audit**

## Organisation

Avni is multi-tenant with multiple organisation's data residing within the same schema - protected by row-level security. Avni also supports a group of organisations with one master organisation.

**organisation, organisation_group, organisation_group_organisation**

## Work area

For more about work-area please refer to [this](https://avni.readme.io/docs/avnis-domain-model-of-field-based-work#1--architecture-of-the-service-delivery-organisation).

**address_level_type, address_level, location_location_mapping, catchment, catchment_address_mapping**

## Master data tables

For few commonly required entities recognised, hence recognised by the platform.

**gender, individual_relation, individual_relationship_type, individual_relation_gender_mapping**

## User-defined data model

These tables allow the user of the platform (aka implementer) to define their data model. These tables could be further sub-grouped into form_, concept_ and _types tables. Concept tables describe your data independent of how it has been captured. Form_ tables describe how the data should be captured. \*Types tables allow you to define the high-level relationship between your data entities, as explained in section 2 and 4 [here](doc:avnis-domain-model-of-field-based-work).

**concept, concept_answer, subject_type, operational_subject_type, program, operational_program, encounter_type, group_role, operational_encounter_type, form, form_element_group, form_element, non_applicable_form_element, form_mapping**

<hr/>

# Transaction data

## Transaction data

**individual, program_enrolment, encounter, program_encounter, group_subject, individual_relationship**

<hr/>

# Functional modules

Note that here tables for transaction and meta/master data are grouped together.

## Identifiers

Identifiers have been documented [here](doc:creating-identifiers).

**identifier_assignment, identifier_source, identifier_user_assignment**

## Checklist

checklist, checklist_detail, checklist_item,  checklist_item_detail

<hr/>

# Application behaviour

## Rules

**deps_saved_ddl, rule, rule_dependency**

## User

**users, user_group, user_facility_mapping**

## Application settings

**organisation_config**

## Access Control

**privilege, group_privilege, groups**

<hr/>

# Others

## Translations

**platform_translation, translation**

## Account

**account, account_admin**

## Telemetry and logs

**rule_failure_log, rule_failure_telemetry, sync_telemetry, video_telemetric**

## Task

**Task, Task_Status, Task_Type, Task_Unassignment**

## Messaging

**Manual_Message, Message_Receiver, message_request_queue, message_rule, msg91_config, news**

## Documentation

documentation, documentation_item

## Comment

comment, comment_thread

## Offline/mobile dashboard

**dashboard, dashboard_card_mapping, dashboard_filter, dashboard_section, dashboard_section_card_mapping, standard_report_card_type, group_dashboard, report_card**

## ETL

**table_metadata, column_metadata, scheduled_job_run**

# Media Viewer - Database

- **download_jobs**
  - Contains information about the downloads done by the user