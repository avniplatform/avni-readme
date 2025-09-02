---
title: Environment setup for local product development - Ubuntu and Mac
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
This page documents, how to set up your local development machine so that you can contribute to Avni products. 

This setup documentation assumes that the user knows how to install and use some of the commonly used utilities. There is enough documentation on the Internet for installing and using them (putting in your version of Ubuntu would give you better results). We may link these docs in some places but not always. The exact commands are `indicated as this always`. Please ensure you have the following software setup.

* Git
* GitHub account
* Your OS user has sudo access
* Curl
* nvm
* Flyway command line tool (optional)

### Other information

* The GitHub organization for Avni is here - [https://github.com/avniproject](https://github.com/avniproject) which has all the repositories.
* The Linux user using which installation has to be done is expected to be a sudo user

# Server-Side Components

### PostgreSQL

* Install postgresql-server (version = 16)
* Change postgres login access from *peer to trust* - Edit /etc/postgresql/PG.VER/main/pg\_hba.conf. As in line 2 and 9 of [this file](https://github.com/avniproject/avni-readme-docs/blob/master/example-code-files/pg_hba.conf).
* Restart postgres - `sudo systemctl restart postgresql.service`
* Configure your system user to have all privileges on postgres DB

CMD$>psql -d postgres --u postgres

```Text sql
CREATE ROLE <system_user>;

GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO <system_user>;

GRANT ALL PRIVILEGES ON DATABASE "postgres" to <system_user>;

ALTER USER <system_user> WITH SUPERUSER;
```

* Create OpenCHS database, user and install extension - make build\_db

### Java

* Install JDK 21
* Set JAVA\_HOME,\
  OR\
  Add installed JDK to `jenv`  using\
   `jenv add /usr/localCellar/openjdk@21/21.0.*`

### Avni Server

* Clone avni-server from Github
* Create the database - make build\_db
* Start Avni Server by running the command `make start_server` from the home directory of the avni-server project. (the first time it may take a few minutes to download Gradle, maven dependencies, and all the database migrations. wait till you see Started OpenCHS....)
* Verify Avni server is running - `curl <http://localhost:8021/concept`> - should give you a few concepts in the JSON response.

### Avni Rules Server

* Clone rules-server from Github
* Use nvm to install node version requires - `nvm install` from the rules-server directory. It would pick up the .nvmrc file to install the correct version.
* Use `make deps` to install dependencies
* Use `make start` for starting the server

### Avni ETL Server

* Clone avni-etl from Github
* Avni ETL uses the same database as avni-server for unit tests. Hence to run the test you need clear/setup test database from the avni-server project.
  * Run `make rebuild_testdb deploy_test_schema` from avni-server, you will need flyway for this. The other way is to run `make test_server` once to setup the latest test database schema.
  * Run `make test` from avni-etl
* `make start_server` from avni-etl

### Avni Media

Avni media has two components - web app and server. Avni Media client and server do not support username based authentication which is currently supported on avni-server for local development. Connecting local avni-media to server/etl on another environment like staging leads to CORS errors.

Thus, for local development on avni-media, other avni components like avni-server and avni-etl need to be running locally with an IDP enabled i.e. Cognito or Keycloak.

I. Ensure that local avni-server and avni-etl have the following environment variables set

```shell
AVNI_IDP_TYPE
OPENCHS_CLIENT_ID
OPENCHS_IAM_USER_ACCESS_KEY
OPENCHS_IAM_USER_SECRET_ACCESS_KEY
OPENCHS_BUCKET_NAME
OPENCHS_USER_POOL
```

```Text Shell
OPENCHS_KEYCLOAK_SERVER
OPENCHS_KEYCLOAK_CLIENT_SECRET
OPENCHS_KEYCLOAK_ENABLED
```

II. Alternately, to work with Staging env IDP and Storage, you may use the following Make command in both avni-server and avni-etl. If required, override the DB and other details, Ex: OPENCHS\_DATABASE\_NAME=avni\_org

````Text Shell
```
## Init below fields in env variables
OPENCHS_STAGING_APP_CLIENT_ID
OPENCHS_STAGING_USER_POOL_ID
OPENCHS_STAGING_IAM_USER
OPENCHS_STAGING_IAM_USER_ACCESS_KEY
OPENCHS_STAGING_IAM_USER_SECRET_ACCESS_KEY

make start_server_staging OPENCHS_DATABASE_NAME=<your_db_name>
```
````

There is a local.staging environment template configured for client and server that help with running avni-media locally. Look at client/middleware.js which takes care of routing the request to local avni-server and avni-etl in this mode.

**Server**

```Text Shell
cd server
nvm use
make deps
make start-local-staging
```

**Client**

```Text Shell
cd client
nvm use
make deps
make start-local-staging
```

### Conceptual note

Avni authenticates against an external system (Cognito User Pools) which is a common resource. In developer environments, we do not use authentication because that would mean creating and sharing users on the user pool. If we use common users across developer machines the token issued and user status (locked, password) become shared data - leading to one developer blocking another user. This can hamper productivity. Hence for now, instead of engineering a solution around it, in developer environments, we can bypass authentication. This is sub-optimal but useful at a small scale (number of developers).

Bypassing authentication leaves one problem behind - how does the server know which user is sending the requests. As you will see later that we tell the server about the user, hence the server assumes that when no user is supplied to it, it should use that default user.

Start the server with the default user as implementation by running it is as: `OPENCHS_USER_NAME=admin@jnpct make start_server`

# Field app components

### Android SDK

* Install Android Studio (for SDK manager and device manager).\
  `brew install android-studio`\
  Note: Genymotion is preferred usually because of a lower resource configuration, but Mac M1 does not support Genymotion.
* Install JDK 17 using brew.
* Use SDK Manager in  android-studio to install latest - *platforms;android-31* and \_build-tools
* Set `ANDROID_HOME` and `ANDROID_SDK_ROOT` environment variables\
  (usually `$HOME/Android/sdk` on linux and `$HOME/Library/Android/sdk`  on Mac)
* Modify PATH to include `$ANDROID_HOME/tools` and `$ANDROID_HOME/tools/bin`\
  OR\
  On a mac  :Install android-platform-tools `brew install android-platform-tools`.\
  This makes android tools executables available on the path

### Android Emulator

* Install Genymotion or Android studio.
* Create a virtual android device and start it\
  TIP: you can start the device without starting studio by using the following command\
  `$ANDROID_SDK_ROOT/emulator/emulator -avd <your-avd-name>`

### Avni Android Client

* Clone avni-client from GitHub
* Use nvm to install node version requires - `nvm install` from the avni-client directory. It would pick up the .nvmrc file to install the correct version. Do read up on nvm if you are not familiar.
* Install node dependencies - `make deps` (this would take some time in installing all the node dependencies)
* Verify unit tests are running fine - `make test`
* Replace "localhost" with "10.0.2.2" in androidDevice.mk and dev.json.template files for "Mac OSX m1" with non-genymotion emulator
* Launch the app on an Android device
  * `cp packages/openchs-android/config/env/dev.json.template packages/openchs-android/config/env/dev.json`.
  * From the avni-client folder, perform the `make run_packager` in one terminal and perform `make run_app` in another terminal. After the app loads up in the emulator, press the sync button in the app. This should download all the configuration and master data that has been set up for this implementation.

# Web app components

* Clone avni-web-app
* Run `nvm install` from root dir of avni-web-app
* Install Yarn. Instructions [here](https://yarnpkg.com/en/docs/install)
* `make deps start` (ensure that you have started the server by providing the user)
* If the command fails with errors about fsevents, then check the version of Python installed and used as default 
* Configure pyenv and install 3.11.3 version of python on the machine and set it as local default
  ```
  #> pyenv versions
  #> pyenv install 2.7.1
  #> pyenv install 3.11.3
  #> pyenv local 3.11.3
  #> pyenv versions
  ```
* Open the app by going to [http://localhost:6010](http://localhost:6010)
* To start the web app as a specific user like admin\@jnpct or dev\@jnpct you can run - `REACT_APP_DEV_ENV_USER=admin@jnpct make start` or `REACT_APP_DEV_ENV_USER=dev@jnpct make start`

# Keycloak

Note: This is optional and is relevant only if you are working on features related to Avni on-premise deployment. We do not require Cognito or Keycloak access for most work (see conceptual note). There is a [makefile](https://github.com/avniproject/avni-infra/blob/master/local/keycloak/Makefile) that most of the following commands as targets.

* Follow the steps given here up to *Login to admin console* - [https://www.keycloak.org/getting-started/getting-started-zip](https://www.keycloak.org/getting-started/getting-started-zip).
* Request for a sample realm file that you can import from the team, using the`bin/kc.sh import --file=basic_realm.json`. Note the server must be stopped to run this command. Post import you can start Keycloak again.
* Log in to the Keycloak console and create a super admin user. This user is set up in the Avni database to be able to access any organization. This user should have an attribute by name=custom:userUUID and the value to be the same as the UUID of this user in the Avni database.
* In the client, settings set the Web Origins to [http://localhost:6010](http://localhost:6010) (this is the origin during development for the Avni web app)

# Setting up an implementation

Avni completed the setup and will create a blank slate. In this blank slate, you can create a new organization and configure the organization by setting up the applications, locations, users, etc. But if that is too much for you to get a feel of what is possible in Avni, then you can apply an implementation after creating the organization and an organization admin user. All the steps to follow below are from avni-web-app.

* Create a new organization (note that you are a super admin when you are doing this)
* Switch to the organization by choosing the org in the dropdown near the top right
* Create an organization admin user (by filling only the mandatory fields and setting the org admin value correctly)
* Get access to bundle export of an existing implementation by downloading it from another environment
* Install the bundle by uploading it

# Common tasks

* Debugging avni-client
  * Use console.log(). You will be able to see the logs by running `make log` or in metro output.
  * Debugging or testing changes done in avni-models
    * Since avni-models is an npm dependency in avni-client, you will have to build it locally and copy over the code in node\_modules. Build it from avni-models repo by running `make deploy-to-avni-client`

* Debugging avni-webapp
  * Use chrome/firefox debugger or console.log().
  * Debugging or testing changes done in avni-models
    * Same as how it's done in avni-client (you can find the target in Makefile of avni-models)

## **SETUP COMPLETE !!!**
