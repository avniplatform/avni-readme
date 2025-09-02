---
title: Sync Scheduling
excerpt: >-
  Sync can be automated or manual on the Android app. This article talks about
  the different triggers of sync
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Sync data between Avni Client and Server

Sync between Avni Client and Server is initiated by the Client and could be of following types:

### Manual Sync(User triggered, upload and fetch data)

> 📘 
> 
> As part of manual sync, we'll first replace the "background-sync" job with a "dummy sync" job, perform manual-sync and then, replace the "dummy sync" job again with "background-sync" job.  
> In react-native-background-worker, when we schedule a job with same jobKey(Name) as an existing job, it replaces the old one with new one. Therefore, above specified steps are supposed to fulfill our need to NOT run background-sync in parallel with manual-sync.  
> This is done, as we do not have a way to cancel jobs by name directly in react-native-background-worker. We could only cancel by id, but we do not want to store job id in db.  
> ![](https://files.readme.io/2dcc00c-ManualAndDummySync.png)

### Automatic Sync

1. Complete Sync (Both upload and fetch data)
2. Partial Sync (Only upload of data)
   ![](https://files.readme.io/d567681-Screenshot_2023-10-30_at_12.08.26_PM.png)