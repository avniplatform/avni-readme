---
title: Restore organisation database dump
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
This document assumes that Postgres server has been setup. It also doesn't list down all the basic steps that are common to any Postgres setup and only lists things that are specific to Avni.

### Before restoring the dump

```sql
create user openchs with password 'password' createrole

-- Extensions
create extension if not exists "uuid-ossp"
create extension if not exists "ltree"
create extension if not exists "hstore"

create role openchs_impl
grant openchs_impl to openchs
create role organisation_user createrole admin openchs_impl
```

### After restoring dump

Following should be run in the database created via restore. `$dbUser` should be provided person who provided the dump.

```sql
select create_db_user('$dbUser', 'password')
```

Note that the dump provided contains only the source data. The ETL data that can be derived from it is not present in the dump. This dump contains the ETL metadata as well.

### For running ETL service

Ensure your ETL service is running. Please enable and disable analytics database for the organisation, to recreate the ETL database.

## Caveats

* catchment\_address is a many-to-many table that doesn't have row-level security mapping. This table likely contains data that are not relevant for you. The non-relevant data can be deleted. After following two foreign constraints can be re-applied.
  * ```sql
    -- pseudo code
    catchment_id references catchment.id
    addresslevel_id references address_level.id
    ```
