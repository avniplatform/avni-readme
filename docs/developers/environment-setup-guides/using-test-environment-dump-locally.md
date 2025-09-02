---
title: Using shared test environment dump locally
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
**This facility is available only for the core team members.**

## Take dump

### From IntelliJ choose the database and from pop-up menu choose "Export pg\_dump".

<Image align="center" className="border" border={true} src="https://files.readme.io/42ab63c-Screenshot_2023-07-19_at_2.30.41_PM.png" />

### In Statements choose Copy instead of insert (applying the dump later is faster). Note down the location of the dump.

![](https://files.readme.io/ccb692c-image.png)

## Apply dump.

In avni-server and integration-service there are makefile commands in the file externalDB.mk to restore the dump. In avni-server there is also a command to enable a db user for an implementation organisation.
