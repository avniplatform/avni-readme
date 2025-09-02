---
title: ValueJSON structure
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
On the front-end the Observation has valueJSON field that stores observation values. The structure of that can be understood from the examples below:

Multiselect (conceptUUID is the uuid of the answer which is a concept)
{ answer: [ {conceptUUID: '00828291-c2fe-415f-a51e-ba8a02607da0’}, {conceptUUID: '00828291-c2fe-415f-a51e-ba8a02607da0’} ] }

SingleSelect 
{ answer: { conceptUUID: '00828291-c2fe-415f-a51e-ba8a02607da0' }  }

Numeric
{ answer: 1}
{ answer : null}

Boolean Dated
{ answer: {value: true, date: DATE}}