---
title: Access the servers
excerpt: ''
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## About various shared environments

*Logically* there are following shared environments running OpenCHS backend services.

* **Demo** - The demo environment hosts all the health modules. Certain configurations that are specific to an organization is made up for demonstration purpose. The configuration can be understood by looking at the code [here](https://github.com/OpenCHS/openchs-client/tree/master/packages/demo-organisation).
* **Staging** - Staging is a testing environment. All the builds are automatically deployed here from the continuous integration service (CircleCI).
* **UAT** - UAT is a testing environment for the customers.
* **Production** - As the name suggests.

OpenCHS has following services:

* **Server** - Web service accessed using REST API
* **Reporting and Dashboard** - [Metabase](http://www.metabase.com) based reporting and dashboard platform
* **Health worker application** - Android application
* **Self-Service Application** - Web Application
* **Identity and Access Management** - Amazon's [Cognito](https://aws.amazon.com/cognito/) service

OpenCHS online service runs following physical environments.

* **Production**
* **Staging**
* **UAT**\
  Important to note that demo is not a physical environment and it is configured as an organization (or tenant) in the Production physical environment.\
  Also, because we are using metabase which doesn't allow for a source code based version control, there is only one reporting service running on production and demo as well as staging will be organizations.

## 1. Create PEM file locally

```shell
#If you have the openchs/infra project run the following command.

ENCRYPTION_KEY_AWS=? make install
cp server/key/openchs-infra.pem ~/.ssh/openchs-infra.pem
chown $USER:$USER ~/.ssh/openchs-infra.pem
chmod 0600 ~/.ssh/openchs-infra.pem

#or run the following command
ENCRYPTION_KEY_AWS=? @openssl aes-256-cbc -a -md md5 -in server/key/openchs-infra.pem.enc -d -out server/key/openchs-infra.pem -k ${ENCRYPTION_KEY_AWS}

#ENCRYPTION_KEY_AWS=? 
# is the password which you need to get from the development team
# admin, once you become part of the team.
```

## 2. Use the following server details

User name = ec2-user\
Port = 22\
**Host names**

* ssh.staging.openchs.org (Staging, All builds are deployed here automatically.)
* ssh.uat.openchs.org (Staging, All builds are deployed here automatically.)
* ssh.server.openchs.org (Production. Demo is set up as a tenant here.)
* ssh.reporting.openchs.org (There is a single reporting server which runs staging, demo and production. This is because the way metabase works doesn't allow for version control via source code)

## 3. SSH to a server

ssh -i &lt;location of pem file&gt; ec2-user@&lt;environment-host-name&gt;\
**e.g.**\
ssh -i \~/.ssh/openchs-infra.pem [ec2-user@ssh.staging.openchs.org](mailto:ec2-user@ssh.staging.openchs.org)

You could make entries in your \~/.ssh/config file as follows:

```text OpenCHS Hosts
Host staging-server-openchs
    Hostname ssh.staging.openchs.org
    User ec2-user
    Port 22
    IdentityFile ~/.ssh/openchs-infra.pem

Host uat-server-openchs
    Hostname ssh.uat.openchs.org
    User ec2-user
    Port 22
    IdentityFile ~/.ssh/openchs-infra.pem

Host prod-server-openchs
    Hostname ssh.server.openchs.org
    User ec2-user
    Port 22
    IdentityFile ~/.ssh/openchs-infra.pem

Host reporting-server-openchs
    Hostname ssh.reporting.openchs.org
    User ec2-user
    Port 22
    IdentityFile ~/.ssh/openchs-infra.pem

Host prod-db-openchs
    Hostname serverdb.openchs.org
    User ec2-user
    Port 22
    IdentityFile ~/.ssh/openchs-infra.pem

Host staging-db-openchs
    Hostname stagingdb.openchs.org
    User ec2-user
    Port 22
    IdentityFile ~/.ssh/openchs-infra.pem

Host uat-db-openchs
    Hostname uatdb.openchs.org
    User ec2-user
    Port 22
    IdentityFile ~/.ssh/openchs-infra.pem
```

## 4. Connecting to the databases

Host: serverdb.openchs.org, stagingdb.openchs.org or uatdb.openchs.org\
Database: openchs\
User: openchs\
You would need to use ssh tunneling to the databases, as we haven't installed psql on the servers. Sample commands below to do this:

```shell
# 3333 is local port on your machine
# 5432 is remote port on prod-server
# prod-server alias defined in the config earlier/above
ssh -L 3333:stagingdb.openchs.org:5432 staging-server-openchs
# Once the tunnel is setup you can use below to connect to the database from command line. You can also use the parameters to connect via other tools. Important to note that you do not run the command on the shell that opens after the running the previous commnad. You should run this on the local machine/shell.
psql -h localhost -p 3333 -Uopenchs openchs
```