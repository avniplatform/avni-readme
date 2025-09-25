---
title: Worklist Configuration
deprecated: false
hidden: false
metadata:
  title: Worklist Configuration
  robots: index
---
## Overview

Worklists in Avni are a powerful feature that enables **sequential form workflows** by automatically chaining multiple forms together. When a user completes one form, the system can automatically present the next form in the sequence, creating smooth data collection workflows for field workers.

## Table of Contents

1. [What are Worklists?](#what-are-worklists)
2. [For Newcomers](#for-newcomers)
3. [Self-Service Configuration](#self-service-configuration)
4. [Advanced Implementation Guide](#advanced-implementation-guide)
5. [Limitations and Gotchas](#limitations-and-gotchas)
6. [Troubleshooting](#troubleshooting)

***

## What are Worklists?

A **Worklist** is a sequence of forms that are presented to the user one after another. Each item in the worklist is called a **WorkItem**. When a user completes a form, Avni can automatically:

* Navigate to the next form in the sequence
* Pass context/data between forms
* Show appropriate "Save and Proceed" buttons
* Handle different types of forms (Registration, Encounters, Program Enrolment, etc.)

### Key Components

* **WorkList**: Container holding multiple WorkItems
* **WorkItem**: Individual form/task in the sequence
* **WorkItem Types**: REGISTRATION, ENCOUNTER, PROGRAM_ENCOUNTER, PROGRAM_ENROLMENT, PROGRAM_EXIT, ADD_MEMBER, HOUSEHOLD, REMOVE_MEMBER, CANCELLED_ENCOUNTER
* **Worklist Updation Rule**: JavaScript code that modifies the worklist dynamically

***

## Basic Concept

Think of a worklist as a **to-do list for forms**. Instead of manually navigating between forms, the system automatically takes you to the next form when you complete the current one.

### Simple Example: Family Registration + Health Survey

**Scenario**: When registering a new family member, you want to automatically conduct a health survey.

**What happens**:

1. User starts family member registration
2. User fills registration form and clicks "Save and Proceed"
3. System automatically opens health survey form
4. User completes survey and saves
5. User returns to dashboard

**Benefits**:

* Reduces navigation time
* Ensures data collection completeness
* Improves user experience
* Maintains data consistency

### When to Use Worklists

✅ **Good Use Cases**:

* Registration followed by baseline survey
* Multi-step enrollment processes
* Sequential health checkups
* Family member registration workflows
* Program enrollment with immediate first encounter

❌ **Not Suitable For**:

* Independent forms that don't need to be linked
* Complex branching workflows (use form rules instead)
* Forms that require external data validation between steps

***

## Writing Worklist Updation Rules

Worklist Updation Rules are JavaScript functions that modify worklists dynamically based on form data, user context, or business logic.

#### Rule Structure

```javascript
({params, imports}) => {
    const workLists = params.workLists;
    const context = params.context;
    const WorkItem = imports.models.WorkItem;
    
    // Your logic here
    
    return workLists; // Always return modified workLists
}
```

#### Available Parameters

```javascript
// params object contains:
{
    workLists: WorkLists,     // Current worklist container
    context: {                // Form completion context
        entity: Individual,   // The entity that was just saved
        // Additional context based on form type
    }
}

// imports object contains:
{
    models: {                 // Avni model classes
        WorkItem,
        WorkList,
        WorkLists,
        Individual,
        // ... other models
    },
    rulesConfig,             // Rule configuration utilities
    common,                  // Common utility functions
    lodash,                  // Lodash library
    moment,                  // Moment.js for dates
    globalFn                 // Global rule functions
}
```

### WorkItem Types and Parameters

#### 1. REGISTRATION

```javascript
new WorkItem(uuid, WorkItem.type.REGISTRATION, {
    subjectTypeName: "Individual",  // Required
    uuid: existingSubjectUuid      // Optional: for editing
    // Note: subjectUUID is NOT required for REGISTRATION
})
```

#### 2. ENCOUNTER

```javascript
new WorkItem(uuid, WorkItem.type.ENCOUNTER, {
    encounterType: "Health Survey",  // Required
    subjectUUID: individual.uuid,    // Required
    uuid: existingEncounterUuid     // Optional: for editing
})
```

#### 3. PROGRAM_ENCOUNTER

```javascript
new WorkItem(uuid, WorkItem.type.PROGRAM_ENCOUNTER, {
    encounterType: "ANC Visit",              // Required
    programEnrolmentUUID: enrolment.uuid,    // Optional
    subjectUUID: individual.uuid,            // Required
    name: "ANC 1",                          // Optional: encounter name
    uuid: existingEncounterUuid             // Optional: for editing
})
```

#### 4. PROGRAM_ENROLMENT

```javascript
new WorkItem(uuid, WorkItem.type.PROGRAM_ENROLMENT, {
    programName: "Maternal Health",  // Required
    subjectUUID: individual.uuid     // Required
})
```

#### 5. PROGRAM_EXIT

```javascript
new WorkItem(uuid, WorkItem.type.PROGRAM_EXIT, {
    programName: "Maternal Health",     // Required
    programEnrolmentUUID: enrolment.uuid, // Optional
    subjectUUID: individual.uuid        // Required
})
```

#### 6. ADD_MEMBER

```javascript
new WorkItem(uuid, WorkItem.type.ADD_MEMBER, {
    uuid: existingMemberUuid,           // Optional: for editing
    groupSubjectUUID: household.uuid,   // Required
    subjectTypeName: "Individual",      // Required
    member: memberObject,               // Required
    individualRelative: relativeObject, // Required
    headOfHousehold: false,             // Required
    relativeGender: gender              // Required
    // Note: subjectUUID is NOT required for ADD_MEMBER
})
```

#### 7. HOUSEHOLD

```javascript
new WorkItem(uuid, WorkItem.type.HOUSEHOLD, {
    saveAndProceedLabel: 'saveAndAddMember', // Required
    household: '2 of 5',                     // Required
    headOfHousehold: false,                  // Required
    currentMember: 2,                        // Required
    groupSubjectUUID: household.uuid,        // Required
    message: 'newMemberAddedMsg',           // Required
    totalMembers: 5                         // Required
    // Note: subjectUUID is NOT required for HOUSEHOLD
})
```

#### 8. REMOVE_MEMBER

```javascript
new WorkItem(uuid, WorkItem.type.REMOVE_MEMBER, {
    groupSubjectUUID: household.uuid,  // Required
    memberSubjectUUID: member.uuid,    // Optional
    subjectTypeName: "Individual"      // Optional
})
```

#### 9. CANCELLED_ENCOUNTER

```javascript
new WorkItem(uuid, WorkItem.type.CANCELLED_ENCOUNTER, {
    encounterType: "ANC Visit",              // Required
    programEnrolmentUUID: enrolment.uuid,    // Optional: for program encounters
    subjectUUID: individual.uuid,            // Required
    uuid: existingEncounterUuid             // Required: for cancellation
})
```

### WorkItem Validation Rules

Each WorkItem type has specific validation requirements enforced by the system:

#### Required Parameters by Type:

* **REGISTRATION**: `subjectTypeName`
* **ENCOUNTER**: `encounterType`, `subjectUUID`
* **PROGRAM_ENCOUNTER**: `encounterType`, `subjectUUID`
* **PROGRAM_ENROLMENT**: `programName`, `subjectUUID`
* **PROGRAM_EXIT**: `programName`, `subjectUUID`
* **ADD_MEMBER**: `groupSubjectUUID` (subjectUUID NOT required)
* **HOUSEHOLD**: No subjectUUID required
* **REMOVE_MEMBER**: `groupSubjectUUID`
* **CANCELLED_ENCOUNTER**: `encounterType`, `subjectUUID`

#### Special Validation Rules:

* `subjectUUID` is **NOT required** for: `REGISTRATION`, `ADD_MEMBER`, `HOUSEHOLD`
* All other WorkItem types require `subjectUUID`
* Missing required parameters will cause validation errors during WorkItem creation

### Available API Methods

#### WorkLists Methods

```javascript
// Get current WorkItem
const currentItem = workLists.getCurrentWorkItem();

// Add WorkItems to current WorkList (Recommended approach)
workLists.addItemsToCurrentWorkList(workItem1, workItem2, workItem3);

// Preview next WorkItem without moving
const nextItem = workLists.peekNextWorkItem();

// Add parameters to current WorkItem
workLists.addParamsToCurrentWorkList({
    additionalData: 'value'
});
```

#### WorkList Methods

```javascript
// Find WorkItem by ID
const workItem = currentWorkList.findWorkItem(workItemId);

// Find WorkItem index (useful for insertion)
const index = currentWorkList.findWorkItemIndex(workItemId);

// Add multiple WorkItems
currentWorkList.addWorkItems(workItem1, workItem2);

// Get next WorkItem
const next = currentWorkList.nextWorkItem();
```

### Real-World Examples

#### Example 1: Registration + Health Survey (Recommended API)

```javascript
({params, imports}) => {
    const workLists = params.workLists;
    const context = params.context;
    const WorkItem = imports.models.WorkItem;
    const _ = imports.lodash;
    
    // Add health survey after individual registration
    if (_.get(context, 'entity.individual.subjectType.name') === 'Individual') {
        const healthSurvey = new WorkItem(
            imports.common.randomUUID(), 
            WorkItem.type.ENCOUNTER,
            {
                encounterType: 'Health Survey',
                subjectUUID: _.get(context, 'entity.individual.uuid')
            }
        );
        
        // RECOMMENDED: Use the proper API
        workLists.addItemsToCurrentWorkList(healthSurvey);
    }
    
    return workLists;
}
```

#### Example 2: Age-Based Conditional Workflows

```javascript
({params, imports}) => {
    const workLists = params.workLists;
    const context = params.context;
    const WorkItem = imports.models.WorkItem;
    const _ = imports.lodash;
    
    const individual = _.get(context, 'entity.individual');
    const age = individual.age;
    
    if (individual.subjectType.name === 'Individual') {
        let encounterType;
        
        // Different surveys based on age
        if (age < 5) {
            encounterType = 'Child Health Survey';
        } else if (age >= 5 && age < 18) {
            encounterType = 'Adolescent Health Survey';
        } else {
            encounterType = 'Adult Health Survey';
        }
        
        const survey = new WorkItem(
            imports.common.randomUUID(),
            WorkItem.type.ENCOUNTER,
            {
                encounterType: encounterType,
                subjectUUID: individual.uuid
            }
        );
        
        // RECOMMENDED: Use the proper API
        workLists.addItemsToCurrentWorkList(survey);
    }
    
    return workLists;
}
```

#### Example 3: Program Enrollment with Immediate First Visit

```javascript
({params, imports}) => {
    const workLists = params.workLists;
    const context = params.context;
    const WorkItem = imports.models.WorkItem;
    const _ = imports.lodash;
    
    const enrolment = _.get(context, 'entity');
    
    // After ANC program enrollment, add first ANC visit
    if (enrolment && enrolment.program.name === 'Maternal Health') {
        const firstVisit = new WorkItem(
            imports.common.randomUUID(),
            WorkItem.type.PROGRAM_ENCOUNTER,
            {
                encounterType: 'ANC Visit',
                programEnrolmentUUID: enrolment.uuid,
                subjectUUID: enrolment.individual.uuid,
                name: 'ANC 1'
            }
        );
        
        // RECOMMENDED: Use the proper API
        workLists.addItemsToCurrentWorkList(firstVisit);
    }
    
    return workLists;
}
```

#### Example 4: Household Member Addition

```javascript
({params, imports}) => {
    const workLists = params.workLists;
    const context = params.context;
    const WorkItem = imports.models.WorkItem;
    const _ = imports.lodash;
    
    const individual = _.get(context, 'entity.individual');
    
    // For household head registration, add member addition workflows
    if (individual.subjectType.isHousehold()) {
        const totalMembers = _.get(context, 'totalMembers', 3); // Default 3 members
        
        for (let i = 2; i <= totalMembers; i++) {
            const addMember = new WorkItem(
                imports.common.randomUUID(),
                WorkItem.type.ADD_MEMBER,
                {
                    groupSubjectUUID: individual.uuid,
                    subjectTypeName: 'Individual',
                    household: `${i} of ${totalMembers}`,
                    headOfHousehold: false,
                    currentMember: i,
                    totalMembers: totalMembers,
                    saveAndProceedLabel: 'saveAndAddMember',
                    message: 'newMemberAddedMsg'
                }
            );
            
            // RECOMMENDED: Use the proper API
            workLists.addItemsToCurrentWorkList(addMember);
        }
    }
    
    return workLists;
}
```

#### Example 5: Complex Conditional Workflow (Advanced Pattern)

```javascript
({params, imports}) => {
    const workLists = params.workLists;
    const context = params.context;
    const WorkItem = imports.models.WorkItem;
    const _ = imports.lodash;
    
    const addChildRegistrationWorkflow = () => {
        const currentWorkList = workLists.currentWorkList;
        
        // Find position after current WorkItem for insertion
        const currentWorkItem = workLists.getCurrentWorkItem();
        const splicePosition = currentWorkList.findWorkItemIndex(currentWorkItem.id) + 1;
        
        // Insert child registration and enrollment
        currentWorkList.workItems.splice(
            splicePosition,
            0,
            new WorkItem(imports.common.randomUUID(), WorkItem.type.REGISTRATION, {
                subjectTypeName: "Individual",
                saveAndProceedLabel: "registerAChild"
            }),
            new WorkItem(imports.common.randomUUID(), WorkItem.type.PROGRAM_ENROLMENT, {
                programName: "Child"
            })
        );
    };
    
    const reorderPNCEncounter = () => {
        const workItems = workLists.currentWorkList.workItems;
        const pncIndex = workItems.findIndex(item => 
            item.parameters.encounterType === "PNC"
        );
        const neonatalIndex = workItems.findIndex(item => 
            item.parameters.encounterType === "Neonatal"
        );
        
        if (pncIndex !== -1 && neonatalIndex !== -1) {
            // Move PNC encounter before Neonatal encounter
            const [pncWorkItem] = workItems.splice(pncIndex, 1);
            const newNeonatalIndex = workItems.findIndex(item => 
                item.parameters.encounterType === "Neonatal"
            );
            workItems.splice(newNeonatalIndex, 0, pncWorkItem);
        }
    };
    
    // Main workflow logic
    const currentWorkItem = workLists.getCurrentWorkItem();
    
    if (currentWorkItem.type === WorkItem.type.PROGRAM_ENCOUNTER) {
        switch (currentWorkItem.parameters.encounterType) {
            case "Delivery": {
                addChildRegistrationWorkflow();
                break;
            }
            case "Monthly needs assessment": {
                // Add conditional enrollment based on assessment
                const programEncounter = context.entity;
                const isPregnant = _.some(programEncounter.observations, 
                    obs => obs.concept.name === "Whether currently pregnant" && 
                           obs.getValue() === "Yes"
                );
                
                if (isPregnant) {
                    workLists.addItemsToCurrentWorkList(
                        new WorkItem(imports.common.randomUUID(), 
                            WorkItem.type.PROGRAM_ENROLMENT, {
                                programName: "Mother",
                                subjectUUID: programEncounter.programEnrolment.individual.uuid
                            }
                        )
                    );
                }
                break;
            }
        }
    }
    
    if (currentWorkItem.type === WorkItem.type.PROGRAM_ENROLMENT) {
        if (currentWorkItem.parameters.programName === "Child") {
            reorderPNCEncounter();
        }
    }
    
    return workLists;
}
```

### WorkItem Management Approaches

#### Method 1: Using WorkLists API (Recommended)

```javascript
// This is the preferred approach used in the codebase
workLists.addItemsToCurrentWorkList(
    new WorkItem(imports.common.randomUUID(), WorkItem.type.ENCOUNTER, {
        encounterType: 'Health Survey',
        subjectUUID: individual.uuid
    })
);
```

#### Method 2: Direct Array Manipulation

```javascript
// This also works but is more manual
const totalItems = workLists.currentWorkList.workItems.length;
workLists.currentWorkList.workItems.splice(totalItems - 1, 0, newWorkItem);

// Or append to end
workLists.currentWorkList.workItems.push(newWorkItem);
```

#### Method 3: Using WorkList API

```javascript
// For direct WorkList manipulation
const currentWorkList = workLists.currentWorkList;
currentWorkList.addWorkItems(workItem1, workItem2, workItem3);
```

#### Method 4: Precise Insertion Using findWorkItemIndex

```javascript
// Insert at specific position (advanced pattern)
const currentWorkItem = workLists.getCurrentWorkItem();
const insertPosition = workLists.currentWorkList.findWorkItemIndex(currentWorkItem.id) + 1;
workLists.currentWorkList.workItems.splice(insertPosition, 0, newWorkItem);
```

### Advanced Patterns

#### 1. Conditional Worklist Items

```javascript
// Add items based on form responses
const responses = individual.observations;
const hasDisease = responses.some(obs => 
    obs.concept.name === 'Disease Status' && obs.getValue() === 'Positive'
);

if (hasDisease) {
    // Add follow-up encounter
}
```

#### 2. Dynamic Parameters

```javascript
// Pass data between forms
const workItem = new WorkItem(uuid, type, {
    encounterType: 'Follow-up',
    subjectUUID: individual.uuid,
    previousVisitData: {
        weight: getObservationValue(individual, 'Weight'),
        height: getObservationValue(individual, 'Height')
    }
});
```

#### 3. Worklist Modification

```javascript
// Remove items conditionally
workLists.currentWorkList.workItems = workLists.currentWorkList.workItems
    .filter(item => shouldKeepItem(item));

// Reorder items
workLists.currentWorkList.workItems.sort((a, b) => 
    getPriority(a) - getPriority(b)
);
```

***

## Limitations and Gotchas

### What's NOT Possible

#### ❌ Multiple Simultaneous Buttons

```javascript
// This is NOT supported:
// - Show 2 buttons: "Save and Go to Survey A" and "Save and Go to Survey B"
// - User cannot choose between different next forms
```

**Why**: Worklists are linear sequences. For branching logic, use form element rules or decision rules instead.

**Alternative**: Use form element rules to show/hide different form sections, or decision rules to determine the next form programmatically.

#### ❌ Hiding Save Button

```javascript
// This is NOT supported:
// - Hide the "Save" button and only show "Save and Proceed"
// - Force users to continue the workflow
```

**Why**: Users must always have the option to save and exit the workflow.

**Alternative**: Use validation rules to encourage completion, or show clear messaging about the workflow benefits.

#### ❌ Complex Branching Workflows

```javascript
// This is NOT supported:
// - If condition A, go to Form X, else if condition B, go to Form Y, else go to Form Z
// - Multiple parallel workflows
```

**Why**: Worklists are designed for sequential, not branching workflows.

**Alternative**: Use multiple worklist rules with different conditions, or implement branching logic in form element rules.

#### ❌ Cross-Subject Worklists

```javascript
// This is NOT supported:
// - Register Individual A, then register Individual B
// - Workflows that span multiple subjects
```

**Why**: Worklists are tied to a single subject context.

**Alternative**: Can be used with household/group subject types and their Member Subject Types, or separate worklists for each subject.

#### ❌ Async/External API Calls

```javascript
// This is NOT supported in worklist rules:
// - await fetch('/api/external-data')
// - Database queries to external systems
// - Long-running operations
```

**Why**: Worklist rules must execute synchronously and quickly.

**Alternative**: Use validation rules for async operations, or pre-fetch data during sync.

### Technical Limitations

#### 1. Rule Execution Context

* Worklist rules execute **after** form completion
* Cannot modify the current form being filled
* Cannot prevent form submission
* Limited to modifying the upcoming workflow

#### 2. Performance Considerations

* Rules should execute quickly (\< 100ms)
* Avoid complex calculations or large data processing
* Be mindful of memory usage on mobile devices

#### 3. Error Handling

```javascript
// Always wrap risky operations
try {
    // Your worklist logic
} catch (error) {
    console.log('Worklist rule error:', error);
    // Return original workLists to prevent crashes
    return params.workLists;
}
```

#### 4. Data Availability

* Only data from the just-completed form is guaranteed to be available
* Related entity data might not be fully synced
* Use defensive programming with null checks

### Common Gotchas

#### 1. WorkItem Insertion Position

```javascript
// WRONG: This adds to the end, after the "continue registration" item
workLists.currentWorkList.workItems.push(newItem);

// BETTER: Insert before the last item
const totalItems = workLists.currentWorkList.workItems.length;
workLists.currentWorkList.workItems.splice(totalItems - 1, 0, newItem);

// BEST: Use the proper API (recommended)
workLists.addItemsToCurrentWorkList(newItem);
```

#### 2. UUID Generation

```javascript
// WRONG: Using Date or Math.random
const uuid = new Date().toString();

// RIGHT: Using Avni's UUID generator
const uuid = imports.common.randomUUID();
```

#### 3. Context Entity Access

```javascript
// WRONG: Assuming entity structure
const name = context.entity.name;

// RIGHT: Defensive access
const name = _.get(context, 'entity.individual.name', 'Unknown');
```

#### 4. WorkItem Parameters

```javascript
// WRONG: Missing required parameters
new WorkItem(uuid, WorkItem.type.ENCOUNTER, {
    encounterType: 'Survey'
    // Missing subjectUUID!
});

// RIGHT: All required parameters
new WorkItem(uuid, WorkItem.type.ENCOUNTER, {
    encounterType: 'Survey',
    subjectUUID: individual.uuid
});
```

#### 5. Rule Return Value

```javascript
// WRONG: Forgetting to return
({params, imports}) => {
    const workLists = params.workLists;
    // ... modify workLists
    // Missing return statement!
}

// RIGHT: Always return workLists
({params, imports}) => {
    const workLists = params.workLists;
    // ... modify workLists
    return workLists;
}
```

***

## Troubleshooting

### Common Issues

#### 1. Worklist Rule Not Executing

**Symptoms**: Rule seems to be ignored, no new items added to worklist

**Possible Causes**:

* JavaScript syntax errors in the rule
* Rule not saved properly in Organisation Configuration
* Missing return statement
* Exception thrown during execution

**Debug Steps**:

```javascript
({params, imports}) => {
    console.log('Worklist rule executing');
    console.log('Context:', params.context);
    console.log('Current workLists:', params.workLists);
    
    try {
        // Your rule logic here
        const result = params.workLists;
        console.log('Returning workLists:', result);
        return result;
    } catch (error) {
        console.log('Rule error:', error);
        return params.workLists;
    }
}
```

#### 2. Wrong Form Opens Next

**Symptoms**: Unexpected form opens after completing current form

**Possible Causes**:

* Incorrect WorkItem type
* Wrong parameters in WorkItem
* Form mapping issues
* Multiple rules conflicting

**Debug Steps**:

* Check WorkItem type matches intended form type
* Verify encounterType/programName parameters
* Check form mappings in admin interface
* Review all active worklist rules

#### 3. "Save and Proceed" Button Missing

**Symptoms**: Only "Save" button visible, no workflow continuation

**Possible Causes**:

* No next WorkItem in the worklist
* WorkItem has invalid parameters
* Form mapping not configured
* User lacks permissions for next form

**Debug Steps**:

* Check if worklist has remaining items
* Verify WorkItem parameters are correct
* Check user permissions for next form type
* Review form mappings

#### 4. App Crashes During Workflow

**Symptoms**: App crashes when proceeding to next form

**Possible Causes**:

* Invalid WorkItem parameters
* Missing required entities (subject, program, etc.)
* Memory issues with large worklists
* Null reference errors

**Debug Steps**:

* Add try-catch blocks in worklist rules
* Validate all WorkItem parameters
* Check entity existence before creating WorkItems
* Test with smaller worklists

#### 5. WorkItem Validation Errors

**Symptoms**: "Work item must be one of WorkItem.type" or field validation errors

**Possible Causes**:

* Invalid WorkItem type
* Missing required parameters for the WorkItem type
* Incorrect parameter names
* Using hardcoded UUIDs incorrectly

**Debug Steps**:

```javascript
// Validate WorkItem before adding
try {
    const workItem = new WorkItem(
        imports.common.randomUUID(), 
        WorkItem.type.ENCOUNTER, 
        {
            encounterType: 'Health Survey',
            subjectUUID: individual.uuid
        }
    );
    
    // This will throw if invalid
    workItem.validate();
    
    workLists.addItemsToCurrentWorkList(workItem);
} catch (error) {
    console.log('WorkItem validation failed:', error.message);
    console.log('WorkItem details:', {
        type: WorkItem.type.ENCOUNTER,
        parameters: workItem.parameters
    });
    
    // Don't add invalid WorkItem
    return params.workLists;
}
```

**Common Validation Issues**:

* Missing `subjectUUID` for ENCOUNTER, PROGRAM_ENCOUNTER types
* Missing `encounterType` for encounter-based WorkItems
* Missing `programName` for PROGRAM_ENROLMENT, PROGRAM_EXIT
* Missing `groupSubjectUUID` for REMOVE_MEMBER
* Providing `subjectUUID` for REGISTRATION, ADD_MEMBER, HOUSEHOLD (not required)

## Best Practices Summary

### ✅ Do's

* Always return the workLists object
* Use `workLists.addItemsToCurrentWorkList()` for adding WorkItems (recommended API)
* Use `workLists.getCurrentWorkItem()` to get current WorkItem
* Use `imports.common.randomUUID()` for generating WorkItem IDs
* Validate WorkItems using `workItem.validate()` before adding
* Use defensive programming with null checks (`_.get()`)
* Add comprehensive error handling with try-catch blocks
* Test rules thoroughly before deployment
* Use descriptive WorkItem parameters with comments
* Keep rules simple and focused
* Add logging for debugging
* Consider offline scenarios and mobile constraints
* Follow Avni's error handling patterns (rethrow in views)

### ❌ Don'ts

* Don't use hardcoded UUIDs (use `imports.common.randomUUID()`)
* Don't create complex branching logic in worklist rules
* Don't make external API calls in rules
* Don't assume entity data structure without null checks
* Don't forget error handling
* Don't create circular worklist dependencies
* Don't ignore performance implications on mobile devices
* Don't modify entities in worklist rules
* Don't create infinite worklist loops
* Don't provide `subjectUUID` for REGISTRATION, ADD_MEMBER, HOUSEHOLD WorkItems
* Don't forget required parameters (use validation rules as reference)

### 🎯 Key Takeaways

1. **Worklists are for sequential workflows**, not complex branching
2. **Rules execute after form completion**, not during
3. **Always handle errors gracefully** to prevent app crashes
4. **Test extensively** with different scenarios and data
5. **Keep rules simple** for maintainability and performance