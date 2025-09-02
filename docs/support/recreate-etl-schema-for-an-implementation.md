---
title: Setup ETL schema for an implementation
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
## Enabling/Disabling

### Enabling Analytics via Admin view in Webapp

ETL enabling and disabling for an organisation has now been changed to function via APIs called from the webapp. The act of enabling analytics for an org via the webapp admin view also creates the required job and schedules it to run immediately and every hour from then on (on a best effort basis\*).

![](https://files.readme.io/2b4d900-image.png)

#### Ad hoc runs

If there is a need to run ETL for an organisation in an ad hoc fashion (to check if fixes to broken ETL for an org are working for example), you can disable and then enable analytics for that org. This will remove the previously existing job for the org and create a new one that will run immediately (within a minute on a best effort basis\*). 

Note: This does not recreate the ETL schema for the org. Follow the steps below to recreate the schema if required. 

##### Best effort basis

 Subject to available resources on the server and currently running jobs if any.

## Recreating ETL schema for an organisation

Clearing ETL schema and rows corresponding to it in the database. The next run of the ETL process will recreate the schema and database.

Steps: (please note if you are recreating for an organisation that uses reporting views then after recreation also generate the reporting views)

- Disable ETL for the org using the screen in the previous section in the webapp.
- ### For standalone organizations

```Text SQL Function
select delete_etl_metadata_for_schema('$impl_schema', '$impl_db_user', '$impl_db_owner');
```
```sql
set role "$impl_schema";  
drop schema if exists "$impl_schema" cascade;

delete from entity_sync_status where db_user = '$impl_db_user';
delete from entity_sync_status where schema_name = '$impl_schema';

delete from index_metadata where table_metadata_id 
	in (select id from table_metadata where schema_name = '$impl_schema');

delete from column_metadata where table_id 
	in (select id from table_metadata where schema_name = '$impl_schema');

delete from table_metadata  
		where schema_name = '$impl_schema';
```

- ### For organization groups

```Text SQL Function
select delete_etl_metadata_for_org('$impl_schema', '$impl_db_user');
```
```sql
set role openchs;  
drop schema if exists "$impl_schema" cascade;

delete from entity_sync_status where db_user = '$impl_db_user';
delete from entity_sync_status where schema_name = '$impl_schema';

delete from index_metadata where table_metadata_id 
	in (select id from table_metadata where schema_name = '$impl_schema');

delete from column_metadata where table_id 
	in (select id from table_metadata where schema_name = '$impl_schema');

delete from table_metadata where schema_name = '$impl_schema';
```

- Enable ETl for the org using the screen in the previous section in the webapp.

## Subject Type, Program and Encounter Type names

ETL service tries to automatically create the table names based on the subject type, program and encounter type names. Since these are also namespaced the names for these tables are as long as possible in postgres to support uniqueness. But there could still be clash in the names due to which the ETL process may fail to create tables. To ensure that this doesn't happen please ensure that the first six characters for the following are not the same. Please note that it is scoped, i.e. e.g. you can have same starting six characters for two encounter types under different programs.

- two encounter types within same program
- two encounter types within same subject type
- two programs inside the same subject type

### Trimming configuration on ETL table names

Refer to below link for current trimming configuration on ETL table name, when the original name exceeds length of 63 bytes(63 ASCII Code characters).

<https://github.com/avniproject/avni-etl/blob/main/src/main/java/org/avniproject/etl/repository/rowMappers/TableNameGenerator.java#L15>