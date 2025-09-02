---
title: Access Control
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
### Access control on Avni Server

- When a graph of entities are returned then the checking access has to be done for the entire tree. Iterating over the object tree after the construction to check for access which could be error prone (and sematically duplicated) - hence it is applied when entity response graph is constructed.
- Access control service also checks for user's presence, hence no @PreAuthorise methods that only check for PrivilegeType and not entity id. Leaving @PreAuthorise is also fine.
- The non-transaction data read is not controlled hence the Preauthorise is only for the user role.
- Access control service performs null check on entity type (for centralisation of those checks and ensuring it is always done). Null check is required otherwise access security is broken - e.g. if subject type is not present, we return all subjects matching other criteria.
- Currently access checks are not on the endpoints that save data provided by the mobile app. This will be tacked later.
- It is difficult to do access checks on search requests. Hence in such case the basic details for entities matching the search criteria can be seen by all the users. This is a known limitation.
- The assumption is that the number of entities loaded will be handful via a particular REST endpoint. Hence access check is done for each entity in some cases. In places where it is simpler to optimise the number of checks it has been done.

### Scenarios related to collection of entities

- External API call to get a list of transaction entities without providing the type: the user should have access to all types otherwise they would get access error whenever an entity of such a type is encountered while paging through.
- If the user provides a list of entity ids to get their data. The user must have access to all those entities otherwise they will get error.
- User wants to get all the children belong to an TX entity and then it would get those it has access to instead of error.