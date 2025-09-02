---
title: Fast sync
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
When setting up Avni freshly on an android device the first-time sync can take a lot of time, especially if you have a lot of transactional data for the catchment. To make this process faster Avni provides an option to set up fast sync for a catchment.

The performance of syncing data from the server to the local mobile database is dependent on the volume of data - hence cannot be improved significantly. Hence fast sync depends on using the already synced mobile database file as the starting database for other users. This significantly improves the sync duration to less than 5 minutes in most cases.

There are few things to note before we start setting up fast sync.

* Fast sync is set up for a catchment, so if there is a new user in a catchment called "a" then any existing user of the catchment "a" should set up the fast sync from their device.
* Fast sync does not update automatically, which means if the user has set up fast sync one month earlier, then all the data filled after that will get downloaded by the regular sync. So it is recommended to update the fast sync whenever any user is freshly setting up the Avni on their device.
* Fast sync is triggered only when the user is syncing for the first time. So if the user has not logged in for a long time, then it is recommended to delete all the app data and log in again to use the fast sync.

## Setting up fast sync

.Setting up fast sync is very easy and it requires an active internet connection. Existing users can go to "More -> Setup fast sync" and then click "Yes". This will take a while depending on the data in the device. This uploads the database file from the user's device to Avni storage as fast sync file for this catchment.

<Image title="fast sync.png" alt={568} align="center" src="https://files.readme.io/125e0b2-fast_sync.png">
  Fast sync setup option.
</Image>

Once it is done, the new user from the same catchment will get an option to use the fast sync when he logs in for the first time.

<br />

## Verification of Existence of Fast-Sync file

Steps to follow:

1. Figure out Catchment UUID corresponding to the User facing the issue
2. Login into AWS and open up the S3 Console 
3. Navigate to "s3/buckets/\<env\>-user-media/\<org\_media\_bucket\_name\>"
4. There the FastSync files will have prefix of "MobileDbBackup-" followed by Catchment UUID Ex: "MobileDbBackup-b9103c96-7ed7-4798-a866-89419103d361"
5. Download the file and unzip if needed to check size / content

<br />

<Image align="center" src="https://files.readme.io/f6786a7c51e3bdc43bdb24a7960bc74cd7b38af163ec88dc8685f7fe46c395f2-Screenshot_2025-04-03_at_1.14.45_PM.png" />

<br />

## Perils of Fast Sync

"Fast sync" speed up the initial sync and improves new user onboarding experience. But the flip-side of setting up "Fast Sync" are as follows:

* "Fast Sync", if setup using a newer version of app, prevent fresh logins from older version of the app
* "Fast Sync", is setup per Catchment basis, so, if there are restrictions due to Sync-Concept-Values or UserGroup Privileges for one set of users and they have the FastSync Setup for them, then the other set of Users with conflicting Sync-Concept-Values or UserGroup Privileges might end up receiving invalid data / missing data during sync
* "Fast Sync", when setup, assumes that the Catchment constituent Locations are fixed, any change to the catchment results in a "Reset Sync" being created for all users which are associated with that catchment. But new users who get assigned to that Catchment, will not have the "Reset Sync" configured appropriately in all cases, this could result in missed / extraneous data sync happening to the new users.
* "Fast Sync" data cannot be modularly distributed to users of different catchment with overlapping location boundaries. You would have to spearately setup Fast sync for each catchment.
* There is no easy way for Organisation users to remove a "Fast Sync" setup for a Catchment, he should either over-write it with a new "Fast Sync" file, or contact Support team for deletion of old one. To be able to overcome the Sync failure error, you would need to do:
  * Either do "Fresh login" after that(Deletion / overwrite of FastSync file)
  * Or continue and "Perform Slow Sync"

<Image alt="Fast Sync Failure due to Version mismatch" align="center" width="450px" src="https://files.readme.io/a2bce3187fea665854c9d179dc43c9597a0dfff81f3eec23b77626bb5af2aacd-Screenshot_20250410_184143.jpg">
  Fast Sync Failure due to Version mismatch
</Image>
