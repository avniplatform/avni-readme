---
title: Release process for Security Testing
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
In-order to generate a secure apk used for security testing purposes, we currently have only 2 make commands that generate security compliant AAB or APKs. Before running below build commands, ensure the flavor is set correctly, or include flavor info while invoking them.  
Ex:- export flavor=lfeTeachNagalandSecurity

The 2 commands are:

- Universal APK
  - versionName=1.0.0 versionCode=1 make release_prod_universal
  - versionName=1.0.0 versionCode=1 make release_prod_universal_without_clean
- AAB
  - versionName=1.0.0 versionCode=1 make bundle_release_prod
  - versionName=1.0.0 versionCode=1 make bundle_release_prod_without_clean