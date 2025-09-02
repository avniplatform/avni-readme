---
title: Sync strategies
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
Sync strategies define the way a subject should sync to the user's device. Sync strategies can be defined for each subject type. Each subject type can have different/same sync strategies based on the use case.\
Setting up a sync strategy is a two-step process.

* Defining sync strategy for a subject type.
* Assigning the value of the defined strategy to the user.

## Defining sync strategy for a subject type

For defining sync strategy edit the subject type and under the advance settings configure the sync settings. Below are the different sync strategies available.

| Sync strategy               | Description                                                                                                                                                     |
| :-------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Sync by location            | This is the default strategy and the subject is synced by their registered location.                                                                            |
| Sync by direct assignment   | When this is enabled only subjects assigned to the user will get synced to the user's device.                                                                   |
| Sync registration concept 1 | Any mandatory form element's concept from the registration form can be selected. Subjects get synced based on the values assigned to the user for this concept. |
| Sync registration concept 2 | Similar to `Sync registration concept 1`, this is to support once more concept for the same subject type.                                                       |

![](https://files.readme.io/ad013a1-sync-settings.png "sync-settings.png")

## Assigning the value of the defined strategy to the user

Once the sync strategy is defined for a subject type, values can be assigned to the user so that only those values get synced to the user's device. This can be done by editing an existing user or while creating a new user.

| Sync strategy               | Supported values                   |
| :-------------------------- | :--------------------------------- |
| Sync by location            | Catchment                          |
| Sync by direct assignment   | Already registered subjects        |
| Sync registration concept 1 | Concept values (Code/Text/Numeric) |
| Sync registration concept 2 | Concept values (Code/Text/Numeric) |

![](https://files.readme.io/f215814-Sync_settings.png "Sync settings.png")

**Note** 

* In case of any catchment changes/direct assignment changes user needs to delete the old data and sync as per the newly assigned values.

## Handling on Data Entry App (DEA)

Starting August 2024 ([v9.3.0](https://github.com/avniproject/avni-product/releases/tag/v9.3.0)), sync strategy is also respected for updates made via DEA. DEA users will be able to search for and view all data but will be restricted from creating new / updating existing entities that do not match their sync settings.

If the update the DEA user is making involves changing the value of the attribute controlling sync, the user will be blocked from doing so unless the sync setting allows the user access to both the original value as well as the changed value. i.e. if the DEA user is updating the address of a subject from 'Delhi' to 'Mumbai', the catchment for the DEA user needs to contain both 'Delhi' and 'Mumbai' 

### Override to ignore sync registration concepts

A user-level setting is available to ignore the user's sync registration concept settings for updates made via DEA. Location and Assignment strategies will continue to be respected. In the Avni admin app, navigate to Users -> Search for / Select the user to be modified -> Edit -> Toggle the setting 'Ignore below listed sync settings in the Data Entry app'

![](https://files.readme.io/2f475037479d5e87ded6067331bc01566e0a94bac10d7e896d6725043ea1e44f-image.png)
