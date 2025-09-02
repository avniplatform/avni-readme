---
title: Cross system field mapping
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
The core objective of integration service is to move data from Avni and make it available in another customer system and vice-versa. This movement of data internally heavily involves mapping of fields from one system to another. The straightforward way ways one can imagine mapping of fields (and their coded answers) it to do it in the Java code. This work fine - except for the type of data where new fields can come up frequently after the system is deployed to production. If this is too frequent then it would require code change and deployment.

For such type of data fields integration service provides Mapping Metadata entity. It can be used to keep such mapping in the database so that the development & deployment can be avoided. Important to understand that if this is used incorrectly then it can create unnecessary complexity in the integration module by delegating mapping to database instead of doing it in the code.