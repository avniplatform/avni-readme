---
title: Backup and Restore organisation database dump
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

## Overview

The Avni server provides several make targets for creating database backups from different environments. This guide covers how to take organization data backups using the available make commands and then how to restore them.

## Prerequisites

1. **Database Tunnel**: Most backup operations require establishing an SSH tunnel to the remote database first
2. **Database Role**: You need to specify the appropriate database role for the organization
3. **Backup Directory**: Ensure `~/projects/avni/avni-db-dumps/` directory exists

## Database Tunnel Setup

Before taking backups, establish a tunnel to the appropriate environment:

```bash
# Production database
make tunnel-prod-db

# Staging database  
make tunnel-staging-db

# Prerelease database
make tunnel-prerelease-db
```

## Backup Commands

### Production Environment

```bash
# Standard backup (excludes large tables and system tables)
make dump-org-data-prod dbRole=<database_role>
```

### Staging Environment

```bash
# Standard backup
make dump-org-data-staging dbRole=<database_role>
```

### Prerelease Environment

```bash
# Standard backup
make dump-org-data-prerelease dbRole=<database_role>
```

### Local Database Backup

```bash
# Backup local organization database
make backup-org-db orgDbUser=<organization_db_user>
```

## Backup Details

### Standard Backup (`dump-org-data`)

**File Location**: `~/projects/avni/avni-db-dumps/<prefix>-<dbRole>.sql`

**Excluded Tables**:

* `audit` - Audit logs
* `public.sync_telemetry` - Sync telemetry data
* `rule_failure_log` - Rule failure logs
* `batch_*` - Batch job tables
* `scheduled_job_run` - Scheduled job runs
* `qrtz_*` - Quartz scheduler tables

## Usage Examples

```bash
# Example: Backup production data for organization role 'demo_org'
make tunnel-prod-db
make dump-org-data-prod dbRole=demo_org

# Example: Backup staging data with copy tables
make tunnel-staging-db  
make dump-org-data-staging dbRole=test_org

# Example: Backup local organization database
make backup-org-db orgDbUser=demo_user
```

## Output Files

Backup files are saved with the naming convention:

* **Remote backups**: `<prefix>-<dbRole>.sql` (e.g., `prod-demo_org.sql`)
* **Local backups**: `local-<orgDbUser>.sql` (e.g., `local-demo_user.sql`)

## Important Notes

1. **Tunnel Required**: Remote backups require an active SSH tunnel
2. **Database Role**: The `dbRole` parameter should match the organization's database schema/role
3. **Exclusions**: System tables and large log tables are automatically excluded to reduce backup size
4. **Security**: Backups are created with row security enabled
5. **Verbose Output**: Backup process shows verbose logging for monitoring progress

## Troubleshooting

* Ensure the tunnel is established before running remote backups
* Verify the database role exists and is accessible
* Check disk space in the backup directory
* Ensure proper permissions for the backup location

## Restoration

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

For restoring backups, see the restoration commands:

* `restore-org-dump` - For organization databases
* `restore-dump-only` - Direct SQL file restoration
* `restore-staging-dump` - For staging environment

### After restoring dump

Following should be run in the database created via restore. `$dbUser` should be provided person who provided the dump.

```sql
select create_db_user('$dbUser', 'password')
```

Note that the dump provided contains 

* the source data 
* the ETL metadata and
* the ETL data that can be derived from source data

### For running ETL service

Ensure your ETL service is running. Please enable and disable analytics database for the organisation, to retrigger the ETL process.

## Caveats

* catchment_address is a many-to-many table that doesn't have row-level security mapping. This table likely contains data that are not relevant for you. The non-relevant data can be deleted. After following two foreign constraints can be re-applied.
  * ```sql
    -- pseudo code
    catchment_id references catchment.id
    addresslevel_id references address_level.id
    ```
    <br />
