---
title: Observation data model
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
Avni is a platform that allows its user to define their own data model via forms. But Avni doesn't define table at runtime every time you define a new form. When the field user or data entry user create actual data using these forms - Avni stores data for entire in [JSONB](https://www.postgresql.org/docs/9.5/functions-json.html) column. The data is stored as **key values**.

## Key

Each form element is linked to a concept. The key is the UUID of the concept.

## Value

### Primitive value

Primitive values as stored as is. e.g. \{"09c852a1-56ae-431a-b0b8-3207cea15dfb": 10, "a9cb0768-1fd7-4f69-ad9b-53a0517c3ae9": "This is a string value"} where 10 and "This is a string value" are values. The keys are the UUID of corresponding concepts.

### Coded value

Since Avni supports multiple select for coded answers. It stores the answers in an array. e.g. \{"4aacd6f8-7ee2-4df5-ad77-137b941c128b": \["e3b05c49-c0fa-4d8f-bc42-f2c291c59f4d", "baa65cfe-38fe-4116-96e7-8902ed6e9aef"]}. The UUIDs inside the array those of concepts which have been chosen as answers.
