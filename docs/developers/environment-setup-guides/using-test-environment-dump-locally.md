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

### From IntelliJ choose the database and from pop-up menu choose "Export pg_dump".

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/42ab63c-Screenshot_2023-07-19_at_2.30.41_PM.png",
        null,
        ""
      ],
      "align": "center",
      "border": true
    }
  ]
}
[/block]

### In Statements choose Copy instead of insert (applying the dump later is faster). Note down the location of the dump.

![](https://files.readme.io/ccb692c-image.png)

## Apply dump.

In avni-server and integration-service there are makefile commands in the file externalDB.mk to restore the dump. In avni-server there is also a command to enable a db user for an implementation organisation.