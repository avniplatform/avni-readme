---
title: Phone number formats
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
### Formats and Usage

1. E164 format (+919455509147)
   1. User phone number in Avni database
   2. Valid user phone number as sent to Cognito
   3. Avni web app display for User
2. National Format (9455509147)
   1. Observation value for phone number data type (used in web app, mobile app, and external API)
3. Glific format (919455509147)
   1. Format used in Glific requests
4. Human format - [examples](https://github.com/avniproject/avni-server/blob/b846c627f7306514f284c6511d7ac8c149f82596/avni-server-api/src/test/java/org/avni/server/domain/framework/PhoneNumberTest.java#L12)
   1. User phone number input on web app and user CSV upload
   2. Phone number observation value in CSV upload (not in mobile app)