---
title: Component Architecture
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
## The Avni suite includes the following components:

* Field app (Android)
* Administration app and App Designer (Web)
* Media app (Web)
* Avni server
* Rules server
* ETL Server
* Media Server
* Integration Server
* BI Tools

## Avni components

<Image align="center" src="https://files.readme.io/601f4e4-Avni_Components.png" />

### Field app (android)

The **Field app** is the primary interface for field workers. It provides the ability to fill forms and record observations from the field. The app can work completely offline and allows for sync of data to a central server (Avni server) when the field-worker is at a place with good internet. 

The login credentials of authorised users are maintained in AWS Cognito - a third-party identity server that is integrated with Avni.

### Administration app and App Designer

The **Administration app and App Designer** is a web-based interface that is used to configure organisations in Avni. Login is allowed to all organisation administrators. In this, you can configure and setup Avni server, for example configuring master data for Locations, Hierarchies, Users, design the Program Forms, etc. It can also be used for Data Imports, Exports and Data-Entry. It runs in a web browser.

### Avni Server

The **Avni server** exists for the following reasons

* A centralized place to keep data persistent and to enable sharing of data between different devices.
* Enable creation of organisation workflows and forms centrally through metadata.
* Enable availability of aggregate data that can be accessed by the Reports server. 

The Avni server is multitenant, which means that the same server can be used for multiple organisations/locations/programs. Multitenancy is achieved through *row-level security policies* within the database. This is important for Avni to achieve operational efficiency. For further in-depth details on multi-tenancy please refer [Multitenancy](doc:multitenancy-1)

### Rules Server

Avni uses JavaScript-based rules to provide configurability for each implementation of the platform. For details please refer to [Rules concept guide](doc:rules-concept-guide). These rules are executed primarily on the android device when used in the field in offline mode. Since Avni also has data entry web application which is used in the online mode, for performance reasons these rules cannot be executed in the browser. To run these rules, closer to the database, Avni provides a node-based rule server which executes the same rules on the server-side.

**Security** is handled through JWT tokens obtained from the Identity Server. More details are available here:  [Security](doc:security).

### ETL Server

Avni ETL server is a homegrown solution that creates and updates the data in an implementation specific schema (instead of generic forms data that is same for all implementation - as maintained in the main database). ETL Server keeps the data upto date at configured frequency. It works by connecting directly to the production database and uses generated SQL to create/update implementation data at a very high speed.

### Media app (web)

Media app allows the users to view the media and other type of files like Google Photos (with previews, download). These files are saved to Avni  using the field app. It also allows filtering of media files based on Avni metadata like program, subject types, dates, form fields etc.

### Integration Server

Integration server provides a platform for developer to write Java modules to integrate Avni with any implementation specific system - moving data in both directions. The platform has modules to support integration reusable products like Bahmni, Glific, Exotel. Apart from these products Avni is also integrated with customer specific systems using this platform.

### BI Tools

Avni team uses [Jasper Reports](https://www.jaspersoft.com/), [SuperSet](https://superset.apache.org/), & [Metabase](https://www.metabase.com/) as a reporting interface running on top of the ETL schema created using ETL Server. Avni allows for navigating from BI tool to Avni web app for zooming into the beneficiary/subject specific data. Other BI tools can be also used - as Avni uses Postgres database supported by most if not all BI tools.
