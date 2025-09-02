---
title: Use of media in forms
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
Avni allows for adding media like data (image and video) in the forms. It can be in form of single or multiple media files in the same question. These can be added by the user using the camera and the file system. Multiple files can be added too in one go. Please see the following table for the capabilities.

| Media Type  | Selection type | Android Version | Supported?    |
| :---------- | :------------- | :-------------- | :------------ |
| Image/Video | Single         | &lt; 13.0       | Supported     |
| Image/Video | Multiple       | &lt; 13.0       | Not Supported |
| Image/Video | Single         | &gt;= 13.0      | Supported     |
| Image/Video | Multiple       | &gt;= 13.0      | Supported     |

<br />

## Why multi-select is not supported in older versions of android

This capability has been restricted by the react native (framework) library used by us. [https://www.npmjs.com/package/react-native-image-picker](https://www.npmjs.com/package/react-native-image-picker)

## My media is in a folder that is not showing in the albums when I am using Avni

If you have images in android folders (in storage) as archive then it is possible that they are shown when you want to upload images in Avni forms. Please see the following as a way to solve this issue.

Android displays only folders which are **considered** albums. A plain folder with images may not be shown here for this reason.

### Option 1

You can setup a folder you want to upload media in Avni by making it show up as albums. You can do that by setting it as Google Photos backup folder. You can do that by:

* going to `Google Photos` app, then `Settings`, then `Choose backup device` folders option, then choose your folder.
* going to `Google Photos` app, then `Library`, then `Utilities`, then choose `Backup Device Folders`, then choose your folder.

### Option 2

Copy/move the folder which has the media to one of the folders which are picked by the Avni form.

*Please note that Google Photos have storage limits.*

We cannot find any means by which an album be added only locally, without it being backed up on Google Photos. [https://www.reddit.com/r/googlephotos/comments/x331q9/create_albums_that_dont_sync_with_the_cloud/](https://www.reddit.com/r/googlephotos/comments/x331q9/create_albums_that_dont_sync_with_the_cloud/)

## How do apps like Dropbox, Facebook, etc support multi-select and have better album support

Since these are not open source projects we can only guess. But it is likely that they have developed their own screens that uses the android file system API.

## Why does Avni not do the same as other apps

1. It is significant amount of work to develop this from scratch compared to use the android's media picker.
2. About 50% and rapidly growing number of Avni users are already on android 13 or later.

<br />

# Also see

* [Media Viewer](doc:media-viewer)