---
title: Avni Server Coding Guidelines
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
## Building blocks

Controller, Service, Entity, Value Object, Repository, Contract Object (request/response). These are pretty much as they have been described in [domain driven design](https://matfrs2.github.io/RS2/predavanja/literatura/Avram%20A,%20Marinescu%20F.%20-%20Domain%20Driven%20Design%20Quickly.pdf).

### Transaction Management

@Transaction attribute of spring should be used only on the controller and not on services. This is because for us each controller method makes the scope of one transaction.

For the background (spring batch jobs) jobs where the controller has not invoked the scope of the transaction should be discussed. A very large job should be broken up into smaller transactions and the job should be designed to be idempotent. These transactions can be controlled via a worker layer which is currently not formalized in the Avni server code.

### Entity Lifecycle

In Avni most entities are synchronised to the client, hence their lifecycle must be maintained by the server when it modifies it. The scenario where one needs to be careful about this is when an entity has child entities. Simplest way to manage child entities is to clear previous list in the parent and add the edited ones. This results in new set of child entities being created with new ID and previous ones are deleted. While from data standpoint there is no change - but this will create problem with avni-client local database. When the next sync happens the previous entities will remain and new ones will get added - to the mobile database.

Hence following needs to be done:

* The web app where editing of these entities happen, must always use the UUID or id of each entity it is changing (parent, child, all). It must communicate with the server with these ids.
* The one-to-many mappings on the server, uses Set and we have id based equality on entities (not uuid). On the server one must load the entity from the database and then modified it found or create new.
* For saving the on the server, hibernate maintains cascade from the parent to child - hence one needs to call save only on the parent and not on the child.
