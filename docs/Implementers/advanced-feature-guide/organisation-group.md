---
title: How and when to use organisation group
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
If an organisation works with other sub-organisations performing same activity and if it wants each sub-organisation to be able to view/manage only their data - then one can check organisation group feature to solve for:

- Same app definition shared across partner organisations
- Each partner organisation get their own dashboard and reports without being able to see data from other partner organisations.
- Super organisation to be able to have a dashboard where they can view all sub-organisation data.

> 🚧 Should not be used when the number of partner organisations can grow a large number over time.

### Organisation and organisation groups

1. Mainline Organisation - for maintaining the trunk of source bundle
2. Release Organisation - for maintaining the released branch of source bundle
3. One production organisation for each partner organisation. One production organisation group.
4. Two UAT organisations. One organisation group consisting of these two organisations)
   1. Two organisations allow us to replicate the organisation group.

### Testing Deployment

Active development take places on mainline organisation.

### Release Deployment

1. The bundle from mainline organisation is released to release organisation and sanity testing can be done on this.
2. The bundle from release organisation is uploaded to each production organisation.

### Managing locations

There are two options.

1. There is one location set that is used by all partner organisations. These locations are to be imported in each organisation.
2. Each organisation has their own locations. In this case if the same logical location is used by multiple organisations, then Location should suffixed like Location1 (Org 1), Location2 (Org 1) when uploading. See the filters section below for the trade-off.

### Reporting

Metabase is not right tool for such setup and SuperSet should be used.

#### How to create separation between different partner organisations so that they do not see each other's data.

1. All reports should connect using the organisation group db_user to the database. ETL should be enabled for only organisation group.
2. Setup row level security and roles for each organisation (feature of Superset).
3. Assign correct role to the user when provisioning users from any partner organisation
4. For users who can see the reports for all organisations no role level security role is required.

### Filters

1. Filters like dates and hardcoded values there is nothing different to be done.

2. (Assumes row-level security works for filter queries as well) Filters that display drop downs like any concept's coded values, location types query with distinct clause should be used. Distinct clause is required for super organisation users, other wise they will see repeated values from each organisation.
   1. In query match against the concept, location type name and not ID or UUID.

3. (Assumes row-level security works for filter queries as well) Filters displaying location
   1. If approach 2 has been taken the users will be required to select all locations for same logical location.

## Access control

1. App Designer, Location Types - Recommended that these edit access is not provided for these to the customer.
2. Location - In Approach 1 (in Managing Locations), the access should not be given to the customer. In approach 2 one can do that with some training on naming if required.
3. User Groups - If access has to be given to customers then the tradeoff is in giving up on centralised source bundle management and it should excluded from bundles every time.

## Activities to consider when creating multiple organisations

1. Setup of organisations as described above
2. Release is more complex than for regular organisation
3. Each partner addition will require release activities like org setup, bundle upload, location setup and administrator training, row-level security / roles creation in superset, higher support required due to lack of access control that can be provided to the customers.