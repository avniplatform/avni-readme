---
title: Checklist Requirements Capture Guide
deprecated: false
hidden: false
metadata:
  robots: index
---
Checklists in Avni are scheduled Immunization activities for children. This guide helps you capture complete and accurate business requirements for implementing checklists in Avni.

***

## Understanding Checklist Components

### 1. Checklist Definition

A checklist is a collection of scheduled items that need to be completed within specific timeframes.

**Key Attributes:**

* **Checklist Name**: E.g., "Child Immunization Schedule"
* **Applicable Program/Subject Type**: Which subjects this checklist applies to
* **Trigger Event**: What event starts the checklist (e.g., child birth)

### 2. Checklist Items

Individual items within a checklist that need to be tracked.

**Key Attributes:**

* **Item Name**: E.g., "BCG", "OPV 1"
* **Timing Information**: When the item is due (see timing details below)
* **Dependencies**: Items that must be completed before this one
* **Status Logic**: How the system determines if item is completed, overdue, expired, etc.

***

## Requirement Collection Format

### Basic Format (Simplified)

Use this format for simple checklists without complex dependencies:

| Checklist Item Name | Due by (days) | Critical (days) | Overdue (days) | Expired (days) | Notes                             |
| ------------------- | ------------- | --------------- | -------------- | -------------- | --------------------------------- |
| BCG                 | Birth         | 56              | 365            | 366            | Given at birth                    |
| OPV 0               | Birth         | 7               | 15             | 16             |                                   |
| OPV 1               | 42            | 49              | 365            | 1826           |                                   |
| Penta 1             | 42            | 49              | 365            | 366            |                                   |
| Measles 1/MR 1      | 252-365       | 280             | 365            | 366            | Can be given between 252-365 days |

**Column Definitions:**

* **Due by (days)**: Number of days from the trigger event when the item is due
  * Can be a single value (e.g., `42`) or a range (e.g., `252-365`)
  * Use "Birth" for items due immediately at the trigger event
* **Critical (days)**: Last day when the item can be completed with normal effectiveness
* **Overdue (days)**: Beyond this day, the item is considered overdue but still valid
* **Expired (days)**: After this day, the item is expired and cannot/should not be completed

### Advanced Format (Comprehensive)

Use this format for complex checklists with dependencies, multiple time units, and gaps:

| Name of Vaccines | # Doses of vaccination | Days | Weeks | Months | Start Days at the age of child | Expiry after (days) | Weeks (completed) During the Months | Minimum gap in days from previous vaccination | Dependencies           | Special Notes            |
| ---------------- | ---------------------- | ---- | ----- | ------ | ------------------------------ | ------------------- | ----------------------------------- | --------------------------------------------- | ---------------------- | ------------------------ |
| Polio            | Polio-0                | 0    | 0     | 0      | At birth                       | 15                  |                                     |                                               |                        |                          |
| Polio            | Polio-1                |      |       |        | 42                             |                     | 6                                   | 28                                            | dependent on Polio-0   | 28 days after Polio-0    |
| Polio            | Polio-2                |      |       |        | 70                             |                     | 10                                  | 28                                            | dependent on Polio-1   | 28 days after Polio-1    |
| BCG              | BCG                    | 0    | 0     | 0      | At birth                       | 1095                |                                     |                                               |                        | Within 3 years of age    |
| Measles          | Measles-1              | 274  | 39    | 10     | 270                            |                     | 39                                  | 0                                             |                        |                          |
| Measles          | Measles-2              | 274  | 78    | 20     | 540                            |                     | 78                                  | 180                                           | dependent on Measles-1 | 180 days after Measles-1 |



**Additional Column Definitions:**

* **# Doses**: If the same vaccine/item has multiple doses, specify which dose number
* **Days/Weeks/Months**: Multiple representations of the same timing (helps verification)
* **Weeks (completed) During Months**: For monthly tracking, which week the item should be completed
* **Minimum gap in days from previous vaccination**: Minimum time required between related items
* **Dependencies**: Which checklist items must be completed before this one
* **Special Notes**: Any additional business logic, contraindications, or special handling

***

## Step-by-Step Requirements Gathering

### Step 1: Identify Checklist Type and Scope

**Questions to Ask:**

1. What is the name of this checklist?
2. What subject type does it apply to? (Individual, Household, Group)
3. What program or enrollment triggers this checklist?
4. What event starts the checklist timeline? (e.g., date of birth, date of enrollment, date of diagnosis)

**Example:**

* **Checklist Name**: Child Immunization Schedule
* **Subject Type**: Individual (Child)
* **Program**: Child Health Program
* **Trigger Event**: Date of Birth

### Step 2: List All Checklist Items

**Questions to Ask:**

1. What are all the items that need to be tracked?
2. Are there multiple doses/visits of the same item?
3. What is the official/medical name for each item?
4. Are there alternative names or abbreviations used?

**Example:**

```
- BCG (Bacillus Calmette-Guérin)
- Hepatitis B (Hep B)
- OPV (Oral Polio Vaccine) - Doses: 0, 1, 2, 3, Booster
- Pentavalent (Penta) - Doses: 1, 2, 3
- Rotavirus - Doses: 1, 2, 3
- IPV (Inactivated Polio Vaccine) - Doses: 1, 2
- Measles/MR - Doses: 1, 2
- Vitamin A - Multiple doses
- DPT Booster - Doses: 1, 2
- TT Booster - Doses: 1, 2
```

### Step 3: Define Timing for Each Item

**Questions to Ask for Each Item:**

1. **When is it due?**
   * Days/weeks/months from trigger event?
   * Specific age?
   * Range of acceptable timing?
2. **When does it become critical?**
   * Last day for optimal effectiveness
   * Last day within recommended window
3. **When is it overdue?**
   * When should the system alert that it's late?
   * Is it still valid if overdue?
4. **When does it expire?**
   * After what point should it not be given?
   * Are there medical reasons for expiration?

**Example - OPV 1:**

* **Due**: 42 days after birth (6 weeks)
* **Critical**: 49 days (7 weeks) - last day of recommended window
* **Overdue**: 365 days (1 year) - still valid but late
* **Expired**: 1826 days (5 years) - no longer applicable

### Step 4: Document Dependencies

**Questions to Ask:**

1. Are there items that must be completed before this one?
2. What is the minimum gap required between dependent items?
3. Are there items that must be completed together?
4. Are there alternative dependencies (either A or B must be done first)?

**Example:**

* Polio-1 depends on Polio-0
* Polio-2 depends on Polio-1 (minimum 28 days gap)
* Measles-2 depends on Measles-1 (minimum 180 days gap)
* DPT Booster 1 depends on Penta 3

### Step 5: Capture Business Rules

**Questions to Ask:**

1. Are there conditions when an item should be skipped?
2. Are there alternative items (e.g., Measles OR MR)?
3. How is completion determined? (observation, form submission, external verification)
4. What data should be captured when completing an item?
5. Are there any special calculations or validations?

**Example Rules:**

```javascript
// Skip Vitamin A if child is severely malnourished
if (child.nutritionStatus === 'SAM') {
  skipVitaminA = true;
}

// BCG scar verification after 3 months
if (daysSinceBCG > 90 && !scarVerified) {
  showScarVerificationTask = true;
}

// Calculate next due date based on last completed dose
nextDueDate = lastCompletedDate + minimumGapDays;
```

### Step 6: Define Status Colors and Alerts

**Questions to Ask:**

1. How should each status be displayed to field workers?
2. What alerts/notifications should be triggered?
3. Should there be reminders before due date?

**Example:**

| Status    | Color  | Display           | Alert/Action            |
| --------- | ------ | ----------------- | ----------------------- |
| Upcoming  | Blue   | Due in X days     | No alert                |
| Due       | Green  | Due today         | Show in today's tasks   |
| Critical  | Orange | Due (last day)    | High priority alert     |
| Overdue   | Red    | Overdue by X days | Daily reminder          |
| Expired   | Gray   | Expired           | Remove from active list |
| Completed | Black  | Done on [date]    | No alert                |

***

## Example: Complete Requirements Document

### Checklist: Child Immunization Schedule

**Metadata:**

* **Checklist Name**: Child Immunization Schedule
* **Applicable Subject Type**: Individual (Child)
* **Applicable Program**: Child Health Program
* **Trigger Event**: Date of Birth (DOB)
* **Timeline Unit**: Days from DOB
* **Active Duration**: 0-7000 days (approximately 19 years)

**Checklist Items:**

| Vaccine Name   | Due by (days) | Critical (days) | Overdue (days) | Expired (days) | Min Gap (days) | Dependencies  | Notes                             |
| -------------- | ------------- | --------------- | -------------- | -------------- | -------------- | ------------- | --------------------------------- |
| BCG            | Birth (0)     | 56              | 365            | 366            | -              | None          | Given at birth or within 2 months |
| Hep B          | Birth (0)     | 1               | 2              | 10             | -              | None          | Must be given within 24 hours     |
| OPV 0          | Birth (0)     | 7               | 15             | 16             | -              | None          | Birth dose                        |
| OPV 1          | 42            | 49              | 365            | 1826           | 28             | OPV 0         | 28 days after OPV 0               |
| OPV 2          | 70            | 77              | 365            | 1826           | 28             | OPV 1         | 28 days after OPV 1               |
| OPV 3          | 98            | 105             | 365            | 1826           | 28             | OPV 2         | 28 days after OPV 2               |
| Penta 1        | 42            | 49              | 365            | 366            | -              | None          | Can be given with OPV 1           |
| Penta 2        | 70            | 77              | 365            | 366            | 28             | Penta 1       | 28 days after Penta 1             |
| Penta 3        | 98            | 105             | 365            | 366            | 28             | Penta 2       | 28 days after Penta 2             |
| Rotavirus 1    | 42            | 49              | 365            | 366            | -              | None          | Given with Penta 1                |
| Rotavirus 2    | 70            | 77              | 365            | 366            | 28             | Rotavirus 1   | Given with Penta 2                |
| Rotavirus 3    | 98            | 105             | 365            | 366            | 28             | Rotavirus 2   | Given with Penta 3                |
| IPV 1          | 42            | 49              | 365            | 366            | -              | None          | Given with Penta 1                |
| IPV 2          | 98            | 105             | 365            | 366            | 56             | IPV 1         | Given with Penta 3                |
| Measles 1/MR 1 | 252-365       | 280             | 365            | 366            | -              | None          | Between 9-12 months               |
| Measles 2/MR 2 | 448-730       | 504             | 730            | 731            | 180            | Measles 1     | Between 16-24 months              |
| Vit. A         | 252           | 280             | 1825           | 1826           | -              | None          | Every 6 months after 9 months     |
| DPT Booster 1  | 448-730       | 504             | 730            | 731            | -              | Penta 3       | Between 16-24 months              |
| DPT Booster 2  | 1825          | 2190            | 2191           | 2500           | -              | DPT Booster 1 | At 5-6 years                      |
| OPV Booster    | 448           | 504             | 1825           | 1826           | -              | OPV 3         | At 16-18 months                   |
| TT Booster 1   | 3650          | 5475            | 6570           | 7000           | -              | DPT Booster 2 | At 10 years                       |
| TT Booster 2   | 5840          | 7300            | 8760           | 9000           | -              | TT Booster 1  | At 16 years                       |

**Business Rules:**

1. **Completion Logic:**
   * Item is marked complete when the corresponding encounter form is submitted
   * Completion date is the date of vaccine administration (not form submission date)

2. **Alternative Vaccines:**
   * Measles and MR are alternatives - completing either satisfies the requirement
   * If MR is given, mark it as Measles 1 or Measles 2 as appropriate

3. **Vitamin A Recurring:**
   * After initial dose at 9 months, Vitamin A should be given every 6 months
   * Create recurring checklist items or handle via rules

4. **Late Vaccination Protocol:**
   * If OPV 1 is given after expiry, don't create subsequent OPV items
   * If Measles 1 is given after 365 days, adjust Measles 2 due date accordingly

5. **Scar Verification:**
   * BCG scar should be verified 3 months after vaccination
   * If no scar is visible, refer to health facility for re-vaccination

6. **Concurrent Administration:**
   * Multiple vaccines can be given on the same day if they are due
   * Document all vaccines given in a single visit

***

## Data Format for Upload

### Checklist Configuration JSON Structure

Based on the Avni checklist format, your requirements will be translated into JSON like this:

```json
{
  "name": "Child Immunization Schedule",
  "uuid": "unique-uuid-here",
  "items": [
    {
      "name": "BCG",
      "uuid": "item-uuid-1",
      "scheduleOnExpiryOfDependency": false,
      "minDaysFromStartDate": 0,
      "maxDaysFromStartDate": 56,
      "expiresAfter": 366,
      "dependentOn": null
    },
    {
      "name": "OPV 1",
      "uuid": "item-uuid-4",
      "scheduleOnExpiryOfDependency": false,
      "minDaysFromStartDate": 42,
      "maxDaysFromStartDate": 49,
      "expiresAfter": 1826,
      "dependentOn": "OPV 0"
    }
  ]
}
```

### Concept Definitions

Vaccines also need to be defined as concepts:

```json
{
  "name": "BCG",
  "uuid": "concept-uuid-1",
  "dataType": "Coded",
  "answers": [
    {
      "name": "Given",
      "uuid": "answer-uuid-1"
    },
    {
      "name": "Not Given",
      "uuid": "answer-uuid-2"
    }
  ]
}
```

***

## Validation Checklist

Before finalizing requirements, verify:

* [ ] All checklist items are listed with unique names
* [ ] All timing values (due, critical, overdue, expired) are defined
* [ ] Dependencies between items are clearly documented
* [ ] Minimum gaps between dependent items are specified
* [ ] Alternative items (if any) are identified
* [ ] Business rules for special cases are documented
* [ ] Completion criteria for each item is defined
* [ ] Status colors and alerts are specified
* [ ] Any recurring items are handled (separate checklist or rules)
* [ ] Edge cases and late administration scenarios are covered
* [ ] Data to be collected on completion is defined
* [ ] Integration with other program schedules is considered

***

## Common Patterns and Best Practices

### 1. Use Clear Naming Conventions

* **Good**: "OPV 1", "OPV 2", "OPV 3"
* **Avoid**: "OPV First Dose", "Second OPV", "OPV III"

### 2. Document Ranges Explicitly

* If an item can be given in a range (e.g., 252-365 days), specify:
  * Earliest acceptable date (252)
  * Latest optimal date (365)
  * Overdue cutoff
  * Expired cutoff

### 3. Handle Dependent Chains

* For chains like OPV 0 → OPV 1 → OPV 2 → OPV 3:
  * Each item depends on the previous one
  * Specify minimum gaps between each
  * Consider what happens if the chain is broken (late vaccination)

### 4. Account for Real-World Scenarios

* What if a child misses the window for a vaccine?
* What if a child comes for catch-up vaccination?
* What if vaccines are given in a different order?
* What if there are stock-outs or unavailability?

### 5. Recurring Items

* For items like Vitamin A (every 6 months):
  * Option 1: Create multiple checklist items (Vit A 1, Vit A 2, etc.)
  * Option 2: Use rules to create new checklist items after completion
  * Document which approach is preferred

***

## Additional Resources

* **Avni Checklist Documentation**: [https://avni.readme.io/docs/upload-checklist](https://avni.readme.io/docs/upload-checklist)
* **Checklist Rules Guide**: [https://avni.readme.io/docs/writing-rules#/9-checklists-rule](https://avni.readme.io/docs/writing-rules#/9-checklists-rule)
* **Example Vaccination Concepts**: [https://github.com/avniproject/avni-health-modules/blob/master/src/health\_modules/child/metadata/vaccinationConcepts.json](https://github.com/avniproject/avni-health-modules/blob/master/src/health_modules/child/metadata/vaccinationConcepts.json)
* **Example Checklist JSON**: [https://github.com/avniproject/calcutta-kids/blob/master/child/checklist.json](https://github.com/avniproject/calcutta-kids/blob/master/child/checklist.json)

***

## Template for Stakeholder Discussions

Use this template when meeting with program managers, doctors, or other stakeholders:

```
Meeting Date: __________
Stakeholders Present: __________

1. Checklist Overview
   - Name: __________
   - Purpose: __________
   - Applies to: __________
   - Starts when: __________

2. List of Items (one per row)
   Item Name | Due Date | Critical Date | Overdue Date | Expired Date | Dependencies

3. Special Rules or Conditions
   - 
   - 
   - 

4. Questions/Clarifications Needed
   - 
   - 
   - 

5. Next Steps
   - 
   - 
```

***
