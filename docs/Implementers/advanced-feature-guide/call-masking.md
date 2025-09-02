---
title: Masked Calls
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
When a contact number is configured in an implementation (through concept attributes), then the user gets a "Call" button on the subject's dashboard. It is used to open the dialler and make a call to the beneficiary.

With the call masking feature, an implementation can choose to convert this call button into a masked call. There is a user settings toggle to turn this on or off. Under the wraps, Avni can use the Exotel Masked Call feature to make this happen. 

To use this feature, a user needs to purchase an Exophone and configure this in Avni. Configuration is currently done by adding a row to the external\_api table. 

### User flow

* User goes to a subject's dashboard.
* User clicks on the call button
* If call masking is enabled for this user, then the call button makes a call to the server to connect their phone to the beneficiary's number. The user and the beneficiary will get a call from Exotel through which they can talk.
* The user gets a message that the call request was made successfully, and to wait for a call back.
* If call masking is not enabled for this user, then the call button makes a direct call through the dialler.
