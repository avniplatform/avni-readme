---
title: Release Naming and Branching
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
Starting June 2023, Avni is going to follow the following release naming strategy. The release has three parts - the major version, minor version and the patch fix version. Note that this is not semantic versioning. 

Release Versioning Format: MajorVersionNumber.MinorVersionNumber.PatchVersionNumber.

1. A major release happens every 4-6 weeks. The major version is bumped up during this time. eg: 4.0.0
2. There might be a minor release between two major releases that consists of planned work that might need to be released sooner. This goes in a minor release. eg: 4.1.0
3. Urgent patches go in a patch fix release. eg: 4.0.1

### Branches

<Image align="center" src="https://files.readme.io/701267ec2f229cac03b6f1996472488fb8e37ea9bba2e4ff669aca89d50a4ed2-7d3d36e-branching.png" />

Every branch comes out of the previous parent (4.1.0 comes out of 4.0.0, 5.0.0 comes out of main / master). Branches are merged to their parents, which eventually go up till the mainline (master/main).

### Release branching guidelines

1. Patch version use the corresponding major / minor version branch itself and **not** a separate branch\
   Ex: For 4.0.1, we'll use 4.0 release branch itself
2. Minor version has its own branch, created from previous major/ minor release branch\
   Ex: for 4.1.0, we'll create a new branch 4.1 from 4.0
3. Merge happens from release major / minor branch to all pending Major / minor release branches, as well as to Master/Main branch\
   Ex: During 4.1 release, We'll merge 4.1 to 4.2, 5.0 and Master/Main branches
4. During release create tag with format vMajor.Minor.Patch version in release branch across all repos\
   Ex: For 4.1.0 release, create tag v4.1.0 on all repos, 4.1 branch **head** commit
5. To trigger different Flavor(Gramin) APK generation for an old Patch version in avni-client which already has additional patch versions, you should make use of a separate branch that would only used for APK generation purpose.\
   Ex: For 4.0.1 Patch release, if there is already 4.0.2 release completed, since all changes are on 4.0 branch, create a new branch "apk\_401" from tag v4.0.1 and use "apk\_401" branch for Gramin APK generation.
