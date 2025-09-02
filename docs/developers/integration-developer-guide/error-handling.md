---
title: Error handling
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
During the integration process, the following types of errors can happen.

1. Business error
2. Availability or defects in the integrated systems
3. Error due to defect in the integration code

When errors happen the integration code there are two choices, whether to move on and process the next record set of records or to stop the integration process completely.

### Business error

These are error scenarios that are known at the time of writing integration code. These errors happen when the two integrated systems and the integrator are working as expected but the error can still happen due to users or known business scenarios. e.g. when a patient is being synchronised from Bahmni to Avni there are two subjects in Avni with the same Sangam number (unique identifier).

Important rule to identify such errors is that - these type of rules can be be fixed by the users of the Avni, integrated systems, or the system admin of the integration service.

### Availability or defects in the integrated systems causing errors during integration

These are errors that the integrator doesn't have any way to recover from, as it is outside the scope of the integrator if even it itself is working as expected.

- one or both of the systems being integrated are not available due to network or other issues
- one or both of the integrated systems have software defects that cause the integration process to work not as expected

### Error due to defect in the integration code or data setup of the integrator

self-evident

## Types of error

### Business error

Integration code should be coded to capture all business errors and log them into the Error Records in the integrator database. The integrator should move to the next record and process it. Since the business error has happened specifically to one record (e.g. patient) the next record can be successfully processed.

### For other types of error

it is difficult to differentiate and tell whether the integrator can process the rest of the records successfully or not. These errors are where the system throws an exception (NPE, IO exception, SQL exception, etc). Just based on exception type usually it is very difficult to tell exactly what is the cause and take corrective action in the code. Hence for such exceptions, the integration module can decide whether it should keep processing or abort. The trade-off is as follows.

#### Continue processing on error

If the error is due to such record-unspecific reason then all the subsequent errors will go and sit in the error records as well. It is also possible that the underlying reason may get rectified while processing of subsequent records. There are issues that one needs to be aware of when taking this approach.

1. When looking at the error records it will become difficult to tell whether which issues one much tackle and which ones one should leave alone (as they would get fixed when the underlying issues get resolved). Hence this approach can have negative consequence on supportability of the system.
2. When taking this approach the integration module **must** ensure that out of order execution of records doesn't create incorrect data and semantically out of order execution **must** always fail - otherwise the records may go in the error log in the reverse order and may never process successfully.
3. If this approach is taken the entire error records must be processed more frequently (almost like the main processing frequency). This may create unpredictable and much higher load for the Avni and the other system. For example if the source system is up and destination system is unavailable then source system will be keep getting requests for all records in each run - while not being able to process them.

(so far Avni integration framework has only been used for the subsequent error processing approach - some of the features like water marking after each record processing is not present)

#### End processing on error

In this case the next run of the process will pick up from the same record and try to process again. The error is notified to Bugsnag. If the error is due to

1. availability then uptime monitoring for these services will also catch them. Hence in bugsnag, such errors should be marked as ignored so that next time they do not raise a trigger.
2. some defect in any of the three systems. The error can be rectified and the bug can be marked as fixed in Bugsnag.

The limitation of this approach is that:

- In case it is a bug in one of the three systems that impact only certain records then other records are also not processed till this issue is resolved by someone manually by changing the code.

## Business error classification

Since the business error needs to be resolved by fixing the data. These errors can be browsed through the Avni integration web application. The integration framework allows the developer to define their own error types and then log the errors against that - to assist in browsing the errors.

## Error records scheduled job

A schedule or task in within the existing schedule can be defined to reprocess these errors. In the data has been corrected then the error corresponding to them will resolve itself.