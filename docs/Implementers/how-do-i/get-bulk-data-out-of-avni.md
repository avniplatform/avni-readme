---
title: Get bulk data out of Avni
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
## Transaction data

*i.e. Subjects, Encounters, Enrolments etc.*

There are a few options available suited for different purpose.

### 1. Longitudinal Export

Use this if the purpose is to get all the data associated to a subject in a single row. Also see - [New Longitudinal export](doc:new-longitudinal-export). Note currently there is a limit of 10,000 rows in this export. One can use date ranges to get data in parts.

### 2. Download Metabase tables

Metabase automatically recognises all the tables in the data source that it is pointed to. Hence, on browsing to the implementation specific schema one can see all the ETL tables. Metabase allows the ability to download the data for each table. A few points to consider/know:

a. Although in the display Metabase has limit of 10,000 rows. There is no such limit on downloads.

b. These downloads are per-table with foreign keys to parent tables (e.g. encounter form tables will have foreign keys for program enrolment, subject ETL tables). The consumer of this data will have to join these themselves in their analytics solution.

c. Download operations currently are not metered. This may change in future, if we see performance impact on the reporting database. It is recommended that these downloads are done after hours, lets say after 6 pm - so that it doesn't impact other reporting operations.

### 3. Download custom query data (SuperSet and Metabase)

This can be used to provide custom downloads based on queries. This can get around any limitations of approach 2 above in terms of the shape of downloadable data. e.g. One can join Subject, Enrolment in encounter and provide a report that one can use to download subject, enrolment, identification data along with encounter data.

The point 2(c) applies to this as well.
