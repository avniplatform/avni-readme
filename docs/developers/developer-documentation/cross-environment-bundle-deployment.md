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
* Ensure that both the environments have the same build number deployed.
* In the bundle there may be use of S3 files like icons in Subject Type
  * Do find in files for such data and remove it.
  * Manually upload the assets from the application screen (app designer, edit subject type in this case)
* Concept media (images attached to concepts) is carried in the bundle's `conceptMedia/` folder, never inside `concepts.json`.
  * If the bundle contains `concepts.json`, it must also contain the `conceptMedia/` entries from a current export of the target org.
  * Uploading `concepts.json` without them clears the media on those concepts. There is no error, and the bundle review diff does not show it, since `concepts.json` is unchanged. See [avni-server#1043](https://github.com/avniproject/avni-server/issues/1043) — this is intended behaviour, not a bug to wait on.
* There is bug in platform related to bundle upload/download for GroupPrivilege.
