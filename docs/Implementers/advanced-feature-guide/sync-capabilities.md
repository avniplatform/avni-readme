---
title: Sync capabilities
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
## Offline

Avni works completely in offline mode except during login and sync. The first time sync runs just after login.

## About Sync

* Download - Get data meant for the user from the server onto the device. It is incremental after first sync after login.
* Upload - Uploads any new data created by the user.

| Sync Initiation | Function         | Frequency      |
| :-------------- | :--------------- | :------------- |
| Login           | Download, Upload | NA             |
| Manual Sync     | Download, Upload | NA             |
| Auto Sync       | Upload           | Every hour     |
| Auto Sync       | Download         | Every 12 hours |

<br>

## More about Auto Sync

Auto sync needs to run in the background when the user is not using the app for data integrity and app availability to the user.

* Battery usage - Upload sync should have minimal device resource usage as it will do anything only if the user has captured any new data. Download sync will run twice in a day and the duration for which it runs depends on Internet quality and amount of incremental data it has to get from the server. Also, if the internet quality is poor the device is mostly be CPU idle during the sync.
  * The users may report unusual battery usage using the Battery Usage in the settings for a period of time > 1 day.