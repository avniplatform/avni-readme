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
[block:api-header]
{
  "title": "About various shared environments"
}
[/block]
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
  * **UAT**
Important to note that demo is not a physical environment and it is configured as an organization (or tenant) in the Production physical environment.
Also, because we are using metabase which doesn't allow for a source code based version control, there is only one reporting service running on production and demo as well as staging will be organizations.
[block:api-header]
{
  "type": "basic",
  "title": "1. Create PEM file locally"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "#If you have the openchs/infra project run the following command.\n\nENCRYPTION_KEY_AWS=? make install\ncp server/key/openchs-infra.pem ~/.ssh/openchs-infra.pem\nchown $USER:$USER ~/.ssh/openchs-infra.pem\nchmod 0600 ~/.ssh/openchs-infra.pem\n\n#or run the following command\nENCRYPTION_KEY_AWS=? @openssl aes-256-cbc -a -md md5 -in server/key/openchs-infra.pem.enc -d -out server/key/openchs-infra.pem -k ${ENCRYPTION_KEY_AWS}\n\n#ENCRYPTION_KEY_AWS=? \n# is the password which you need to get from the development team\n# admin, once you become part of the team.",
      "language": "shell"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "2. Use the following server details"
}
[/block]
User name = ec2-user
Port = 22
**Host names**
  * ssh.staging.openchs.org (Staging, All builds are deployed here automatically.)
  * ssh.uat.openchs.org (Staging, All builds are deployed here automatically.)
  * ssh.server.openchs.org (Production. Demo is set up as a tenant here.)
  * ssh.reporting.openchs.org (There is a single reporting server which runs staging, demo and production. This is because the way metabase works doesn't allow for version control via source code)
[block:api-header]
{
  "title": "3. SSH to a server"
}
[/block]
ssh -i <location of pem file> ec2-user@<environment-host-name>
**e.g.** 
ssh -i ~/.ssh/openchs-infra.pem ec2-user@ssh.staging.openchs.org

You could make entries in your ~/.ssh/config file as follows:
[block:code]
{
  "codes": [
    {
      "code": "Host staging-server-openchs\n    Hostname ssh.staging.openchs.org\n    User ec2-user\n    Port 22\n    IdentityFile ~/.ssh/openchs-infra.pem\n\nHost uat-server-openchs\n    Hostname ssh.uat.openchs.org\n    User ec2-user\n    Port 22\n    IdentityFile ~/.ssh/openchs-infra.pem\n\nHost prod-server-openchs\n    Hostname ssh.server.openchs.org\n    User ec2-user\n    Port 22\n    IdentityFile ~/.ssh/openchs-infra.pem\n\nHost reporting-server-openchs\n    Hostname ssh.reporting.openchs.org\n    User ec2-user\n    Port 22\n    IdentityFile ~/.ssh/openchs-infra.pem\n\nHost prod-db-openchs\n    Hostname serverdb.openchs.org\n    User ec2-user\n    Port 22\n    IdentityFile ~/.ssh/openchs-infra.pem\n\nHost staging-db-openchs\n    Hostname stagingdb.openchs.org\n    User ec2-user\n    Port 22\n    IdentityFile ~/.ssh/openchs-infra.pem\n\nHost uat-db-openchs\n    Hostname uatdb.openchs.org\n    User ec2-user\n    Port 22\n    IdentityFile ~/.ssh/openchs-infra.pem",
      "language": "text",
      "name": "OpenCHS Hosts"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "4. Connecting to the databases"
}
[/block]
Host: serverdb.openchs.org, stagingdb.openchs.org or uatdb.openchs.org
Database: openchs
User: openchs
You would need to use ssh tunneling to the databases, as we haven't installed psql on the servers. Sample commands below to do this:
[block:code]
{
  "codes": [
    {
      "code": "# 3333 is local port on your machine\n# 5432 is remote port on prod-server\n# prod-server alias defined in the config earlier/above\nssh -L 3333:stagingdb.openchs.org:5432 staging-server-openchs\n# Once the tunnel is setup you can use below to connect to the database from command line. You can also use the parameters to connect via other tools. Important to note that you do not run the command on the shell that opens after the running the previous commnad. You should run this on the local machine/shell.\npsql -h localhost -p 3333 -Uopenchs openchs",
      "language": "shell"
    }
  ]
}
[/block]