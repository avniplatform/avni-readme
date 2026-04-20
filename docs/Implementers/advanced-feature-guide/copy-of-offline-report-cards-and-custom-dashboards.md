---
title: Copy of Offline Report Cards and Custom Dashboards
deprecated: false
hidden: false
metadata:
  robots: index
---
Avni allows you to create different indicator reports that are available offline to the field users. These reports help field users to derive more insights on the captured data.

Creating an offline report is a two-step process. First, we need to create a report card that holds the actual query function. Second, we group multiple cards into to a dashboard.

## Creating a Card

Creating a new card is no different than creating any other Avni entity. Open app designer and go to the **Card** tab. Click on the new card and provide the details like name, description, etc.

### Card Types

Cards can be of 4 types:

1. **Standard Report Card** — Built-in logic for common use cases (scheduled visits, overdue, approvals, etc.)
2. **Custom Data Card** — A query-based card with configurable actions (view subject profile or do visit)
3. **Nested Report Card** — A single query that renders up to 9 sub-cards
4. **Custom Design Card** — A fully custom card rendered using an uploaded HTML template with dynamic data

### 1. Standard Report Cards

The logic used to display the values in standard cards is already implemented in Avni. The different types are (Entity specified in brackets indicates the type of entity listed on clicking on the card):

* Pending approval (Entity Approval Statuses)

* Approved (Entity Approval Statuses)

* Rejected (Entity Approval Statuses)

* Scheduled visits (Subjects)

* Overdue visits (Subjects)

* Recent registrations (Subjects)

* Recent enrolments (Subjects)

* Recent visits (Subjects)

* Total (Subjects)

* Comments (Subjects)

* Call tasks (Tasks)

* Open subject tasks (Tasks)

* Due checklist (Individuals)

<Image align="center" src="https://files.readme.io/5093034-Screenshot_2023-12-11_at_4.55.48_PM.png" />

#### Standard Report Card Type Filters

Filters can be added at the report card level for certain standard report types. The following filters are supported:

1. Subject Type
2. Program
3. Encounter Type
4. Recent Duration

Subject Type, Program and Encounter Type filters are supported for 'Overdue Visits', 'Scheduled Visits', 'Total' and 'Recent' types ('Recent registrations', 'Recent enrolments', 'Recent visits').

![](https://files.readme.io/c2f3eb0a468c1e8e3efb808ccb831cf87e19c5da5ba92ae9ed99a0af619d528f-image.png)

<br />

Filters can also be configured at the dashboard level (covered below). If a filter is configured both at the report card level and the dashboard level, the filter at the report card level is applied first. Hence, mixing of the same type of filter at both levels should be avoided as it could lead to the unintuitive behaviour of the field user selecting a value, say 'Household' for the subject type filter at the dashboard level but still seeing the numbers for the 'Individual' subject type which is configured at the report card level.

### 2. Custom Data Cards

Custom data cards are query-based cards where the implementer writes the logic. They support configurable **actions** that determine what happens when a user taps the card on the mobile app.

#### Action Configuration

When creating a custom data card (with "Is Standard Report Card?" disabled), an **Action** dropdown appears with two options:

* **View subject profile** (default) — Opens the subject's profile on tap
* **Do visit** — Opens an encounter form on tap

#### View Subject Profile (Default Action)

This is the default action. The card runs your query and displays the result on the dashboard tile.

**Behavior:**

* **Multiple subjects returned:** Shows the list of subjects. The custom card name appears in the top bar.
* **Single subject returned:** Directly opens the subject's profile — no listing screen shown.
* **No subjects returned:** Card shows count as 0.

The query can return a simple array of subjects:

```javascript
'use strict';
({params, imports}) => {
    return params.db.objects('Individual')
        .filtered("voided == false AND subjectType.name = 'MySubjectType'");
};
```

Or return an object with additional display properties:

```javascript
'use strict';
({params, imports}) => {
    const individuals = [...params.db.objects('Individual')
        .filtered("voided == false AND subjectType.name = 'MySubjectType'")];
    return {
        primaryValue: individuals.length,
        secondaryValue: '(active)',
        cardColor: '#123456',
        textColor: '#ffffff',
        cardName: 'Dynamic Card Name',
        lineListFunction: () => individuals
    };
};
```

**Query Return Properties:**

| Property           | Required | Description                                                                                                                                            |
| ------------------ | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `primaryValue`     | Yes      | The main value displayed on the card (typically a count or percentage)                                                                                 |
| `secondaryValue`   | No       | Additional text displayed below the primary value                                                                                                      |
| `cardColor`        | No       | Background color of the card tile (hex). Overrides the color set in the admin UI                                                                       |
| `textColor`        | No       | Text color on the card tile (hex). Overrides default                                                                                                   |
| `cardName`         | No       | Display name on the card tile and list header. Overrides the card name set in the admin UI                                                             |
| `lineListFunction` | No       | A function that returns the list of subjects. If provided, the card becomes tappable. If not provided, the card displays the value but is not tappable |

**Backward Compatibility:** All existing custom card queries continue to work. Queries returning a plain array of subjects, or only `primaryValue` and `lineListFunction`, or `primaryValue` without `lineListFunction` (e.g., percentage cards) all work as before.

**Example: Percentage Card (Non-tappable because `lineListFunction` is not a function)**

```javascript
'use strict';
({params, imports}) => {
    const total = params.db.objects('Individual').filtered('voided = false').length;
    const enrolled = params.db.objects('Individual')
        .filtered("voided = false AND SUBQUERY(enrolments, $e, $e.voided = false AND $e.program.name = 'MyProgram').@count > 0").length;
    const percentage = total > 0 ? ((enrolled / total) * 100).toFixed(1) : 0;
    return {primaryValue: percentage + '%', lineListFunction: 'n/a'};
};
```

#### Do Visit Action

This action is for cards where tapping should directly open an encounter form. When **Do visit** is selected, additional configuration fields appear:

| Field              | Required | Description                                                                                      |
| ------------------ | -------- | ------------------------------------------------------------------------------------------------ |
| **Subject Type**   | Yes      | The subject type this card applies to                                                            |
| **Program**        | No       | The program (for program encounters). Leave empty for general encounters                         |
| **Encounter Type** | Yes      | The encounter form to open                                                                       |
| **Visit Type**     | Yes      | **Scheduled** — opens an existing scheduled visit. **Unplanned** — creates and opens a new visit |

**Scheduled Visit Type:**

* On tap, only subjects with a scheduled encounter of the configured type are shown.
* The encounter type name appears below each subject in the list. Clicking it opens the scheduled visit form directly.
* Single matching subject: opens the form directly — no listing shown.
* No matching subjects: card dismisses without navigation.

**Unplanned Visit Type:**

* On tap, only subjects enrolled in the configured program (if set) are shown.
* The encounter type name appears below each subject. Clicking it opens a new visit form.
* Single matching subject: opens the new form directly — no listing shown.

The count shown on the card tile reflects the filtered count — only subjects with eligible encounters, which may differ from the total returned by the query.

**Example: Scheduled Program Encounter**

Admin configuration: Action = Do visit, Subject Type = Patient, Program = Maternal Health, Encounter Type = ANC Visit, Visit Type = Scheduled

```javascript
'use strict';
({params, imports}) => {
    const individuals = [...params.db.objects('Individual')
        .filtered("voided == false AND subjectType.name = 'Patient'")];
    return {
        primaryValue: individuals.length,
        cardName: 'ANC Visits Due',
        cardColor: '#e65100',
        lineListFunction: () => individuals
    };
};
```

**Example: Unplanned General Encounter**

Admin configuration: Action = Do visit, Subject Type = Household, Program = (empty), Encounter Type = Survey, Visit Type = Unplanned

```javascript
'use strict';
({params, imports}) => {
    const individuals = [...params.db.objects('Individual')
        .filtered("voided == false AND subjectType.name = 'Household'")];
    return {
        primaryValue: individuals.length,
        cardName: 'Surveys to Fill',
        lineListFunction: () => individuals
    };
};
```

### 3. Custom Design Cards

Custom design cards allow implementers to upload an HTML file as the display template for a fully custom card. This enables org-specific layouts (tables, styling, sections) without being constrained by the fixed Avni card UI.

**Configuration:**

1. Select **Custom Design Card** as the card type
2. **Data Rule** (optional): A JS rule that returns dynamic data for the HTML template. Input/output follows the same pattern as other card rules — dashboard filters are passed as input, and the rule returns data accessible in the HTML via `data.variableName`. If `primaryValue`/`secondaryValue` are returned, they show on the card tile. If `cardName`/`cardColor`/`textColor` are returned, they override defaults.
3. **HTML File** (required): Upload an HTML file defining the custom layout. Saving without an HTML file shows a validation error. Saving without a data rule is allowed.

In the mobile app, the HTML template is rendered with data from the rule. The HTML is wrapped as a template literal and data is passed to generate the final HTML string, rendered within a `WebView`.

### Bundle Upload

Card configuration — including action settings (action type, subject type, program, encounter type, visit type) and custom design card HTML — is included in the organisation bundle export/import. All card settings are preserved across bundle upload. No additional configuration is needed after import.

## Creating a Dashboard

After all the cards are done it's time to group them together using the dashboard. Offline Dashboards have the following sub-components:

* Sections : Visual Partitions used to club together cards of specific grouping type
* Offline (Custom) Report Cards : Usually Clickable blocks with count information about grouping of Individuals or EntityApprovals of specific type
* Filters : Configurable filters that get applied to all "Report Cards" count and listing

Users with access to the "App Designer" can Create, Modifiy or Delete Custom Dashboards as seen below.

![](https://files.readme.io/824878a-image.png)

### Steps to configure a Custom Dashboard

* Click on the dashboard tab on the app designer and click on the new dashboard.
* This will take you to the new dashboard screen. Provide the name and description of the dashboard.
* You can create sections on this screen and
* Select all the cards you need to add to the section in the dashboard.
* After adding all the cards, you can re-arrange the cards in the order you want them to see in the field app.

<Image align="center" src="https://files.readme.io/b6a8b74-Screenshot_2023-12-11_at_4.45.34_PM.png" />

### Dashboard Filters

You can also create filters for a dashboard on the same screen by clicking on "Add Filter". This shows a popup as in the below screenshot where you can configure your filter and set the filter name, type, widget type and other values based on your filter type.

![](https://files.readme.io/91f1aa1-image.png)

Once all the changes are done. Save the dashboard.

#### For the filters to be applied to the cards in the dashboard, the code for the cards will need to handle the filters.

Sample Code for handling filters in report card:

```
'use strict';
({params, imports}) => {
//console.log('------>',params.ruleInput.filter( type => type.Name === "Gender" ));
//console.log("params:::", JSON.stringify(params.ruleInput));
  let individualList = params.db.objects('Individual').filtered("voided = false and subjectType.name = 'Individual'" )
     .filter( (individual) => individual.getAgeInYears() >= 18 && individual.getAgeInYears() <= 49  &&  individual.getObservationReadableValue('Is sterilisation done') === 'No');
  
  if (params.ruleInput) {

       let genderFilter = params.ruleInput.filter(rule => rule.type === "Gender");
       let genderValue = genderFilter[0].filterValue[0].name;
      
        console.log('genderFilter---------',genderFilter);
        console.log('genderValue---------',genderValue);        
        
      return individualList
     .filter( (individual) => {
     console.log("individual.gender:::", JSON.stringify(individual.gender.name));
     return individual.gender.name === genderValue;
     });
     }
     else return individualList;
};
```

### Assigning custom Dashboards to User Groups

Custom Dashboards created need to be assigned specifically to a User Group, in-order for Users to see it on the Avni-client mobile app. You may do this, by navigating to the "Admin" app -> "User Groups" -> (User_GROUP) -> "Dashboards" tab, and assigning one or more Custom Dashboards to a User-Group.

In addition, You can also mark one of these Custom Dashboards as the Primary (Is Primary: True) dashboard from the "Admin" app -> "User Groups" -> (User_GROUP) -> "Dashboards".

<Image align="center" src="https://files.readme.io/54b6434-Screenshot_2024-06-26_at_12.14.37_PM.png" />

## Using the Dashboard in the Field App

After saving the dashboard sync the field app, and from the bottom "More" tab click on the "Dashboards" option. It will take you to the dashboard screen and will show all the cards that are added to the dashboard.

<Image align="center" alt="Report cards only passing List of subjects." caption="Report cards only passing List of subjects." title="dashboard-field-app.png" src="https://files.readme.io/8b37cf6-Screenshot_2024-06-26_at_12.15.37_PM.png" width="400px" />

<Image align="center" alt={566} caption="Report cards  returning `primaryValue` and `secondaryValue` object" title="offline-dashboard.png" src="https://files.readme.io/548f99d-offline-dashboard.png" width="400px" />

Clicking any card will take the user to the subject listing page, which will display all the subject names returned by the card query. For custom data cards with the "View subject profile" action, if only one subject is returned, the app directly opens the subject's profile. For cards with the "Do visit" action, clicking opens the encounter form directly (for single subject) or shows the list with encounter type rows below each subject.

<Image align="center" width="400px" src="https://files.readme.io/18fb944-Subject-list-field-app.png" />

Users can click on any subject and navigate to their dashboard.

## Secondary Dashboard

### Web app configuration

As part of Avni release 8.0.0, a new feature of a secondary dashboard is added which can be configured at user group level to populate an additional option on the Avni mobile app bottom drawer to navigate to a secondary dashboard. This configuration has to be done in the user group in Avni web app.

* By navigating to the dashboard section in a particular user group where dashboards can be added to user groups, the secondary dashboard can be defined apart from the home dashboard. As shown in the screenshot below, any dashboard can be selected as the secondary dashboard.

<Image align="center" width="1500px" src="https://files.readme.io/68ac5d9-Screenshot_2024-06-26_at_12.14.37_PM.png" />

<Image align="center" width="15px" src="https://files.readme.io/a5640e1-Se.png" />

### Secondary dashboard in mobile app

The configuration mentioned above would display the particular dashboard in the mobile app as given below. This would allow users to access the home and secondary dashboard from the bottom drawer of the mobile app instead of navigating to the more page.

<Image align="center" width="400px" src="https://files.readme.io/d95166d-Screenshot_2024-06-26_at_12.17.40_PM.png" />

### Clash in Dashboards configuration across different UserGroups

In-case a User belongs to multiple UserGroups, where-in each has a different Primary and/or Secondary Dashboards, then the behaviour is undeterministic. I.e, any of the possible Primary Dashboards across the various UserGroups, would show up as the Primary on the app. Similar behaviour should be expected of the Secondary Dashboard as well.

## Report card query examples

As mentioned earlier, a query can return a list of Individuals or an object with properties:

```javascript
{
    primaryValue: '20',
    secondaryValue: '(5%)',
    cardColor: '#123456',    // overrides card background color
    textColor: '#ffffff',    // overrides card text color
    cardName: 'Dynamic Name', // overrides card name on tile and list header
    lineListFunction: () => [...] // returns list of subjects; makes card tappable
}
```

`primaryValue` is the main display value. `lineListFunction` should return the list of subjects — if not provided or not a function, the card displays the value but is not tappable. `cardColor`, `textColor`, and `cardName` are optional and override the static values configured in the admin UI.

DB instance is passed using the params and useful libraries like lodash and moment are available in the imports parameter of the function. Below are some examples of writing the `lineListFunction`.

The below function returns a list of pregnant women having any high-risk conditions.

```javascript High risk condition women
'use strict';
({params, imports}) => {
    const isHighRiskWomen = (enrolment) => {
        const weight = enrolment.findLatestObservationInEntireEnrolment('Weight');
        const hb = enrolment.findLatestObservationInEntireEnrolment('Hb');
        const numberOfLiveChildren = enrolment.findLatestObservationInEntireEnrolment('Number of live children');
        return (weight && weight.getReadableValue() < 40) || (hb && hb.getReadableValue() < 8) ||
            (numberOfLiveChildren && numberOfLiveChildren.getReadableValue() > 3)
    };
    return {
      lineListFunction: () => params.db.objects('Individual')
        .filtered(`SUBQUERY(enrolments, $enrolment, SUBQUERY($enrolment.encounters, $encounter, $encounter.encounterType.name = 'Monthly monitoring of pregnant woman' and $encounter.voided = false).@count > 0 and $enrolment.voided = false and voided = false).@count > 0`)
        .filter((individual) => individual.voided === false && _.some(individual.enrolments, (enrolment) => enrolment.program.name === 'Pregnant Woman' && isHighRiskWomen(enrolment)))
    }
};
```

It is important to write optimised query and load very less data in memory for processing. There will be the cases where query can't be written in realm and we need to load the data in memory, but remember more data we load into the memory slower will be the reports. As an example consider below two cases, in the first case we directly query realm to fetch all the individuals enrolled in Child program, but in the second case we first load all individuals into memory and then filter those cases.

```javascript Query in Realm context (better performance)
'use strict';
({params, imports}) => ({
    lineListFunction: () => params.db.objects('Individual')
        .filtered(`SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and voided = false).@count > 0`)
});
```
```javascript Query in app context (poor performance)
'use strict';
({params, imports}) => {
    return params.db.objects('Individual')
        .filter((individual) => individual.voided === false && _.some(individual.enrolments, (enrolment) => enrolment.program.name === 'Child'))
};
```

For using the filters in the rules also check section on Dashboard Card Rule here - [Writing rules](doc:writing-rules)

## Performance of queries

The report cards requires one to return a list individuals. This can be done by:

1. Performing db.objects on Individual and filtering them down.
2. Performing db.objects on descendants of Subject (like encounter, enrolment), filter them down, then return list of Individuals from each filtered object. Example is given below.

## Implementation Patterns for writing performant queries

Please refer to [this reference for Realm Query Language](https://www.mongodb.com/docs/atlas/device-sdks/realm-query-language/).

To understand difference between filter and filtered that is referred below, please see, [https://avni.readme.io/docs/writing-rules#difference-between-filter-and-filtered](https://avni.readme.io/docs/writing-rules#difference-between-filter-and-filtered)

Please also get in touch with platform team if you identify a new pattern and a new type of requirement where none of the following fits.

1. Filter based on chronological data
   1. The matching has to be done on specific chronological descendant entity. e.g. `first` encounter of a specific type, `recent` encounter of specific type.
   2. In this case performing `db.objects` on Individual will lead to either very complex queries or will demand performing filtering in memory using JS.
   3. In this case one can do `db.objects` on descendant entity and then use something like `.filtered('TRUEPREDICATE sort(programEnrolment.individual.uuid asc , encounterDateTime desc) Distinct(programEnrolment.individual.uuid)')` to get the chronological relevant entity at the top in each group. Distinct keyword picks only the first entity in the sorted group.
   4. After performing `filtered`, one can return Subjects by performing `list.map(enc => enc.programEnrolment.individual)`
2. Filter based exact observation value
   1. Matching observations by loading them in memory and calling JS functions will lead to slower reports.
   2. A combination of `subquery` and realm query based match will have much better performance. For example: matching observation that has a specific value - `SUBQUERY(observations, $observation, $observation.concept.name = 'Phone number' and $observation.valueJSON CONTAINS '7555795537'`
3. Filter based on exact specific coded observation value
   1. Matching coded value using its name will require one to load data in memory and perform the match. But this could result in sub-optimal performance. Hence the readability of the report should be sacrificed here for performance.
   2. The query will be like `SUBQUERY(observations, $observation, $observation.concept.uuid = 'Marital Status' and $observation.valueJSON CONTAINS 'fb1080b4-d1ec-4c87-a10d-3838ba9abc5b'`
   3. Please note here that multiple observations can be matched here using OR, AND etc.
4. Filter based on a custom observation value expression.
   1. Instead of matching against a single value match using numeric expression. e.g. match BMI greater than 20.0.
   2. This kind of match cannot be done using realm query and implementing them in JS may result in poor performance.
   3. In such cases we should find out the significance of magic number 20.0. Usually we also have a coded decision observation associated that has meaning behind 20.0, like malnutrition status, BMI Status etc. If there is one then we should match against that using pattern 3 above. If such observation is not present then consider adding them to decisions.
   4. In requirements where such associated coded observation are not present and cannot be added, the performance will depend on the number of observations and entities being matched. If this number is large the performance is expected to be slow, it is better to avoid making reports, or move such reports to their own dashboard - so that they don't impact the usability of other reports.

### Detailed examples

#### DEPRECATED: Avoid using generic functions:

* The following is deprecated cause we should use `Filter based on chronological data` pattern from above.
* To find observation of a concept avoid using the function `findLatestObservationInEntireEnrolment` unless absolutely necessary since it searches for the observation in all encounters and enrolment observations. Use specific functions.
* Eg: To find observation in enrolment can use the function `enrolment.findObservation` or to find observations in specific encounter type can get the encounters using `enrolment.lastFulfilledEncounter(...encounterTypeNames)` and then find observation. Refer code examples for the below 3 usecases.
* ```text Usecase 1
  Find children with birth weight less than 2. Birth weight is captured in enrolment
  ```
  ```javascript Recommended way
  'use strict';
  ({params, imports}) => {
      const isLowBirthWeight = (enrolment) => {
          const obs = enrolment.findObservation('Birth Weight');
          return obs ? obs.getReadableValue() <= 2 : false;
      };
      return params.db.objects('Individual')
          .filtered(`voided = false and SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false).@count > 0`)
          .filter((individual) => _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && _.isNil(enrolment.programExitDateTime) && !enrolment.voided && isLowBirthWeight(enrolment)))
  };
  ```
  ```javascript Not recommended way
  'use strict';
  ({params, imports}) => {
      const isLowBirthWeight = (enrolment) => {
          const obs = enrolment.findLatestObservationInEntireEnrolment('Birth Weight');
          return obs ? obs.getReadableValue() <= 2 : false;
      };
      return params.db.objects('Individual')
          .filtered(`SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false and SUBQUERY($enrolment.observations, $observation, $observation.concept.uuid = 'c82cd1c8-d0a9-4237-b791-8d64e52b6c4a').@count > 0 and voided = false).@count > 0`)
          .filter((individual) => individual.voided === false && _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && isLowBirthWeight(enrolment)))
  };
  ```
  ```Text How optimized
  do voided check first in realm instead of JS - helps in filtering ahead
  Check for concept where it is used - no need to check in all encounters and enrolment
  ```
  ```Text Usecase 2
  Find MAM status from value of Nutritional status concept captured in Child Followup encounter
  ```
  ```javascript Recommended way
  // While this example is illustrating the right JS function to use, but it may be better to filter(ed)
  // encounter schema than to start with Individual
  // i.e. someting like db.objects("ProgramEncounter").filtered("programEnrolment.individual.voided = false AND programEnrolment.voided = false AND ...")
  // then return Individuals using .map(enc => enc.programEnrolment.individual) after filtering all program encounters
  'use strict';
  ({params, imports}) => {
      const isUndernourished = (enrolment) => {
          const encounter = enrolment.lastFulfilledEncounter('Child Followup'); 
          if(_.isNil(encounter)) return false; 
         
         const obs = encounter.findObservation("Nutritional status of child");
         return (!_.isNil(obs) && _.isEqual(obs.getValueWrapper().getValue(), "MAM"));
      };
      
      return params.db.objects('Individual')
          .filtered(`voided = false and SUBQUERY(enrolments, $enrolment,$enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false and SUBQUERY($enrolment.encounters, $encounter, $encounter.encounterType.name = 'Child Followup' and $encounter.voided = false).@count > 0).@count > 0`)
          .filter((individual) => individual.getAgeInMonths() > 6 && _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && _.isNil(enrolment.programExitDateTime) && !enrolment.voided && isUndernourished(enrolment)))
  };
  ```
  ```javascript Not recommended way
  'use strict';
  ({params, imports}) => {
      const isUndernourished = (enrolment) => {
          const obs = enrolment.findLatestObservationInEntireEnrolment('Nutritional status of child');
          return obs ? _.includes(['MAM'], obs.getReadableValue()) : false;
      };
      return params.db.objects('Individual')
          .filtered(`SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false and SUBQUERY($enrolment.encounters, $encounter, $encounter.encounterType.name = 'Child Followup' and $encounter.voided = false and SUBQUERY($encounter.observations, $observation, $observation.concept.uuid = '3fb85722-fd53-43db-9e8b-d34767af9f7e').@count > 0).@count > 0 and voided = false).@count > 0`)
          .filter((individual) => individual.voided === false && individual.getAgeInMonths() > 6 && _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && isUndernourished(enrolment)))
  };
  ```
  ```Text How optimized
  Check only in specific encounter type
  ```
  ```Text Usecase 3
  Find sick children using the presence of value for concept 'Refer to the hospital for' which is not a mandatory concept
  ```
  ```javascript Recommended way
  // also see comments in Recommended way for use case 2
  'use strict';
  ({params, imports}) => {
      const isChildSick = (enrolment) => {
        const encounter = enrolment.lastFulfilledEncounter('Child Followup', 'Child PNC'); 
        if(_.isNil(encounter)) return false; 
         
        const obs = encounter.findObservation('Refer to the hospital for');
        return !_.isNil(obs);
      };
      
      return params.db.objects('Individual')
          .filtered(`voided = false and SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false).@count > 0`)
          .filter(individual => _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && _.isNil(enrolment.programExitDateTime) && !enrolment.voided && isChildSick(enrolment)))
  };
  ```
  ```javascript Not recommended way
  'use strict';
  ({params, imports}) => {
      const isChildSick = (enrolment) => {
           const obs = enrolment.findLatestObservationFromEncounters('Refer to the hospital for');    
           return obs ? obs.getReadableValue() != undefined : false;
      };
      
      return params.db.objects('Individual')
          .filtered(`SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false).@count > 0`)
          .filter((individual) => individual.voided === false && (_.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && isChildSick(enrolment))) )
  };
  ```
  ```Text How optimized
  Check only in last encounter, not all encounters since the concept is not a mandatory concept. 
  Using findLatestObservationFromEncounters will check in all encounters and mark child has sick even if the concept value had represented sick in any of the previous encounters, resulting in bug, since the concept is not a mandatory concept.
  ```

#### Based on the use case decide whether to write the logic using realm query or JS.

* Not always achieving the purpose using realm queries might be efficient/possible.

  * **DEPRECATED** cause we should use `Filter based on chronological data` pattern from above. Eg: consider a use case where a mandatory concept is used in a program encounter. Now to check the latest value of the concept, its sufficient to check the last encounter and need not iterate all encounters. Since realm subquery doesn't support searching only in the last encounter, for such usecases, using realm queries not only becomes slow and also sometimes inappropriate depending on the usecase. So in such cases, using JS code for the logic, is more efficient. (refer the below code example)

    * ```Text Usecase
      ```

    Find dead children using concept value captured in encounter cancel or program exit form.

    ````
        ```javascript Recommended way
        'use strict';
        ({params, imports}) => { 
           const moment = imports.moment;

           const isChildDead = (enrolment) => {
              const exitObservation = enrolment.findExitObservation('29238876-fbd8-4f39-b749-edb66024e25e');
              if(!_.isNil(exitObservation) && _.isEqual(exitObservation.getValueWrapper().getValue(), "cbb0969c-c7fe-4ce4-b8a2-670c4e3c5039"))
                return true;
              
              const encounters = enrolment.getEncounters(false);
              const sortedEncounters = _.sortBy(encounters, (encounter) => {
              return _.isNil(encounter.cancelDateTime)? moment().diff(encounter.encounterDateTime) : 
              moment().diff(encounter.cancelDateTime)}); 
              const latestEncounter = _.head(sortedEncounters);
              if(_.isNil(latestEncounter)) return false; 
               
              const cancelObservation = latestEncounter.findCancelEncounterObservation('0066a0f7-c087-40f4-ae44-a3e931967767');
              if(_.isNil(cancelObservation)) return false;
              return _.isEqual(cancelObservation.getValueWrapper().getValue(), "cbb0969c-c7fe-4ce4-b8a2-670c4e3c5039")
            };

        return params.db.objects('Individual')
                .filtered(`voided = false`)
                .filter(individual => _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && isChildDead(enrolment)));
        }
    ````
    ```javascript Not recommended way
    'use strict';
    ({params, imports}) => {

    return params.db.objects('Individual')
            .filtered(`subquery(enrolments, $enrolment, $enrolment.program.name == "Child" and subquery(programExitObservations, $exitObservation, $exitObservation.concept.uuid ==  "29238876-fbd8-4f39-b749-edb66024e25e" and ( $exitObservation.valueJSON ==  '{"answer":"cbb0969c-c7fe-4ce4-b8a2-670c4e3c5039"}')  ).@count > 0 ).@count > 0 OR subquery(enrolments.encounters, $encounter, $encounter.voided == false and subquery(cancelObservations, $cancelObservation, $cancelObservation.concept.uuid ==  "0066a0f7-c087-40f4-ae44-a3e931967767" and ( $cancelObservation.valueJSON ==  '{"answer":"cbb0969c-c7fe-4ce4-b8a2-670c4e3c5039"}')  ).@count > 0 ).@count > 0`)
            .filter(ind => ind.voided == false)
    };
    ```
    ```Text How optimized
    Moving to JS since realm query iterates through all encounters which can be avoided in JS
    In this cases since the intention is to find if child is dead, hence it can be assumed to be captured in the last encounter or in program exit form based on the domain knowledge

    ```
  * Please also refer to `Filter based on a custom observation value expression` pattern above, before using this. Consider another use case, where observations of numeric concepts need to be compared. This is not possible to achieve via realm query since the solution would involve the need for JSON parsing of the stored observation. Hence JS logic is appropriate here. (refer below code example)
    * ```Text Usecase
      ```
    Find children with birth weight less than 2. Birth weight is captured in enrolment
    ````
        ```javascript Recommended way
        'use strict';
        ({params, imports}) => {
            const isLowBirthWeight = (enrolment) => {
                const obs = enrolment.findObservation('Birth Weight');
                return obs ? obs.getReadableValue() <= 2 : false;
            };
            return params.db.objects('Individual')
                .filtered(`voided = false and SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false).@count > 0`)
                .filter((individual) => _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && _.isNil(enrolment.programExitDateTime) && !enrolment.voided && isLowBirthWeight(enrolment)))
        };
    ````
    ```javascript Not recommended way
    'use strict';
    ({params, imports}) => {
        const isLowBirthWeight = (enrolment) => {
            const obs = enrolment.findLatestObservationInEntireEnrolment('Birth Weight');
            return obs ? obs.getReadableValue() <= 2 : false;
        };
        return params.db.objects('Individual')
            .filtered(`SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false and SUBQUERY($enrolment.observations, $observation, $observation.concept.uuid = 'c82cd1c8-d0a9-4237-b791-8d64e52b6c4a').@count > 0 and voided = false).@count > 0`)
            .filter((individual) => individual.voided === false && _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && isLowBirthWeight(enrolment)))
    };
    ```
    ```Text How optimized
    Moving to realm query for checking birth weight was not possible. If it were a equals comparison it can be achieved using 'CONTAINS' in realm
    ```
* But in cases where time complexity is the same for both cases, writing realm queries would be efficient to achieve the purpose. (refer below code example). Also refer to `Filter based on a custom observation value expression` pattern above.

  * ```Text Usecase
    ```

  Find 13 months children who are completely immunised

  ````
    ```javascript Recommended way
    'use strict';
    ({params, imports}) => {        
       return params.db.objects('Individual')
            .filtered(`voided = false and SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false and SUBQUERY(checklists, $checklist, SUBQUERY(items, $item, ($item.detail.concept.name = 'BCG' OR $item.detail.concept.name = 'Polio 0' OR $item.detail.concept.name = 'Polio 1' OR $item.detail.concept.name = 'Polio 2' OR $item.detail.concept.name = 'Polio 3' OR $item.detail.concept.name = 'Pentavalent 1' OR $item.detail.concept.name = 'Pentavalent 2' OR $item.detail.concept.name = 'Pentavalent 3' OR $item.detail.concept.name = 'Measles 1' OR $item.detail.concept.name = 'Measles 2' OR $item.detail.concept.name = 'FIPV 1' OR $item.detail.concept.name = 'FIPV 2' OR $item.detail.concept.name = 'Rota 1' OR $item.detail.concept.name = 'Rota 2') and $item.completionDate <> nil).@count = 14).@count > 0).@count > 0`)
            .filter(individual => individual.getAgeInMonths() >= 13)     
    };
  ````
  ```javascript Not recommended way
  'use strict';
  ({params, imports}) => {
      const isChildGettingImmunised = (enrolment) => {
          if (enrolment.hasChecklist) {
              const vaccineToCheck = ['BCG', 'Polio 0', 'Polio 1', 'Polio 2', 'Polio 3', 'Pentavalent 1', 'Pentavalent 2', 'Pentavalent 3', 'Measles 1', 'Measles 2', 'FIPV 1', 'FIPV 2', 'Rota 1', 'Rota 2'];
              const checklist = _.head(enrolment.getChecklists());
              return _.chain(checklist.items)
                  .filter(({detail}) => _.includes(vaccineToCheck, detail.concept.name))
                  .every(({completionDate}) => !_.isNil(completionDate))
                  .value();
          }
          return false;
      };

      return params.db.objects('Individual')
          .filtered(`SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false).@count > 0`)
          .filter((individual) => individual.voided === false && individual.getAgeInMonths() >= 13 && _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && isChildGettingImmunised(enrolment)))
  };
  ```
  ```Text How optimized
  Moving to realm query since no of children with age < 13 months were less
  ```
* In most cases, filtering as much as possible using realm queries (for cases like voided checks) and then doing JS filtering on top of it if needed, would be appropriate. (refer the below code example)

  * ```Text Usecase
    ```

  Find dead children using concept value captured in encounter cancel or program exit form.

  ````
    ```javascript Recommended way
    'use strict';
    ({params, imports}) => { 
       const moment = imports.moment;

       const isChildDead = (enrolment) => {
          const exitObservation = enrolment.findExitObservation('29238876-fbd8-4f39-b749-edb66024e25e');
          if(!_.isNil(exitObservation) && _.isEqual(exitObservation.getValueWrapper().getValue(), "cbb0969c-c7fe-4ce4-b8a2-670c4e3c5039"))
            return true;
          
          const encounters = enrolment.getEncounters(false);
          const sortedEncounters = _.sortBy(encounters, (encounter) => {
          return _.isNil(encounter.cancelDateTime)? moment().diff(encounter.encounterDateTime) : 
          moment().diff(encounter.cancelDateTime)}); 
          const latestEncounter = _.head(sortedEncounters);
          if(_.isNil(latestEncounter)) return false; 
           
          const cancelObservation = latestEncounter.findCancelEncounterObservation('0066a0f7-c087-40f4-ae44-a3e931967767');
          if(_.isNil(cancelObservation)) return false;
          return _.isEqual(cancelObservation.getValueWrapper().getValue(), "cbb0969c-c7fe-4ce4-b8a2-670c4e3c5039")
        };

    return params.db.objects('Individual')
            .filtered(`voided = false`)
            .filter(individual => _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && isChildDead(enrolment)));
    }
  ````
  ```javascript Not recommended way
  'use strict';
  ({params, imports}) => {

  return params.db.objects('Individual')
          .filtered(`subquery(enrolments, $enrolment, $enrolment.program.name == "Child" and subquery(programExitObservations, $exitObservation, $exitObservation.concept.uuid ==  "29238876-fbd8-4f39-b749-edb66024e25e" and ( $exitObservation.valueJSON ==  '{"answer":"cbb0969c-c7fe-4ce4-b8a2-670c4e3c5039"}')  ).@count > 0 ).@count > 0 OR subquery(enrolments.encounters, $encounter, $encounter.voided == false and subquery(cancelObservations, $cancelObservation, $cancelObservation.concept.uuid ==  "0066a0f7-c087-40f4-ae44-a3e931967767" and ( $cancelObservation.valueJSON ==  '{"answer":"cbb0969c-c7fe-4ce4-b8a2-670c4e3c5039"}')  ).@count > 0 ).@count > 0`)
          .filter(ind => ind.voided == false);
  };
  ```
  ```Text How optimized
  Moving to JS since realm query iterates through all encounters which can be avoided in JS
  In this cases since the intention is to find if child is dead it can be assumed to be captured in the last encounter or in program exit form based on the domain knowledge

  ```

Also check - [https://avni.readme.io/docs/writing-rules#using-paramsdb-object-when-writing-rules](https://avni.readme.io/docs/writing-rules#using-paramsdb-object-when-writing-rules)

#### DEPRECATED. Use Concept UUIDs instead of their names for comparison

Please check - `Filter based on a custom observation value expression` pattern above.

Though not much performance improvement, using concept uuids(for comparing with concept answers), instead of getting its readable values did provide minor improvement(in seconds) when need to iterate through thousands of rows. (refer below code example)

* ```Text Usecase
  Find children with congential abnormality based on values of certain concepts
  ```
  ```javascript Recommended way
  'use strict';
  ({params, imports}) => {
      const isChildCongenitalAnamoly = (enrolment) => {
         const _ = imports.lodash;
      
         const encounter = enrolment.lastFulfilledEncounter('Child PNC'); 
         if(_.isNil(encounter)) return false; 
         
         const obs1 = encounter.findObservation("Is the infant's mouth cleft pallet seen?");
         const condition2 = obs1 ? obs1.getValueWrapper().getValue() === '3a9fe9a1-a866-47ed-b75c-c0071ea22d97' : false;
           
         const obs2 = encounter.findObservation('Is there visible tumor on back or on head of infant?');
         const condition3 = obs2 ? obs2.getValueWrapper().getValue() === '3a9fe9a1-a866-47ed-b75c-c0071ea22d97' : false;
           
         const obs3 = encounter.findObservation("Is foam coming from infant's mouth continuously?");
         const condition4 = obs3 ? obs3.getValueWrapper().getValue() === '3a9fe9a1-a866-47ed-b75c-c0071ea22d97' : false;
                    
           return condition2 || condition3 || condition4;
      };
      
      const isChildCongenitalAnamolyReg = (individual) => {
           const obs = individual.findObservation('Has any congenital abnormality?');
           return obs ? obs.getValueWrapper().getValue() === '3a9fe9a1-a866-47ed-b75c-c0071ea22d97' : false;
      };
      
      return params.db.objects('Individual')
          .filtered(`voided = false and SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false).@count > 0`)
          .filter((individual) => (isChildCongenitalAnamolyReg(individual) || 
              _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && _.isNil(enrolment.programExitDateTime) && !enrolment.voided && isChildCongenitalAnamoly(enrolment) )) )
  };
  ```

<br />

```javascript Not recommended way
'use strict';
({params, imports}) => {
    const isChildCongenitalAnamoly = (enrolment) => {
         
         const obs1 = enrolment.findLatestObservationInEntireEnrolment("Is the infant's mouth cleft pallet seen?");
         const condition2 = obs1 ? obs1.getReadableValue() === 'Yes' : false;
         
     const obs2 = enrolment.findLatestObservationInEntireEnrolment('Is there visible tumor on back or on head of infant?');
         const condition3 = obs2 ? obs2.getReadableValue() === 'Yes' : false;
         
         const obs3 = enrolment.findLatestObservationInEntireEnrolment("Is foam coming from infant's mouth continuously?");
         const condition4 = obs3 ? obs3.getReadableValue() === 'Yes' : false;
                  
         return condition2 || condition3 || condition4;
    };
    
    const isChildCongenitalAnamolyReg = (individual) => {
         const obs = individual.getObservationReadableValue('Has any congenital abnormality?');
         return obs ? obs === 'Yes' : false;
    };
    
    return params.db.objects('Individual')
        .filtered(`SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Child' and $enrolment.programExitDateTime = null and $enrolment.voided = false).@count > 0`)
        .filter((individual) => individual.voided === false && (isChildCongenitalAnamolyReg(individual) || 
            _.some(individual.enrolments, enrolment => enrolment.program.name === 'Child' && isChildCongenitalAnamoly(enrolment) )) )
};
```

<br />

```Text How optimized
Use concept uuid instead of readableValue to compare and check for value only in specific encounter type where the concept was used
```

### 3. Nested Report Cards


Frequently there are cases where across report cards very similar logic is used and only a value used for comparison, changes. Eg: in one of our partner organisations, we load 'Total SAM children' and 'Total MAM children'. For rendering each takes around 20-30s. And hence the dashboard nos doesn't load until both the report card results are calculated and it makes the user to wait for a minute. If the logic is combined, we can render the results in 30s since it would need only retrieval from db and iterating once.\
The above kind of scenarios also lead to code duplication across report cards and when some requirement changes, then the change needs to be done in both.

In-order to handle such scenarios, we recommend using the Nested Report Card. This is a non-standard report card, which has the ability to show upto a maximum of **9** report cards, based on a single Query's response.

The query can return an object with "reportCards" property, which holds within it an array of objets with properties, ` { cardName: 'nested-i', cardColor: '#123456', textColor: '#654321', primaryValue: '20', secondaryValue: '(5%)',  lineListFunction: () => \{/\*Do something\*/} }`. DB instance is passed using the params and useful libraries like lodash and moment are available in the imports parameter of the function.

<br />

```javascript Nested Report Card Query Format
'use strict';
({params, imports}) => {
    /*
    Business logic
    */
    return {reportCards: [
        {
            cardName: 'nested-i',
            cardColor: '#123456',
            textColor: '#654321',
            primaryValue: '20',
            secondaryValue: '(5%)',
            lineListFunction: () => {
                /*Do something*/
            }
        },
        {
            cardName: 'nested-i+1',
            cardColor: '#123456',
            textColor: '#654321',
            primaryValue: '20',
            secondaryValue: '(5%)',
            lineListFunction: () => {
                /*Do something*/
            }
        }
    ]
    }
};
```
```Text Mandatory Fields
- primaryValue
- secondaryValue
- lineListFunction
```
```Text Optional fields
- cardName
- cardColor
- textColor
```

<br />

```javascript Sample Nested Report card Query
// Documentation - https://docs.mongodb.com/realm-legacy/docs/javascript/latest/index.html#queries

'use strict';
({params, imports}) => {
const _ = imports.lodash;
const moment = imports.moment;

const substanceUseDue = (enrolment) => {
    const substanceUseEnc = enrolment.scheduledEncountersOfType('Record Substance use details');
    
    const substanceUse = substanceUseEnc
    .filter((e) => moment().isSameOrAfter(moment(e.earliestVisitDateTime)) && e.cancelDateTime === null && e.encounterDateTime === null );
    
    return substanceUse.length > 0 ? true : false;
    
    };
const indList = params.db.objects('Individual')
        .filtered(`SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Substance use' and $enrolment.programExitDateTime = null and $enrolment.voided = false and SUBQUERY($enrolment.encounters, $encounter, $encounter.encounterType.name = 'Record Substance use details' and $encounter.voided = false ).@count > 0 and voided = false).@count > 0`)
        .filter((individual) => _.some(individual.enrolments, enrolment => substanceUseDue(enrolment)
        )); 
        
const includingVoidedLength = indList.length;
const excludingVoidedLength = 6;  
const llf1 = () => { return params.db.objects('Individual')
        .filtered(`SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Substance use' and $enrolment.programExitDateTime = null and $enrolment.voided = false and SUBQUERY($enrolment.encounters, $encounter, $encounter.encounterType.name = 'Record Substance use details' and $encounter.voided = false ).@count > 0 and voided = false).@count > 0`)
        .filter((individual) => _.some(individual.enrolments, enrolment => substanceUseDue(enrolment)
        ));    
        };
           

return {reportCards: [{
      cardName: 'nested 1',
      textColor: '#bb34ff',
      primaryValue: includingVoidedLength,   
      secondaryValue: null,
      lineListFunction: llf1
  },
  {
      cardName: 'nested 2',
      cardColor: '#ff34ff',
      primaryValue: excludingVoidedLength,   
      secondaryValue: null,
      lineListFunction: () => {return params.db.objects('Individual')
        .filtered(`SUBQUERY(enrolments, $enrolment, $enrolment.program.name = 'Substance use' and $enrolment.programExitDateTime = null and $enrolment.voided = false and SUBQUERY($enrolment.encounters, $encounter, $encounter.encounterType.name = 'Record Substance use details' and $encounter.voided = false ).@count > 0 and voided = false).@count > 0`)
        .filter((individual) => individual.voided === false  && _.some(individual.enrolments, enrolment => substanceUseDue(enrolment)
        ));}
  }]}
};
```

<br />

### Screenshot of Nested Custom Dashboard Report Card Edit screen on Avni Webapp

<Image align="center" src="https://files.readme.io/ecdd996-Screenshot_2024-01-25_at_5.15.20_PM.png" />

### Screenshot of Nested Report Cards in Custom Dashboard in Avni Client

<Image align="center" width="576px" src="https://files.readme.io/dca68e5-Screenshot_2024-01-25_at_5.19.04_PM.png" />

Note: If there is a mismatch between the count of nested report cards configured and the length of reportCards property returned by the query response, then we show an appropriate error message on all Nested Report Cards corresponding to the Custom Report Card.

<Image align="center" width="576px" src="https://files.readme.io/82d8ca0-Screenshot_2024-01-25_at_5.23.56_PM.png" />

<br />

### 4. Custom Design Cards

Custom design cards let you define both the data and the UI for a dashboard card. You provide an HTML template and a data rule; the platform evaluates the rule, passes the result into your template, and renders it in a WebView on the mobile app.

#### How it works

1. **Data rule** — a JavaScript function that queries the device database and returns data
2. **HTML template** — an HTML file that renders the data using `${data.variable}` syntax
3. **Platform glue** — the platform calls your data rule, takes the return value of `lineListFunction()`, and passes it as `data` to your HTML template

<br />

```
Data rule returns:
{
    primaryValue: 10,          ← shown on dashboard tile
    secondaryValue: "(5 new)", ← shown below primaryValue on tile
    cardName: "My Card",       ← overrides tile name (optional)
    cardColor: "#FFE500",      ← overrides tile background (optional)
    textColor: "#222",         ← overrides tile text color (optional)
    lineListFunction: () => {  ← called by platform, result becomes 'data' in HTML
        return {
            total: 10,
            rows: [...]
        };
    }
}

HTML template receives:
    data.total  → 10
    data.rows   → [...]
```

#### Configuration

1. Select **Custom Design Card** as the card type
2. **Data Rule** (optional): A JS rule that returns dynamic data for the HTML template. Input/output follows the same pattern as other card rules — `params.db` provides access to the Realm database, and dashboard filters are available via `params.ruleInput`. If `primaryValue`/`secondaryValue` are returned, they show on the card tile. If `cardName`/`cardColor`/`textColor` are returned, they override defaults. `lineListFunction` should be a function — the platform calls it and passes the return value as `data` to the HTML template. Saving without a data rule is allowed (the HTML renders with an empty `data` object).
3. **HTML File** (required): Upload an HTML file defining the custom layout. Saving without an HTML file shows a validation error.
