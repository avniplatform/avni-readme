---
title: Access media in reports
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
Data in Avni is stored in two different data sources. The first is the postgres database, which are easily connected to the reporting servers that are being used by hosting. The second is an S3 database where media is stored. 


In reporting tools, there is a mechanism to show data by connecting to a data source. However, S3 access is usually not provided. In case you need to expose media through reports, here is what you need to do. 

1. Provide users access to Avni. 
2. In reports, observations are usually of the form "https://prod-user-media.s3.ap-south-1.amazonaws.com/org_name/file_name.png". This will be stored in observations of the form. To provide a link that shows this, change it to the form " https://app.avniproject.org/web/media?url=https://prod-user-media.s3.ap-south-1.amazonaws.com/org_name/file_name.png". 

Doing this will send the user to app.avniproject.org, which will redirect the user to the corresponding media once they have authenticated themselves on avniproject.