---
title: Integration process
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
In a given integration between one Avni implementation and another system, there may be several entity types that need to be processed in one or both directions. One reasonable and commonly followed way, so far in avni integrations, is to integrate by entity types. It may be also reasonable to integrate in one direction and then in another direction (they can be done in parallel also, but the errors may become difficult to reason and resolve). That is, first integrate all subjects, then encounters, and so on. Similarly, there may be entity types in the other integrating system as well.

These entities of each entity type may have a dependency on another entity type's entities. For example: in Avni an encounter is dependent on a subject being there first. Hence in any integration where subject, as well as encounters, are to be created in avni-based entities in the integrating system - this dependency becomes important to recognize. Due to this dependency if subject creation has failed then there is rarely any point in trying to create encounters. Hence the integration of one such cluster of entities can be called *an integration process* within an integration. This concept of the integration process is referred to in [Error handling](doc:error-handling).

### Bookmark

Each integration process (regular and error) manages a bookmark for the last record processed. This is to make sure than if the integration process stops then it can resume from the last processed location.

### Idempotent processes

The integration modules must use idempotent approach for record processing. i.e. if a record is processes multiple times then the state of the destination system is business-wise the same (e.g. the audit records can get updated everything with new time value).

### Avoid complex integration solutions

The integration solution typically may result in data flow from the mobile app, to the Avni database, to the integrated system - and back. The time taken for the data to reach from user of one system to another system cannot be guaranteed due to the offline nature of the mobile app.

Because of the above, Avni doesn't support [offline locks](https://www.baeldung.com/cs/offline-concurrency-control) and hence is also an event based system. Hence any entity must be updated by either Avni or by integration process pull data from another system. Both should not update the same record - otherwise they may update each other data. The entity mapping should take care of this by assigning ownership clearly.

The above rules out entity of any type moving in both directions. The other issue with same entity moving in both directions, in a bi-directional integration scenario, is figuring out when to stop. Reasoning about such workflows can be quite difficult when there are errors. When the bi-directional flow for any entity type is required, for some side use cases, use the synchronous API to make the change to another system directly instead of going via integration service for that (this is not possible or recommended from Avni side only for another system).
