---
title: User Subject Types
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
A user subject type is a type that can be used to manage information about users of the system. Each user will have one subject created based on a User type SubjectType. This subject and any data collected against it's encounters and enrolments correspond only to that particular user.

## Special Characteristics

* **Subject Type Create / Edit**: Once a User type SubjectType is created, Avni doesnot allow Administrators to modify the basic configurations of the SubjectType. Ensure that you configure the Subject as needed at the outset. Contact Avni Support if you need any modifications to be done for the User type SubjectType.

  * Registration Date for the subject will be same as User Creation DateTime
  * Toggle of 'Allow empty location' is disabled and is always set to true
  * User's username is inserted as Subject's Firstname
* **Subject Type Create / Edit**: You may only edit the below shown properties post SubjectType creation.

<Image align="center" width="600px" src="https://files.readme.io/ba11a11-Screenshot_2024-05-17_at_3.40.56_PM.png" />

* **Sync**: By default, User type Subjects follow their own Sync strategy, which is currently, to sync a User type Subject only to its corresponding User
* **Subject Creation**: On creation of a "User" type SubjectType, we **automatically** create User type subjects :
  * for every new User created thereafter via the "Webapp" 
  * for new Users created via "CSV Uploads", by triggering a Background Job
  * for all existing Users, by triggering a Background Job
* **Registration disallowed for User type SubjectTypes**: 
When a User type SubjectType is created, the default registration form mapping is not created and hence subjects for this subject type cannot be registered.

* **Access to User type Subject on the client**: Users cannot make use of "Subject Search" capability to access the User type Subject on the Client. They would always have to make use of "Filter" button on "My Dashboard" to select the User type Subject, as shown below.

<Image alt="Select User type in the Subject Filter" align="center" width="500px" border={true} src="https://files.readme.io/f265252-Screenshot_2024-05-17_at_4.23.24_PM.png">
  Select User type in the Subject Filter
</Image>

For organisations that use a Custom Dashboard as the Primary Dashboard, they can easily configure a Offline Report card to provide access to User type Subject.

* **Actions allowed on the User type Subject**: Avni allows organisation to configure a User type Subject similar to the way they would configure a "Person" / "Individual" type Subject types. i.e. they are free to setup Program, Encounter, VisitScheduleRules and so on. They can also configure Privileges in-order to restrict these actions across different UserGroups. A sample screen recording of the client, which has full access to a User type Subject is attached below for reference.

<Image align="center" className="border" width="500px" border={true} src="https://files.readme.io/d966e6d-output.gif" />
