---
title: Integration architecture
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
Avni was first integrated with Bahmni and is now being integrated with multiple other systems. Since there is a lot of common work and code involved in integrating Avni with other systems, the Avni integration service, and its management application are also designed to be reusable, open-source, and multi-tenant. This means that this can be deployed in the cloud to service multiple implementations of Avni or it can be deployed in customer-specific infrastructure. In this document, some of the core/common concepts of avni integration service have been described.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/91732cc-Avni_Integration.jpg",
        "Avni Integration.jpg",
        960,
        720,
        "#e1e5e6"
      ],
      "sizing": "full",
      "caption": "Avni Integration Architecture Diagram"
    }
  ]
}
[/block]
### Multi-tenant
Avni integration framework is multi-tenant. All the concepts described below are sandboxed for each tenant. A tenant can be considered on an instance of Avni's integration of implementation with another project system. The framework has been made multi-tenant so that they can reuse the same framework and can be hosted as a single service like Avni's main service can be hosted.

### Metadata Data Mapping
In Avni, the administrators (users) can define their own data model (via form element, coded answers) and master data (via location). There are scenarios in which Avni has to integrate with another system where the users may do the same. For example: in Bahmni there is a similar concept of forms and coded answers where the data model is managed by. Similarly, other EMR or data collection systems will have the same. Hence, for example, if an Avni Subject (or any other Avni entity) has to be saved as a Patient or a similar entity in another system (and vice-versa) then such data needs to be mapped.

Due to this hardcoding of every field in the integration code will require manual mapping to be done and on changes, this mapping has to be maintained. Such maintenance will require code deployment every time such fields are introduced.

Avni integration solution allows administrators (users) to manage this mapping themselves via a user interface instead of depending on a development cycle. A mapping has two main fields - avni_id and integrating_system_id. Avni id is the UUID of concept, location, etc and intergrating_system_id is either a string or number corresponding to another system's id.

Avni integration framework allows one to organize this mapping using mapping group and mapping type. This data can be set up per integration and it is left to the developer of a specific integration to use it in the way it is useful.

### Guaranteed to process
Most Avni implementations require guaranteed processing of all records. Any failure in processing due to software availability issues needs to be retried to ensure that they are processed (the failures could be due to defects in source, destination, or integrator which needs to be rectified and the failures need to be reprocessed). Note that: approaches like webhooks but without the ability to retry do not work.

Avni integrator uses [sync status records](https://github.com/avniproject/integration-service/blob/main/integration-data/src/main/java/org/avni_integration_service/integration_data/domain/IntegratingEntityStatus.java) for each process. In case of any unexpected failures, the integration process can use the readUpto information to proceed next time.

Avni integration framework allows the developer to define their entity types and can maintain their sync information (watermark, or readUpto information) as they see fit.

### Application error handling
The integration processes can encounter errors that can be fixed only by the intervention of the end users/administrators. For some of these errors, you may not want to stop the processing of other records. Along with this, you may want to reprocess these records later in case the users have fixed the issue. In order to handle both of these, you can use [ErrorRecords](https://github.com/avniproject/integration-service/blob/bdbadf96c79096f746398b69d1faddadc50820e6/integration-data/src/main/java/org/avni_integration_service/integration_data/domain/error/ErrorRecord.java). Along with logging the record you will also have to define error processes which can reprocess them at regular intervals.

For more detail on error handling please see [Error handling](doc:error-handling)]

### Integration Schedule
Since the time taken by different implementation integration processes can be variable, it is expected that each implementation can independently choose its schedule of running integration processes. Avni integration service allows for the running of multiple integrations in parallel at the same time.