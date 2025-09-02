---
title: Why is Avni multi-tenant?
excerpt: An explanation of why Avni is hosted on Cloud with multi-tenant setup.
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
Avni is multi-tenant and cloud-ready because it helps both the software providers and the end-users, by using a common instance of Avni Server for multiple programs / locations / organisations / etc.  

You can optionally easily deploy Avni Server on your own servers as Avni is OpenSource, but it is far economical, maintenance-free and future-proof if you choose to use Avni-on-Cloud.

##Ability to support multiple implementations
Many (not all surely) organisations that provide services on the field are often small. There are several organisations that have around 5 to 20 field workers (app users). Supporting Avni needs a sysadmin that can manage Linux, Java application, Postgres, Metabase,  networks, backups/restores, security that has significant ongoing costs associated to it. Larger organisations may already have servers and people handling them, but even for them, the cost of hosting Avni on the cloud is much lower than hosting themselves. Along with the costs, the system administrator needs to perform these activities well. Find such system administrators is not quite easy for organizations that do not have much of an IT department.
## Availability of service by health-workers
Most on-premise installations mean the server is available only in a central location over their local area network. Health workers usually come to this central location, which has internet, to sync at regular intervals. Whereas with the cloud this access is available from anywhere in the field. Of course, the in-premise deployment can also setup a VPN and acquire public IP. Both of these require maintenance and/or cost.
## Software management and Upgrades
Avni, like most products, will remain in active development for very long time. New features continuously get added, releases come out once or twice a month, patches for defects are fixed many times a month. If one runs it on-premise, the servers would need to be updated with the latest changes. As the product evolves the way it is installed also evolves. If one is not frequently updating the product, the cost of upgrade starts accumulating. On-premise deployment, also means that the app on Android PlayStore cannot be used and one needs build, host and version their own apk. 
## Economy of scale because of shared common
There are two types of economy of scale that one gets with cloud deployment.
  * Shared infrastructure - Most server infrastructures are poorly utilised, with servers running much below their capacity. With cloud infrastructure since the servers are shared.
  * Common services - All activities related to management of the infrastructure and the services, benefits everyone.
While cost component is intuitively understood. The quality component is no so much. The shared common improves in its quality because it receives inputs from all the customers and all their users.
## Benefiting from infrastructure service provides
When servers are hosted on the cloud, many of the infrastructure services like backup, restore, data encryption, server, secure access, firewalls, monitoring are implemented is a high quality fashion for you, by the provider. Replicating this at each in-premise deployment is difficult.

##Q&A
Here are some common questions regarding cloud services that organisations ask. 
- ***Who will own my data?***
A big reason for organisations to not use cloud services is the worry that they might lose ownership of data. The service automatically provides reports and a complete download of data on-demand. Ownership of data **always belongs to the subscribing organization (you!)**. The hosting organisation does not have privileges to use the actual data for their own purposes, enforceable legally and also technically. 

- ***How safe is data in the cloud?***
Most organisations that run servers on their own are also aware of how their data is being backed up. Not knowing the mechanisms of backup causes that feeling of loss of control. 
  * The cloud provider has redundancies to ensure disk failures do not cause loss of data or downtime. 
  * Prior to software upgrade full database backup can be taken (or snapshotted) by the service provider.
  * Additionally when the database is encrypted, the cloud provider cannot read data stored in their infrastructure. All transport of data is also encrypted uses the https protocol. So, nobody can read the data when it travels between the server and the client.

- ***Who else can see my organization's data?*** 
The multi-tenant design of Avni **disallows** organizations from viewing data of other organizations. The separation of data is built into the software and the database access. Additionally when the database is encrypted, the cloud provider cannot read data stored in their infrastructure. All transport of data is encrypted uses the https protocol. So, nobody can read the data when it travels between the server and the client.

**Technical support staff** - For resolving bugs or issues during production, a limited set of Technical support staff do require access to the data, with appropriate checks and audit control in place. The best (and probably only) way to handle this problem would be to ask the support staff to sign a non-disclosure agreement - and ensure access to systems is logged and auditable.