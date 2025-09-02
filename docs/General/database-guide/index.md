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

* organisation\_id: This column indicates the organisation to which a row belongs (since Avni uses row-level multi-tenancy.
* is\_voided or active: This is used to perform soft delete of data
* id: the primary key of a table
* uuid: identifier via which an externally integrating system can refer to records in Avni. id should not be used for this purpose.
* version: although this column is present, please ignore it because this column is not used as the functionality around it has not been developed fully.

# Foundational modules

## Framework

**audit**

## Organisation

Avni is multi-tenant with multiple organisation's data residing within the same schema - protected by row-level security. Avni also supports a group of organisations with one master organisation.

**organisation, organisation\_group, organisation\_group\_organisation**

## Work area

For more about work-area please refer to [this](https://avni.readme.io/docs/avnis-domain-model-of-field-based-work#1--architecture-of-the-service-delivery-organisation).

**address\_level\_type, address\_level, location\_location\_mapping, catchment, catchment\_address\_mapping**

## Master data tables

For few commonly required entities recognised, hence recognised by the platform.

**gender, individual\_relation, individual\_relationship\_type, individual\_relation\_gender\_mapping**

## User-defined data model

These tables allow the user of the platform (aka implementer) to define their data model. These tables could be further sub-grouped into for&#x6D;*, concept* and *types tables. Concept tables describe your data independent of how it has been captured. Form* tables describe how the data should be captured. \*Types tables allow you to define the high-level relationship between your data entities, as explained in section 2 and 4 [here](doc:avnis-domain-model-of-field-based-work).

**concept, concept\_answer, subject\_type, operational\_subject\_type, program, operational\_program, encounter\_type, group\_role, operational\_encounter\_type, form, form\_element\_group, form\_element, non\_applicable\_form\_element, form\_mapping**

<hr/>

# Transaction data

## Transaction data

**individual, program\_enrolment, encounter, program\_encounter, group\_subject, individual\_relationship**

<hr/>

# Functional modules

Note that here tables for transaction and meta/master data are grouped together.

## Identifiers

Identifiers have been documented [here](doc:creating-identifiers).

**identifier\_assignment, identifier\_source, identifier\_user\_assignment**

## Checklist

checklist, checklist\_detail, checklist\_item,  checklist\_item\_detail

<hr/>

# Application behaviour

## Rules

**deps\_saved\_ddl, rule, rule\_dependency**

## User

**users, user\_group, user\_facility\_mapping**

## Application settings

**organisation\_config**

## Access Control

**privilege, group\_privilege, groups**

<hr/>

# Others

## Translations

**platform\_translation, translation**

## Account

**account, account\_admin**

## Telemetry and logs

**rule\_failure\_log, rule\_failure\_telemetry, sync\_telemetry, video\_telemetric**

## Task

**Task, Task\_Status, Task\_Type, Task\_Unassignment**

## Messaging

**Manual\_Message, Message\_Receiver, message\_request\_queue, message\_rule, msg91\_config, news**

## Documentation

documentation, documentation\_item

## Comment

comment, comment\_thread

## Offline/mobile dashboard

**dashboard, dashboard\_card\_mapping, dashboard\_filter, dashboard\_section, dashboard\_section\_card\_mapping, standard\_report\_card\_type, group\_dashboard, report\_card**

## ETL

**table\_metadata, column\_metadata, scheduled\_job\_run**

# Media Viewer - Database

* **download\_jobs**
  * Contains information about the downloads done by the user
