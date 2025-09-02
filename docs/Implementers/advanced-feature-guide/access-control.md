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
Before the introduction of Access Control, organisation users with access to the field app could access all functions (i.e. registration, enrolments, search etc.) in the app. There was a need for some implementations to limit access to specific functions in order to reduce the number of options visible to end users and simplify the workflow for them while also providing a mechanism for access control.

Access Control is implemented via User Groups to facilitate this need. This functionality is available to Organisation admins in the Admin section of the Web app under the User Groups menu.

# Applicability

- The access control rules are applicable in the field app, data entry app, and the web app.
- Access control is not applicable to the reporting app.

# User Groups

User Groups represent a collection of users and a set of privileges allowed to these users. User with EditUserGroup and EditUserConfiguration privilege can define as many groups as they need to define the access control required for their organisation. Each group can be assigned a set of privileges (or all privileges using the switch available at the top).

Each user can be added to multiple groups.

## Privileges are Additive

If any of the groups that a user belongs to allows a particular privilege, the user will have access to that function.

## Default Groups

By default, the system creates an `Everyone` and an `Administrators` group. `Everyone` group includes all the users in the organisation. `Administrators` group grants all the privileges to allow access to all the functionality.

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/9c003c1-Screenshot_2023-08-08_at_3.46.23_PM.png",
        "",
        ""
      ],
      "align": "center",
      "sizing": "500px"
    }
  ]
}
[/block]

Users cannot be removed from `Everyone` group but the privileges associated with this group can be modified. The has all privileges flag cannot be reset for `Administrators` group.

## Privileges

The following privileges are available in order to allow organisation admins to configure fine-grained access to functions for the org users. These privileges are configurable per entity type i.e. a group could have the 'View subject' privilege allowed for subject type 'abc' but disallowed for subject type 'xyz'.

- The Subject level privileges are configurable for each Subject Type setup in your organisation.
- The Enrolment level privileges are configurable for each program setup in your organisation.
- The Encounter level privileges are configurable for each Encounter Type (General or Program) setup in your organisation.
- The Checklist level privileges are configurable for each Program containing checklists for your organisation. 

[block:parameters]
{
  "data": {
    "h-0": "Entity Type",
    "h-1": "Privilege",
    "h-2": "Explanation",
    "0-0": "Subject",
    "0-1": "View subject",
    "0-2": "Controls whether field users can see subjects of a particular subject type in the app.  \n  \nAll other privileges are dependent on this privilege. If disallowed, field users cannot see or access any functionality for the specific subject type.",
    "1-0": "Subject",
    "1-1": "Register subject",
    "1-2": "Allows field users to register new subjects.",
    "2-0": "Subject",
    "2-1": "Edit subject",
    "2-2": "Allows field users to edit previously registered subjects.",
    "3-0": "Subject",
    "3-1": "Void subject",
    "3-2": "Allows field users to void previously registered subjects.",
    "4-0": "Subject",
    "4-1": "Add member\\*",
    "4-2": "Allows field users to add a member to household subject.",
    "5-0": "Subject",
    "5-1": "Edit member\\*",
    "5-2": "Allows field users to edit previously added household members.",
    "6-0": "Subject",
    "6-1": "Remove member\\*",
    "6-2": "Allows field users to remove previously added household members.",
    "7-0": "Enrolment",
    "7-1": "Enrol subject",
    "7-2": "Allows field users to enrol a subject into a program.",
    "8-0": "Enrolment",
    "8-1": "View enrolment details",
    "8-2": "Allows field users to view the program enrolment details for a subject.",
    "9-0": "Enrolment",
    "9-1": "Edit enrolment details",
    "9-2": "Allows field users to edit the program enrolment details for a subject.",
    "10-0": "Enrolment",
    "10-1": "Exit enrolment",
    "10-2": "Allows field users to exit a subject from a program.",
    "11-0": "Encounter",
    "11-1": "View visit",
    "11-2": "Allows field users to view encounters for a subject.",
    "12-0": "Encounter",
    "12-1": "Schedule visit",
    "12-2": "Allows field users to schedule encounters for a subject.",
    "13-0": "Encounter",
    "13-1": "Perform visit",
    "13-2": "Allows field users to perform encounters for a subject.",
    "14-0": "Encounter",
    "14-1": "Edit visit",
    "14-2": "Allows field users to edit previously saved encounter details.",
    "15-0": "Encounter",
    "15-1": "Cancel visit",
    "15-2": "Allows field users to cancel a previously scheduled encounter.",
    "16-0": "Encounter",
    "16-1": "Void visit\\*\\*",
    "16-2": "Allows field users to void an encounter",
    "17-0": "Checklist",
    "17-1": "View checklist",
    "17-2": "Allows field users to view checklist.",
    "18-0": "Checklist",
    "18-1": "Edit checklist",
    "18-2": "Allows field users to edit checklist."
  },
  "cols": 3,
  "rows": 19,
  "align": [
    "left",
    "left",
    "left"
  ]
}
[/block]

`*` Only for 'Household' subject types

`**` Only available as part of Avni 4.0 release (not a full list)

Some of these privileges imply others. For example, allowing the 'Register Subject' privilege implies that the group will also have 'View Subject' allowed. The system handles these dependencies automatically.

## What if I have a simple setup with no separate users?

You can add all your users to the `Administrators` group.

## Is some data with no access control?

Yes some of the app designer and admin user interface (or non-operational data) is open to all users with read access. This data is not confidential in any of the implementations of Avni, hence this has been kept open for any user with login to the organisation.

## Can users update metadata using the API

No, the server also check for the access privileges of the user.

## Super admin access

Access of super admin is restricted to non-operational data of the organisations. Operational data cannot be viewed as well by super admin. This is to provide visibility to the organisations about who can view their data.