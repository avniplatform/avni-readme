---
title: Visit scheduling
excerpt: ''
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Terminology

**Scheduled Visit** - A visit which is scheduled but not completed.\
**Planned Visit** - A visit which was scheduled and was completed.\
**Unplanned Visit** - A completed visit which was not scheduled.\
**Canceled Visit** - A visit that was canceled.

## Scenarios

1. The user performs a planned visit - leading to a visit (of type within the group) getting scheduled after it
2. User edits a planned visit
3. The user performs a canceled visit (or cancels a scheduled visit)
4. User edits a canceled visit
5. The user performs an unplanned visit before the newest scheduled visit
6. User edits an unplanned visit before the newest scheduled visit
7. The user performs an unplanned visit after the newest scheduled visit
8. The user edits an unplanned visit that is after the newest scheduled visit
9. The user performs an unplanned visit when (because of defect) there is no scheduled visit

## Requirement analysis

* Only **one** visit of a type, can be scheduled at a time (instead of 2 or more). What also follows is that the scheduled visit is always the newest among the scheduled and planned visits for a type, also that cancelation can only happen of the newest visits.
* Cancelation of visits, unless leading to the exit of the person from the program, result in next visit being scheduled. Hence as far visit scheduling is concerned the occurrence of a canceled visit may be same as an occurrence of a planned visit. But there will be exceptions too.
* Potentially the visit scheduling logic could get simpler if we disregard what ACTUAL REAL time is. Which is to say that code can assume *today = encounter(enrolment) date*, making the REAL NOW completely irrelevant :-). Hence all references to past and future can be considered to be only wrt encounter(enrolment) date and not to the REAL NOW.
* Let's assume a simple monthly visit scenario. In such a case lets say if a March's visit is performed in April, should a visit for April be scheduled or not? If yes, then the identity of the visit, in this case, is MARCH (i.e. it is the MARCH's visit was performed in April). If no, and visit for MAY should be scheduled, then the identity of this visit is APRIL (i.e. identity is derived from **when** the visit happens). It appears to be a fundamental question without answering which we cannot write our logic for visit scheduling. But we are not asking this question to our customers hence this remains a matter of guesses for later. Also, let's call these two by names - SCHEDULED DATE BASED PLAN (SDBP) and ACTUAL DATE BASED PLAN (ADBP). It is possible that within a group of visit types, the strategy may vary hence that must also be explored and captured.
* Does the implementation requires unplanned visits. If yes (which is likely to be rare), then we could create a separate visit type for such purpose using the same form. If no then the system could potentially take care of such visits by automatically converting them into planned visits or even easier throw warning to the user with an option of an override, in case of defects. Like above this question also should be asked. In either case, we could assume no unplanned visits as far as scheduling is concerned.

### Solution thinking

It seems that there is a real-world identity of a visit which is not mapped in OpenCHS. In the real world, one can say "August 2019's MONTHLY VISIT" but to pinpoint the same visit in the system one needs to use logic. Something that can be represented with data is better than being inferred via logic.

1. **The user performs a planned visit - leading to a visit (of type within the group) getting scheduled after it** - Schedule the next visit in the sequence based on SDBP and ADBP.
2. **User edits a planned visit** - Find visit next in sequence. If still in scheduled, change the scheduled date, if it is ADBP else do not change.
3. **The user performs a canceled visit (or cancels a scheduled visit)** - Same as 2 (using Canceled date instead of Actual date) unless the visit scheduling overrides this.
4. **User edits a canceled visit** - Same as 3.
5. **The user performs an unplanned visit before the newest scheduled visit** - Give the warning with a message - to use/cancel the already scheduled visit, or to use different visit type created for such purposes. If the user still proceeds, do not touch the visit schedules.
6. **User edits an unplanned visit before the newest scheduled visit** - same as 5.
7. **The user performs an unplanned visit after the newest scheduled visit** - same as 5.
8. **The user edits an unplanned visit that is after the newest scheduled visit** - same as 5.
9. **The user performs an unplanned visit when (because of defect) there is no scheduled visit** - Schedule the next visit using the actual date for the scheduling logic.

There are two ways to handle exit.

1. Allow the user to fill exit as a reason in the cancel visit form and then automatically exit.
2. Do not have the option for the exit in the cancel visit form. The user must exit the person from the program and as a result, the platform cancels all scheduled visits with an exit as a reason for it.

## Scenario

What should use do when retrospective data entry has to be done?\
one major scenario missing is retrospective data entry - when we enroll a person at an earlier date, it generates old visit schedules. In cases where we don't have data in the paper-based system especially for the mandatory fields, it becomes a challenge. Also, a bunch of visits has to be canceled to get it to the current state

## IGNORE

Visit types in each implementation have multiple **groups of visit types**. Each group can have one or more visit types. e.g. In Sewa Rural - Monthly, Quarterly, Half-Yearly, and Annual are part of one group. Or in maternal and child health programs - ANC, PNC, Delivery, Abortion are all part of one group. The grouping is based on fact that they schedule each other. A program may have one or groups of visit types.
