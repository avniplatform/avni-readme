---
title: Integration Service Operations
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
Please refer to the [Integration design and developer guide](doc:integration-developer-guide) for - how to develop integration code. This guide describes how to operate and support the integration service

### Managing Metadata Mapping

When new fields or sometimes entity types come up in the system or incorrect mapping needs to be fixed, then the one can use the Metadata Mapping tab to do so. For all entities and fields in Avni name/title is used. For other system it could be UUID or some other identifier. When providing the field mapping one should provide the parent entity type of the field.

### Integration job monitoring

* Background jobs can be monitored via [https://healthchecks.io/](https://healthchecks.io/). The failure here indicates that the background job didn't complete in time. It could be because the job didn't run, or it hung, or it failed with error. A ticket can be raised or product team support can be taken when this happens.
  * If it failed for error then the error can be checked in Bugsnag. The stack trace and link to the error can be put in the ticket. Usually these should be urgent tickets.

### Business Error monitoring

The integration module is coded to handle business errors. These are also called classified errors. The errors can be viewed from the Error tab in the admin app. The operations team should ignore these errors when the system is handed over to the customer. The customer is responsible for looking at classified errors and fixing them by fixing the data.

The classified errors are due to data in Avni or the integrated system - never in the integration service module. If this is not the case then product team should be informed.

For each classified error the required action must be available in the document as to how to rectify them.

In most integration systems, these errors are frequent - hence no specific monitoring has been put up for this. But it can be if required.

### Frequency of scheduled job

Each module has two scheduled jobs - for regular and error processing. The regular job performs synchronising in both directions (if applicable). These jobs can be run more or less frequently via the system environment variable of the integration service.
