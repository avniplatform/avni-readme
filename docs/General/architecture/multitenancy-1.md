---
title: Multitenancy
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
The reason why Avni is multitenant is explained in [Avni service on the cloud](doc:openchs-service-on-the-cloud)  This section explains the implementation of multitenancy in Avni. 

Multitenancy in Avni is built into the database using row-level multitenancy ([RLS](https://www.postgresql.org/docs/9.5/ddl-rowsecurity.html)). All data and metadata tables that are specific to organisations are secured using RLS policies. The Avni server and the Reporting Server are multitenant aware. 

Every new organisation that is set up in Avni gets a database role. This database role is limited to access only those rows that belong that their organisation. The definition of an organisation and the database role corresponding to the organisation is in the organisation table. 

### Avni server

When an http request reaches Avni, the user is first authenticated. Based on the authenticated user's organisation, the database role of the user querying the database is set to a role that is built purely for that organisation. This limits access of data to just the organisation that the user belongs to. Below is a sample request-response cycle. 

![](https://files.readme.io/2a03d9f-Avni_multitenancy.png "Avni multitenancy.png")



### Reporting server

\[Deprecated]In the reporting server, each organisation is defined as a new database, authenticated by their corresponding database role. This limits access to data to their organisation alone. Users are assigned to groups, one group per organisation. Each group is provided access to the database that they have access to.

### Further Reading

Read this excellent blog by Vinay, from the Avni Team, on Multi-tenancy: [Creating a cost-effective multi-tenant platform - Medium.com](https://medium.com/samanvay-on-tech/creating-a-cost-effective-multi-tenant-platform-24b2ed7d0bad)