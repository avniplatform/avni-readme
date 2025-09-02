---
title: Review Implementation Bundle
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
Avni offers the ability to export the configuration and metadata from an implementation into a bundle ([App Designer -> Bundle](https://app.avniproject.org/#/appdesigner/bundle)). This bundle can then be uploaded into another implementation if it is required to have the same metadata and configuration setup ([Admin -> Upload](https://app.avniproject.org/#/admin/upload) ).

Since this a feature with widespread consequences if the wrong bundle is used on the wrong implementation, the implementer can review the changes that will be affected as a result of uploading a bundle before applying it. The option to review the changes is displayed after selecting the upload type as 'Metadata Zip' on the upload screen and uploading the bundle.

<br />

On clicking 'Review', the uploaded bundle is compared against the implementation that the user is currently logged into and a file by file list of differences is displayed on screen. The file listing categorises the changes as additions (green), modifications (orange), removals (red) or if completely new items will be created if the bundle is applied.

![](https://files.readme.io/2582a6ae1664ad643eb9421b4cd7484d0d8baa00c480a9d13c36b60e6e8d8dbb-image.png)

<br />

On selecting a file, the details of the changes in that file are shown. The implementer can use the 'Back to Upload' option to return to the upload screen after reviewing the changes to change the bundle file used or apply the bundle.

![](https://files.readme.io/312f6da0ac043d3048fc4837f167416132de631a8d193084d809e81a63988fc8-metadata-diff.webp)
