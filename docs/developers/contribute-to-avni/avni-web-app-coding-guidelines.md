---
title: Avni Web App Coding Guidelines
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
(not a complete doc yet)

In functional components try to avoid (**this is not a strict requirement**) creating functions in the body, as these functions get redefined with every state change. There are the following options:

* Use *useCallback* to define memoized functions (note that even these get redefined based on the state change)
* define the function outside the functional component in the same file and pass all the required parameters which may be too many
* create class type component
