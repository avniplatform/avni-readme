---
title: Build, Deployment, and Configuration
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
# Integration Server

### Build

Code is here: <https://github.com/avniproject/integration-service>

Currently integration service is configured in CircleCI, so that build has to be done on the local machine. Run `make build-server` and use the file `integrator/build/libs/integrator-0.0.2-SNAPSHOT.jar` file.

### Configuration

There are two applications that needs to be configured. The integration service application and the integration(s). Avni integration service integrates multiple systems with Avni. For each integration there is configuration of the integrated system and Avni. All the configuration is done via the system environment variables.

#### Integration Service Application

Integration service has its own database and it has an admin web application.

```Text Integration Database
AVNI_INT_DATABASE, AVNI_INT_DATABASE_PORT or AVNI_INT_DATASOURCE
AVNI_INT_DB_USER
AVNI_INT_DB_PASSWORD

for default values please check - https://github.com/avniproject/integration-service/blob/main/integration-data/src/main/resources/int-data.application.properties
```

```Text Integration Server
AVNI_INT_SERVER_PORT (port number on which the integration server will run)
BAHMNI_AVNI_IDP_TYPE [Cognito(default) or Keycloak]
```

```Text Integration Application
AVNI_INT_STATIC_PATH (default = /var/www/avni-int-service/), 
  the path from where the front-end admin application is served.
```

#### Integration between Avni and Another System

```Text Avni Configuration
BAHMNI_AVNI_API_URL (base url like https://staging.avniproject.org)
BAHMNI_AVNI_API_USER
BAHMNI_AVNI_API_PASSWORD

NOTE: The Avni configuration will be required per system. Here Bahmni has been used as an example.
```

```Text Bahmni
OPENMRS_BASE_URL
OPENMRS_USER
OPENMRS_PASSWORD
```

There are two background jobs for each integration. One that processes the integration and error record and another than processes only the errors. Usually you can keep the later disabled and run them if you want to process the errors only. By default all jobs are disabled.

```Text Background Job
# You can provide Spring Scheduler's Cron Expression to the following properties
BAHMNI_SCHEDULE_CRON
BAHMNI_SCHEDULE_CRON_FULL_ERROR
```

### Integration DB Migration script sequencing to be followed for different systems

Each system has their own migration series:

| SystemName | Migration Series                 |
| ---------- | -------------------------------- |
| Generic    | V2\_1\_\_XXX\_\<some_string>.sql |
| Bahmni     | V2\_2\_\_XXX\_\<some_string>.sql |
| Goonj      | V2\_3\_\_XXX\_\<some_string>.sql |
| Amrit      | V2\_4\_\_XXX\_\<some_string>.sql |
| Power      | V2\_5\_\_XXX\_\<some_string>.sql |

And most importantly, all the generic db schema changes, have to be done in the series  
**V2\_1\_\_XXX\_\<some_string>.sql**

If we break this behaviour, we'll mess up the Integrations update going forward

### Users, Tenant

Currently these need to be done from the database and using http requests. Hence start the server using `java -jar integrator-0.0.2-SNAPSHOT.jar`. Also, the integration service users have not been integrated with Cognito or Keycloak yet.

```curl User's Password
// Run the following from the same server or over https, so that the request doesn't go out of the network

curl --location 'http://localhost:6013/int/test/passwordHash' \
--header 'Content-Type: application/json' \
--data '{"password": "jdsai2isjd"}'

-- Take the output
```

```sql Create User
select * from integration_system; -- to get all the tenant ids

insert into users (email, password, working_integration_system_id) 
	values ('foo@example.com', 'password has from above', tenant_id);
```

# Integration Admin App

### Build

Code is here - <https://github.com/avniproject/integration-admin-app>

`make deps build-app` (make sure you have the node version mentioned in the .nvmrc)

### Deploy

You can copy the contents of build folder to your reverse proxy. The homepage name is avni-int-admin-app. You can also copy these files to avni-int-admin-app under the avni-integration-server working directory - if you want the integration admin app to be served by the avni-integration-server.