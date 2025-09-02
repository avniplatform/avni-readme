---
title: Rule failure telemetry
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
## About rule failure

Avni doesn't stop the users from using the app and filling the forms when the rules triggered fail. These errors get stored on the mobile device's database and then posted to the server as part of the sync process. Only if the Sync completes successfully, do the RuleFailureTelemetry entries on the mobile device get cleaned-up, otherwise, the keep getting created as duplicates as part of every sync there onwards. This behaviour is to ensure, we do not loose RuleFailures before they are successfully synced to the backend server and stored in primary Database.

The app designer displays these errors from the Rule Failures tab. On clicking a RuleFailure entry, the errorMessage details are visible. These can be also closed from there, by selecting corresponding CheckBoxes and clicking on "CLOSE ERRORS" button.

<Image align="center" src="https://files.readme.io/755b159-Screenshot_2024-05-06_at_4.22.58_PM.png" />

## App designer fields of rule failure entry

### Which Rule

* Rule UUID - UUID of the rule (other details can be picked from the database)
* Source - Possible values are present [here](https://github.com/avniproject/avni-server/blob/master/avni-server-api/src/main/java/org/avni/server/domain/enums/ruleFailure/SourceType.java). This should not be null (currently there are some old entries for which it is null). This indicates what is the type of rule for which this error happened.

### On which entity

* Individual UUID - The UUID of the subject on whose data edit/create this error happened. This will be not be present for EntityType=ReportCard - as ReportCard rules span across subjects.
* Entity - `UUID` + `Type of the Entity`. For report card rule errors this field will be null
  * Type of the entity - Possible values are named [here](https://github.com/avniproject/avni-server/blob/master/avni-server-api/src/main/java/org/avni/server/domain/enums/ruleFailure/EntityType.java).
  * UUID - When the type of entity = `Individual` then the UUID and `Individual UUID` field will be same.

### Other details

* Message (database field error\_message + stack trace).
* Error date - The date on which error happened on the user's device. The clock used is that of the device.
* App - Android or Web. null value = Web. This indicates which type of channel on which this error happened.

### Error management

* Status (Open, Closed)
* Closed date - Date on which on the error was closed.
