---
title: Avni Server - Contract, Request, Response
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
Avni server internally uses entities or domain model based on hibernate. These domain entities are not directly serialised into or deserialised from external formats, except in some cases. The external formats Avni Server currently has are as follows.

1. Web app request
2. Web app response
3. Bundle line item (both export and import are same)
4. Mobile app request
5. Mobile app response
6. External API request
7. External API response
8. Request made to rule server
9. Response received from rule server
10. Custom formats for integration with external systems like Glific.

<br />

In Avni code while we would like to reuse data transfer object classes as much as possible but currently:

- In order to maintain stability of mobile app, we tend to keep their formats separate.
- Rule server formats are not very well thought out yet, as it may be refactored in future, hence they are also separate. It may become internal to Avni server in future, so separate formats may not be required.
- External API request/responses are designed for external consumption. Their formats look like what one implementation may see their data as and not how Avni represents data.

<br />

Web app request, response, and bundle formats are considered `online`, meaning these can be changed and fixed in more agile fashion. Their are no backward compatibility requirements for these formats.

<br />

### Internal design of web app and bundle formats.

1. Avni Contract (id and uuid). Abstract class. The class is called CHSRequest, but can be renamed.
2. Functionality specific contract. Abstract class. `abstract class ReportCardContract extends AvniContract`. These can contain all the primitive values as their representation doesn't change across bundle, request and response.
3. Concrete classes for request, response, and bundle - each extending from functionality specific contract. e.g. `class ReportCardResponse extends ReportCardContract`. Here we can add all related entity/entities.
   1. Web request should use id or uuid for related entities. In case of collection - list of ids or uuids.
   2. Bundle must use uuid or list of uuids for related entities.
   3. Web response can use full response object for related object(s).

<br />

### Related entity

Related entity is the entity which is required only for reference and are not modified by the client. For example - when editing `ReportCard` in the web app, `StandardReportCardType` is not changed.