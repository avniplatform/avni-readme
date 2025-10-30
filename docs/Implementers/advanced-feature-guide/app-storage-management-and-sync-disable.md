---
title: App Storage Configuration and Disable Sync
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
### Need

After an organisation has run Avni for a few years, the amount of data collected over time can be sizeable depending on the scale of the program, number of subjects registered, number of times they were visited etc. Depending on the program objectives and especially for organisations where catchment based division of data is not used or is not effective, all of this historical data may not be of use to a field user who has just joined the organisation and is starting to use Avni. This unnecessary data causes longer initial sync time, slower dashboard loads and increases the storage used by the Avni app on the user's device.

### Solution

In order to address this, implementers can now configure an SQL query which returns the subject ids of subjects which should be disabled from being synced when a sync from the android app is performed.

This configuration can be made via the 'App Designer -> [App Storage Config](https://app.avniproject.org/#/appdesigner/appStorageConfig)' menu. 
**Note: To configure this feature, the user must have the "Edit organisation configuration" permission.** 
This query is validated to ensure it returns a single numeric column (the subject id) as output.

The query configured via this screen is picked up by a job that runs on a daily basis (configured to run at 2AM IST) which finds subjects based on this query and disables **subsequent sync to android app on user devices** for these subjects and the related entities for these subjects (visits, program visits, entity approval status, subject migration, user subject assignments, relationships, groups, checklists, comments, subject program eligibility etc).

### Example

To disable sync for subjects that were created more than 2 years ago, the query would look like:

`select i.id from public.individual i where i.created_date_time < now() - interval '2 years';`

Remember that this job runs every night so it will keep disabling sync for records that match the criteria on a continuing basis if the condition specified in the query is relative.

### Notes

1. If the subjects (and related entities) are already present on the user device (from a previous sync or via fast sync etc), they are not deleted from the device and the user will be able to view and update them. Updates made to such subjects on the android app will be updated on the server when synced. Other users with access to these subjects will however, not receive these updated records on the android app when they sync.
2. Dashboards on the android app may differ between users having the same sync settings depending on when the respective users synced.
3. DEA users will continue to see these records and will be able to update them and see the updates.
4. No impact to existing reports and exports (sync disabled records will be included).
5. There are constraints in place to prevent the sync disabled value for related entities from becoming different from the sync disabled field for the subject. In order to prevent sync errors, `sync_disabled` should never be updated via SQL query for any entity.
6. Writing a query that looks up tables from the ETL org-specific schema might not have the intended result as the ETL schema is not guaranteed to be in sync with the public schema when the job executes.
