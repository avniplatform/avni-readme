---
title: 'UserSubject Assignment - Behavior Guide  '
deprecated: false
hidden: false
metadata:
  robots: index
---
## Overview

UserSubjectAssignment in Avni manages which users have access to which subjects (individuals, groups, and their members). This system ensures proper access control and sync behavior across the application.

## Key Features

### Automatic Member Assignment

When a group is assigned to a user, the system automatically creates individual assignments for all non-voided members of that group. This ensures users have access to both the group and its individual members without requiring separate manual assignments.

### Smart Member Unassignment Constraints

The system prevents data inconsistency by not allowing users to unassign individual members if those members belong to a group that is still assigned to the same user. This maintains logical access control relationships.

### Group Unassignment does not impact Members Assignment

When a Group Subject is unassigned, we do not automatically unassign all member subjects. User would have to manually unassign Members.

### Catchment and Privilege constraints

Only those GroupSubjects and MemberSubjects will sync to User's device that have

* Its Address included in the User's Catchment
* One of the User's UserGroup Privilege should allow for View Privilege for those Subject Types

## Configuration Details

### Subject Type Configuration

* Only subjects marked as `isDirectlyAssignable=true` can be manually assigned
* Groups can be assigned and will automatically include their members as of that moment on the server
* Individual members can also be assigned independently of their groups

### Assignment Rules

* One user can be assigned to multiple subjects
* One subject can be assigned to multiple users
* All assignments must respect the user's designated catchment area

### Database Structure

* `UserSubjectAssignment` table: Tracks user-subject relationships
* `GroupSubject` table: Tracks group-member relationships
* Both tables support soft deletion using the [isVoided](cci:1://file:///Users/himeshr/IdeaProjects/avni-server/avni-server-api/src/main/java/org/avni/server/web/request/FormMappingContract.java:12:4-14:5) flag