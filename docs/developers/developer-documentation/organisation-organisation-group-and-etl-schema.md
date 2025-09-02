---
title: Database schema and users to support multi-tenancy and ETL reporting
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
In Avni, there is one database called openchs. Within this database, there are several schemas and database users created to support multiple implementations and reporting for them.

### public schema

This has all the core tables and source data for all the organizations/implementations. It also has the ETL metadata and supporting tables and their data.

### organization database users

Each organization has its own database user on which the row-level multitenancy rules are applied, hence restricting their access to their own data.

### organization-specific schema

If ETL reporting is enabled, then such an organization gets its own database schema. In this schema, the tables specific to each form are created - for easy and fast reporting. The above database user is provided access to this schema.

### organization group user and schema

Avni supports the concept of an organization group. An organization group has its own database user. This database user is provided the same access as the organization database user. For reporting a schema can also be created. This schema will hold the ETLed data for all the organizations that it has in its group.