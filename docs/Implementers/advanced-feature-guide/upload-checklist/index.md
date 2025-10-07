---
title: Vaccination checklist
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: upload-data
      title: Upload data
---
Avni allows you to upload checklist items from web interface. Before uploading checklist make sure you have already created checklist Item form and checklist rule is already in place. As other forms in Avni, each checklist item need to be a concept and should be uploaded/created before uploading checklist json. A sample concept file for vaccination item looks like [this](https://github.com/avniproject/avni-health-modules/blob/master/src/health_modules/child/metadata/vaccinationConcepts.json). You can directly upload these concepts using metadata upload UI.

Once all the dependencies required by checklist are deployed, you can create a checklist json in the UI editor and upload it. A sample Vaccination checklist looks like [this](https://raw.githubusercontent.com/avniproject/calcutta-kids/master/child/checklist.json).

Click [here](https://docs.google.com/spreadsheets/d/e/2PACX-1vS1Xq4cVi1pDn8B78g_BEdQOcqr5p2hTCeuyhXtpZGKGMHCyba7enJop29zYJy9UyEVYeg523lIutQC/pubhtml#) to see more examples.

### Structure of Checklist json file

```json
{
    "name": "Vaccination",
    "uuid": "uuid of this checklist. We support only single checklist in the system right now so don't change this uuid after you save the file for the first time",
    "items": [
        `<item-object>`
    ]
}
```

### Structure of item-object

```json
{
    //uuid of checklist item
    "uuid": "uuid of checklist item",
    //uuid of a form used to mark the vaccine as completed
    "formUUID": "uuid of a form used to mark the vaccine as completed",
    //uuid of a dependency. This item will get due only after the dependency is marked as completed
    "dependentOn": "uuid",
    //Set this when the dependency can expire and you want this item to be scheduled even then
    "scheduleOnExpiryOfDependency": `<boolean>`,
    //Number of days from base date of checklist after which the item becomes due. Put this only if you are also making this item dependent on another item.
    "minDaysFromStartDate": `<integer>`,
    //Number of days from completion date of dependency after which the item becomes due. Put this only if you are also making this item dependent on another item.
    "minDaysFromDependent": `<integer>`,
    //If an item can expire then you can use this to specify it. It's relative from the base date of the checklist.
    "expiresAfter": `<integer>`,
    //Array of status objects. We use this to specify different phases an item can be in. E.g. You may want to define that it's Due from day 2 to day 30, Critical from Day 30 to 60, and Overdue after day 60. 
    "status": "array of `<status-object>`",
    "concept": `<concept-object>`
}
```

### Structure of status-object

```json
{
  //Looks like an unused field right now. Set it in increasing order for now inside status array
  "displayOrder": 1,
  //Days after which this state should be active
  "start": 270,
  //Days after which this state will not be active
  "end": 291,
  //Name of state
  "state": "Due",
  //Color that the item is displayed in when this state is active
  "color": "#FBF9DA"
}   
```

### Strucuture of concept-object

```json
{
  "uuid": "uuid of the concept that should be used for this item",
  "comment": "Put the name of the concept here for readability"
}
```

### Overview

You can use checklist json file to build the checklist. You can do add list of items and for each item define a state like Due, Critical, Overdue. You can also set depedencies between vaccines so the depedent will get scheduled only after dependency is marked as completed.

## Important Questions:

#### How to test?

Change the device date in future. Don't edit date of birth in profile of the subject.

#### How to add a new item?

Add an item-object in items array

#### How to remove an existing item?

Add voided attribute to an item with value true.

#### How do you add a depedency?

Add dependentOn field

#### How is due date calculated when there is a depedency?

A dependent item goes into it's first state after completion date of it's depedency + specified value of minDaysFromDependent. But there can also be a necessity that an item has to be scheduled only after minimum number of days from base date of the checklist. In the case where we have specified both minDaysFromDependent and minDaysFromStartDate then we compute the **max** of both start dates.

```Text Example
max(dependentCompletionDate + minDaysFromDependent + item.start, 
dependentCompletionDate + minDaysFromStartDate + item.start), 
and move the item to it's first state on that date.
```

#### What will happen if my computed due date is after the expiry date.?

An item's due date based on computations of dependent's completion\_date, minDaysFromDependent and item.start, if exceeds the "expiresAfter" value, then we give priority to "expiresAfter" and mark it as expired.

### Flowchart for determining the Vaccination state:

<Image align="center" src="https://files.readme.io/815c13987e5ad6616202a18db078663bae5ec8000d94680642a418ff4e2ca2f3-Flowcharts_2.png" />

### <span style={{ color: "blue" }}>TODOS:</span>

* [ ] It is not clear if there is any default ordering in displaying status groups and is it possible to change it. E.g. we may want to show all due items in first row, all critical in second, overdue in third, expired in fourth and completed in fifth.