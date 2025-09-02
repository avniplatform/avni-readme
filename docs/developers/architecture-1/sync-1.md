---
title: Sync
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
## Download and Upload

Uploading data from mobile to server is quite straightforward. Mobile app maintains a realm database based queue as and when entities are changed. During upload process these are uploaded one by one. e.g. when a new individual is registered and then an encounter is performed on the individual - these will be two items in the queue with individual in front (because this is how user will save them).

Downloading entities is far more complex. The type dependency (i.e. Individual should be downloaded after Subject Type is downloaded) is handled in [mobile app code](https://github.com/avniproject/avni-models/blob/0975db9c7fb62849acc1f8887bebf85511ab364f/src/EntityMetaData.js#L426). The main complexity of sync download is partitioning of data described below.

## Strategies to create data partition

An organisation can have a lot of data. All the data may not be required by the field worker. Avni has multiple ways to functionally partition this data and then deliver only the partition(s) that the user should get.

1. Subject location (same applied to child entities)
2. Form types (i.e. subject types, programs, and encounter types)
3. Manual assignment of subjects (same applied to child entities)
4. Based on observations on subjects (same applied to child entities)

## Configuration of the partition

Logically 1,3,& 4 partition types are expressed via Subject Type configuration. One or more of these can be configured and they are ANDed to create narrower (or composite) partition. Form types (2) has been implemented as access control but it has the partitioning effect as far as sync is concerned. It has been implemented technically differently as well.

## Sync Process

Mobile clients downloads in two steps.

1. Get the list of entities types that needs to be synchronised given a timestamp. The timestamp is the last sync time for that entity.
2. Download each of these entity types.

#### Entity Type

These are functional (or implementation) entity types rather than model classes. Functional entity types are same as in code entity types for most entities except those which have sub entity type created by the implementation. e.g. multiple encounter type for which in code there is only one entity type encounter. This can be followed from [here](https://github.com/avniproject/avni-server/blob/436631d6c8ccb935110d498b20102b39ae07eb1c/avni-server-api/src/main/java/org/avni/server/service/SyncDetailsService.java#L42).

## Group Subject Synchronisation

Since group subject is a link between two subject types - hence the sync strategy for both these subject types should be as described below.

- The user should have view access to both the subject types. If group subject type access is present then the sync will expect that view access to member subject type is also present - without checking for it (for now).
- If one the subject types have location based sync strategy then the other should also have this.
- If one of the subject types has sync attributes based sync strategy then other should also have it. The implementation must also make sure that the possible observation values on both group and member subject matches.
- When the group subject is assigned then it implicitly assumed that child subject is also assigned. The other location and/sync attributes based sync strategy of child subject type should completely match the group subject type - if present (so that all entities sync correctly with the client).