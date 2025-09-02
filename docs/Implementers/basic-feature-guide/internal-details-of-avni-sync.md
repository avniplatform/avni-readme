---
title: Sync internals
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
Synchronization (sync) of data from the Avni server to the client is a complex procedure. This document tries to explain it in detail. 

Note that this is an advanced topic that can be skipped for those who are not very familiar with Avni. 

### The primary assumptions related to data collection in Avni

A single User effects a change in data stored on the server for a particular Individual at any given point of time. After this all clients subscribing to that Individual's data are supposed to fetch the latest information from the server and only then perform any other actions related to that Individual. This is implemented through means of providing Idempotent POST Apis for all major entity types in Avni.

This is done, so that, there are no concurrent conflicting changes applied for the same Individual by different users, which would results in indeterminate state for the Individual's data.

### The different objectives of sync

During sync, the primary objective is to push all local changes to the server, and fetch all changes from the server to the device. However, there are a few other side-effects that take place during the sync. 

* #### Handling of change of permissions

When there is a change in the permissions of a user, new entities may need to be synced, or existing entities may need to be deleted from the device. This is handled on the fly during the sync. 

* #### Migration of beneficiaries

Sometimes beneficiaries are migrated from one catchment to another. To handle this, we might either have to retrieve all records of a beneficiary that moved in, or remove all records of a beneficiary that moved out. This is handled through a subject\_migration sync mechanism

**It is highly recommended that any correction to an Individual or its related entities "SyncConcept observations" be made using Individual (POST/PUT/PATCH) External API calls**, so that we perform a holistic update of the Individual and all its related entities( Enrolments, Encounters, Program-encounters, Checklists, EntityApprovalStatus, IndividualRelationships, etc..). We would also be creating the SubjectMigration entities, which are essential in removing the beneficiary records from Users who should no longer have the entity.

* #### Reset of sync

If the catchment of a beneficiary changes (either via change of a user's catchment, or a change in the catchment itself), the existing database becomes invalid and needs a complete resync to ensure the right beneficiaries are present. This is called a sync reset. 

There are partial resets that happen due to change of sync attributes of a particular subject type. This is also handled through smaller sync resets. 

* #### Fast sync

When a user is syncing for the first time, some implementations create an existing Realm database for that catchment that has been synced upto a certain point. The app downloads this pre-created database and syncs everything since that point. This is useful when there are multiple users sharing a catchment, or when a user wants to login from another device. 

### Sync specific data storage

Each app maintains its status of sync through three tables - EntityQueue (for push), EntitySyncStatus (for pull) and SyncTelemetry (for telemetry). 

* #### EntityQueue

There are actually 2 tables that are maintained in Avni for entities that have been either created or changed. These are 

* EntityQueue
* MediaQueue

Before syncing media, observations are stored with the name of the media file. During sync, it is assumed that internet is present. 

During this time, the sync service does the following

1. For each of the media queue items:
   1. It pushes the media to S3 and
   2. On Success, replaces the corresponding observation with the S3 url
   3. Otherwise, for failures, it creates a "Media '$\{Entity}' Error" entry in the EntitySyncStatus, to highlight issues during upload of Media content. These entries get cleaned up only when those exact Media content get synced successfully to the server. If in-case the media gets deleted from the device or did not exist even before the first sync attempt, then those "Media '$\{Entity}' Error" entries remain as is till User does a fresh sync 
2. Only if all Media are uploaded successfully, do the modified Avni entities (with observations having the S3 url) get pushed to the server

At the end of sync, a sync telemetry entries are pushed to the server. 

* #### EntitySyncStatus

The EntitySyncStatus table keeps a pointer of each kind of entity to be synced and the last time it was synced. This helps the sync process to start from where it left off last time. Rows will contain the entity type (Subject, Encounter etc), the specific entity type (Individual, Household, ANC etc. essentially the subject type, program or encounter type) and the last sync time. 

The server api provides a paginated service to pull from the last sync time. The table is updated on each page synced. 

* #### EntityMetadata

This is maintained in code, and provides the following for each kind of entity

* Name of the entity
* url to fetch the entity from the server
* Type of entity - entity can be divided into metadata and transactional data
* Order of sync - The sync is dependent on a specific order. Subjects need to be synced before program encounters etc. 

Sync uses EntityMetadata in conjunction with EntitySyncStatus (for pull) or EntityQueue (for push) to identify the exact order in which entities need to be synced. 

* #### Sync Telemetry

Sync Telemetry notes down the details of each sync - the kind of sync, start time, end time, number of entities synced, app details etc and pushes it to the server for analysis. 

### Detailed sync steps

The sync process can be broadly divided into two activities

1. Data Server sync
2. Media sync\
   In the Data Server sync, all data except media is synced to the server. This is a 2-way sync\
   In the Media sync, media observations are taken from the MediaQueue and uploaded. Observations are modified to match the new S3 urls. 

In our current sync process, if there is media, we do a media sync first and then a data server sync. If there is no media, then we do a single data server sync. 

Once the data server sync and media sync is complete, sync telemetry is uploaded to the server. 

#### Data Server Sync

Here are the steps for a data server sync

1. Retrieve /syncDetails. This sends the existing EntityMetadata to the server to identify all the entities that have changed since the last sync. Only relevant entities are then synced to the client. 
2. Upload all entities marked in the EntityQueue
3. Verify if a data reset is required. If it is, then perform necessary resets
4. Get all reference data
5. Update database to account for new privileges if any (privileges are part of reference data)
6. Get all transactional data
7. Perform any migrations necessary (migration data is part of transactional data. New subjects are downloaded in one-shot with all transactions of a subject retrieved using the /subject/$\{subjectUUID}/allEntities endpoint)
8. Download images linked to news broadcasts
9. Download extensions if any
10. Download icon images of subject types

#### Media server sync

Media content are taken from the MediaQueue and uploaded to S3. Once this is done, these observations are modified to have the S3 url as part of the observation instead of the local file name. The next data server sync syncs these new entities back to the server. 

### Automated sync

Since release 3.36, there is now an automated sync mechanism. With this, entities are synced automatically on a timed basis. This happens once every hour, only if a sync was not run within the last half an hour. Normally, this only includes uploading entities that have been changed on the client. If it has been more than 12 hours since we have had a full sync, then the app does a full sync instead. From release 4.0.0, automated sync can be disabled from user settings in both field app and  webapp.

### Sync from a server's perspective

#### Sync strategy

While the app does not worry too much about the data that is being downloaded, the server ensures that only the right data is being sent to the app. Each subject is synced to the app of a user based on a few conditions

1. If the catchment of the subject matches the catchment of the user
2. If the sync attributes on the registration form of the subject matches the sync attributes of a user
3. If the subject is assigned to the user

The exact sync strategy is defined in a subject type. 

You can read more about the different Sync strategies supported in Avni [here](https://avni.readme.io/docs/sync-strategies).

#### Other tidbits

All transactional data except entity approval status is synced based on catchment. From release 3.38, entity approval status is also synced based on catchment. All reference data except locations is completely synced to the app. Locations are filtered by catchment of the user. 

The /syncDetails call is used to ensure that only relevant entities are requested by the app for a sync. This greatly reduces the number of calls on unchanged entities (there could be about 100 different entities present for an implementation, and only about less than 10 change frequently). 

Only data that has been in the server for more than 10 seconds from the time of the start of sync is provided to the app. This is to prevent in-process transactions from being missed out, and ensures that partial transactions that happen during the sync do not cause any errors
