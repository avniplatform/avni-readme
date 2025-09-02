---
title: ETL schema, reporting and management
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
The public datamodel is not suited for easy reporting because of a few reasons. 

1. jsonb fields 
   1. Cannot be indexed because GIN index and RLS do not work well with each other
   2. Cannot be easily explored because of the way it is setup
2. Analytic queries typically require full table scans, and reducing the data to just one organisation makes it easier
3. Address fields are hierarchical, and are not easy to handle for reports. Especially when we need grouping by different levels of address
4. Many times, pre-created rollups might help make reports easier

### The ETL service

The Avni ETL service fixes the above problems by moving data to a denormalized database that is suited for reporting. It creates tables of the form

* For all subject types, a table called \<subjectType\>
* For all general encounters, a table called \<subjectType\>\_\<encounterType\> and \<subjectType\>\_\<encounterType>\_cancel
* For all programs, a table called \<subjectType\>\_\<programName\> and \<subjectType\>\_\<programName\>\_exit
* For all program encounters, a table called \<subjectType\>\_\<programName\>\_\<encounterType\> and \<subjectType\>\_\<programName\>\_\<encounterType\>\_cancel
* An address table for all addresses
* A media table for all media
* For every Repeatable Question Group present for an entity(Subject, Encounter, ProgramEnrolment or ProgramEncounter), we'll have a separate secondary table called  \<parentTable\>\_\<question\_group\_concept\_name\>

#### Other details of the service

* Data is moved from the public schema to a schema defined by the schemaName of the organisation
* Data is moved incrementally every hour
* Analytics needs to be enabled for an organisation in order for it to work (from the Organisation edit screen of Admin)

### Management of ETL process for an organisation

ETL management for an organisation is only available for support users and not to the users of the organisation itself including administrators. You require super admin login to perform these activities.

* ETL can be enabled or disabled from the organisation edit screen.
* In organisation listing the enable disable status is displayed. One can also open an organisation to check the status. Note: that in listing the status is sometimes shown as blank. This is a defect but we have not been able to fix it. In such a case please check organisation show screen.
* If you want to run the ETL process immediately for an organisation (for which it is already enabled) - then you need to disable and enable. The rescheduling of the job will cause it to run after 10 seconds of enabling.

### Checking error of ETL job of an organisation

GET `{{origin}}/etl/job/{{orgUUID}}` with `auth-token` in header for super admin user.

e.g. [https://app.avniproject.org/etl/job/392bcc3e-0b04-495c-861a-64589d2692b4](https://app.avniproject.org/etl/job/392bcc3e-0b04-495c-861a-64589d2692b4)

You can find replace \\n\\t with newline to get a clearer stack trace.
