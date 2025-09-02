---
title: Cross Environment Bundle Deployment
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
- Ensure that both the environments have the same build number deployed.
- In the bundle there may be use of S3 files like icons in Subject Type
  - Do find in files for such data and remove it.
  - Manually upload the assets from the application screen (app designer, edit subject type in this case)
- There is bug in platform related to bundle upload/download for GroupPrivilege.