---
title: Encounter types
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
      slug: concepts
      title: Concepts
---
Encounter Types (also called Visit Types) are used to determine the kinds of encounters/visits that can be performed. An encounter can be scheduled for a specific encounter type using rules. Here, we define that encounter type and the forms associated with the encounter type.

An encounter type is either associated directly with a Subject type or is associated with a Program type, which in-turn would be associated with a subject type. It need not always be associated with programs (you can perform an annual survey of a population using encounter types not associated with programs, and use this information to enrol subjects into a program).

## Immutable encounter type

The encounter type can be made immutable by switching on the immutable flag on the encounter type create/edit screen. If the encounter type is marked as immutable then data from the last encounter is copied to the next encounter. Since the encounter is immutable, the edit is not allowed on these encounters.