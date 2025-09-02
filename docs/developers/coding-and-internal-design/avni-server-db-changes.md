---
title: Avni Server - Modifying the Backend Database
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
## Ensure access for newly created DB tables for all Organisation DB\_Users

In Avni Server, whenever we create a new DB Table using migrations, ensure that you include a `grant_all_on_table` command to make it accessible for all organisation db\_users.

```Text Command
SELECT grant_all_on_table(a.rolname, '<specify table name>') FROM pg_roles a WHERE pg_has_role('openchs', a.oid, 'member');
```
```Text Example
SELECT grant_all_on_table(a.rolname, 'organisation_status') FROM pg_roles a WHERE pg_has_role('openchs', a.oid, 'member');
```

## Whenever a new DB table is created, include it for cleanup during Deletion of Organisation Data

For a newly created DB table, figure out the bucket it should fall under and include the table in corresponding OrganisationService.delete\*\*\*() method. Also recreate the content, if its a basic admin config data for an organisation.

### Definition for classification of Avni DB tables into types

* **Transactional Data**: All DB tables that are used to store Individual(Subject) related details. Ex: Individual, Encounter, ProgramEnrolment, ProgEncounter, Checklist, UserToSubjectAssignment, etc..
* **Admin Config Data**: All data which is accessible from the "Admin" section of the Avni-webapp

<Image align="center" src="https://files.readme.io/3174f36-Screenshot_2024-08-14_at_12.14.14_PM.png" />

* **Metadata**: Rest of the DB tables **specific to an organisation**, that is **not** part of **either "Transactional"** or **"Admin Config"** Data
* **System config data**: Tables corresponding to
  * CRUD of SuperAdmins
  * CRUD of Organisation or OrganisationGroup 
  * Generic System data which is shared across Organisations. Ex: Privilege, ApprovalStatus, etc.

## Use Repeatable migrations for Creation of Views, Functions and Triggers

Always include psql Create/replace views, functions and triggers within respective Repeatable migrations file and not in any version migrations. This is due to the fact that 

* We would need to change the view based on any modifications to the tables its based on 
* And in test DB, we'll recreate the DB and apply creation of views at the end after all the versioned migrations.
