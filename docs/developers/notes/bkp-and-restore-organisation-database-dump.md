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

## Restoration

### Available Restoration Commands

* **`restore-org-dump`** - For organization databases (full restoration with schema fixes)
* **`restore-dump-only`** - Direct SQL file restoration (simple import)
* **`restore-staging-dump`** - For staging environment restoration

### restore-org-dump Process

The `restore-org-dump` command performs a complete restoration process:

1. **Schema Fixes**: Automatically fixes schema references in the dump file
   * Converts `from form` to `from public.form`
   * Converts `inner join form` to `inner join public.form`

2. **Database Preparation**:
   * Cleans and rebuilds the `avni_org` database
   * Creates the implementation database user with proper permissions

3. **Data Import**: Restores the data from the specified dump file

**Usage**:

```bash
make restore-org-dump dumpFile=<path_to_dump_file> dbRole=<database_role>
```

### Backup Contents

The dump file contains three types of data:

1. **Source Data** - Core application data
2. **ETL Metadata** - Analytics and reporting metadata
3. **ETL Derived Data** - Data that can be regenerated from source data

### Running ETL Service

After restoration, ensure your ETL service is running properly:

1. Start/restart the ETL service
2. Enable and disable analytics database for the organization to retrigger the ETL process
3. Monitor the ETL process for completion

## Important Caveats

### catchment_address Table Issue

The `catchment_address` table has specific considerations:

* **No Row-Level Security**: This many-to-many table lacks row-level security mapping
* **Mixed Data**: Likely contains data not relevant to your organization
* **Manual Cleanup**: Non-relevant data should be manually deleted

**Cleanup Process**:

```sql
-- Delete non-relevant catchment_address data
-- (pseudo code - adapt based on your specific requirements)
DELETE FROM catchment_address 
WHERE catchment_id NOT IN (SELECT id FROM catchment WHERE organisation_id = <your_org_id>)
   OR addresslevel_id NOT IN (SELECT id FROM address_level WHERE organisation_id = <your_org_id>);
```

**Constraint Re-application**:
After cleanup, ensure foreign key constraints are properly applied:

```sql
-- Verify constraints are in place
-- pseudo code
ALTER TABLE catchment_address 
ADD CONSTRAINT fk_catchment_address_catchment 
FOREIGN KEY (catchment_id) REFERENCES catchment(id);

ALTER TABLE catchment_address 
ADD CONSTRAINT fk_catchment_address_address_level 
FOREIGN KEY (addresslevel_id) REFERENCES address_level(id);
```

### Additional Considerations

* **Data Validation**: Verify data integrity after restoration
* **Performance**: Large dumps may require significant restoration time
* **Space**: Ensure adequate disk space for restoration process
* **Permissions**: Confirm database user has necessary permissions

## Troubleshooting

* Ensure the tunnel is established before running remote backups
* Verify the database role exists and is accessible
* Check disk space in the backup directory
* Ensure proper permissions for the backup location
* For restoration issues, check that the dump file path is correct and accessible
