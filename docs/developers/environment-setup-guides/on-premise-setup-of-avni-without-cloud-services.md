---
title: On premise setup of Avni without cloud services
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
Avni uses cloud services like AWS Cognito, S3, and ALB for user management, file storage, and reverse proxy respectively - to implement certain functional and technical features. Usage of these services (and other non-functional components like RDS) makes the hosting and operations of Avni simple. In the future, Avni will continue to use such services - for the same reasons. But since Avni is an open-source project, some users of Avni want to deploy it in their own data centers. Avni project therefore also supports integration with open source alternatives to the functional services, and provide automation scripts, and documentation.

While elsewhere in this documentation you may find some Linux commands specific to centos, but Avni is shifting all its OS usage to Ubuntu 20-04. Hence any commands in this document are for Ubuntu.

As we have mentioned elsewhere in the documentation - unless required here we have not put the documentation for installation or setup of standard software packages like Postgres, Java, Ansible, etc - as they are available at so many places on the Internet. 

### Open-source alternatives supported by Avni are as follows:

| Functionality                         | AWS Service               | Open-source alternative |
| :------------------------------------ | :------------------------ | :---------------------- |
| IdentityManagement and Authentication | Cognito                   | KeyCloak                |
| Routing                               | Application Load Balancer | Ngnix                   |
| Storage                               | S3                        | Minio                   |

**Note:** Its important to note that, opting between Cognito or Keycloak for IdentityManagement and S3 or Minio for Storage management is a one-time choice. Once configured, switching to the other alternative could result in access / data loss.

## Pre-requisites

### Knowledge

- Familiarity with Linux Operating system
- Shell Command-line
- Ansible
- SSH
- Basic understanding of Web applications

### Work-machine

A Ubuntu / MacOS Work-system which has following installed:

- Python
- Git
- Ansible

### Target On-premise server specificatins

Target system with 5 different compute nodes, for following purpose with these minimum configurations:

1. Avni-Server (Including webapp and rules-server) : 2 vCPU, 4 GB RAM 
2. Keycloak : 2 vCPU, 4 GB RAM 
3. Minio : 2 vCPU, 4 GB RAM, with 200 GB storage
4. Postgres Server : 2 vCPU, 4 GB RAM, with 100 GB storage
5. Reverse-Proxy : 1 vCPU, 2 GB RAM

## Clone the following GIT Repositories

- [avni-client](https://github.com/avniproject/avni-client/tree/master) : Used for generating an Avni-client APK to use with rest of your Avni application 
- [avni-infra](https://github.com/avniproject/avni-infra/tree/master) : Used for performing installation of all the remote components of Avni application

# Preparation

In Avni, we make use of Ansible scripts available in "avni-infra" repository to setup Avni on an On-premise environment.

You may install Ansible on your work-system, by following instructions specified [here](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

Navigate to "avni-infra" repository, "configure" directory and perform following steps

1. Search and replace all instances of "security.lfe.avniproject.org" in "avni-infra" repository with your Company/project's domain name. Ex: "dev.mycompany.org"

2. Create a file "configure/group_vars/onpremise-secret-vars.yml" in "avni-infra" repository using "configure/group_vars/onpremise-secret-vars.template.yml" as the template and set appropriate values for all values enclosed in "{{<placeholder>}}"

3. Invoke the Encrypt command to save the newly created file, using your own encrytion_key file

```
make encrypt VAULT_PASSWORD_FILE=~/config/infra-pwd-file  
```

4. To be able to read encrypted file content decrypt it using, decrypting will overwrite the contents of "configure/group_vars/onpremise-secret-vars.yml", therefore if there are changes in "configure/group_vars/onpremise-secret-vars.yml", encrypt it and commit before invoking the decrypt command

```
make decrypt VAULT_PASSWORD_FILE=~/config/infra-pwd-file  
```

5. We then use this same encrytion_key file while invoking ansible playbooks for "Avni" applications installation

```
app_zip_path=~/<path>/rules-server/ make rules-server-onpremise VAULT_PASSWORD_FILE=~/config/infra-pwd-file  
```

# Database

## PostgreSQL Database (version 12.x)

- Install _postgresql-server_, _postgresql-contrib_ packages
- Initialize the database and start
- Change postgres login access from _peer to trust_ - Edit /var/lib/pgsql/VERSION/data/pg_hba.conf. As in line 2 and 9 of [this file](https://github.com/avniproject/avni-readme-docs/blob/master/example-code-files/pg_hba.conf).
- Restart postgres - _sudo systemctl restart postgresql-VERSION_
- [Create OpenCHS database, user and install extensions](https://raw.githubusercontent.com/avniproject/avni-readme-docs/master/example-code-files/create-database.sh)

# Keycloak

## Automated setup of KeyCloak using Ansible

We have ansible playbook to automate setup of Keycloak of "21.0.2" version for staging environment. This could be modified to suit change in target servers, Keycloak-version and credentials as per requirement.

- On your machine, clone [avni-infra](https://github.com/avniproject/avni-infra) repository and install ansible and other dependencies required
- Navigate to "configure" folder with-in the repository and trigger following "make" command with credential values as args:
  - keycloak_db_password=\<db_pwd> keycloak_admin_pwd=\<admin_pwd> keycloak_admin_api_secret=\<admin_api_secret> **make keycloak-staging**
- Above command internally invokes following ansible playbook command:
  - KEYCLOAK_DB_PASSWORD=$(keycloak_db_password) KEYCLOAK_ADMIN_PWD=$(keycloak_admin_pwd) KEYCLOAK_ADMIN_API_SECRET=$(keycloak_admin_api_secret) ansible-playbook staging_keycloak_servers.yml --tags=keycloak -i inventory/staging
- For setting up different version of Keycloak on any-other machine in a different environment, update the inventory file and/or targets and/or vars correspondingly

## Setting up KeyCloak manually

- Download and Install JDK 18, as instructed [here](https://www.itzgeek.com/how-tos/linux/ubuntu-how-tos/how-to-install-java-jdk-on-ubuntu.html)
- Download and install keycloak 21 `https://github.com/keycloak/keycloak/releases/download/21.0.2/keycloak-21.0.2.zip` preferrably in `/opt/keycloak-21.0.2/` folder.
- Install Postgres 12 (unless you have a Postgres server running already which can be reused for Keycloak). You may use the instructions [here](https://www.itzgeek.com/post/how-to-install-postgresql-on-ubuntu-20-04/)
- Create a user and database in the Postgres server

```pgsql
create user keycloak with password 'password';
create database keycloak;
GRANT ALL PRIVILEGES ON database keycloak TO keycloak;
```

- Configure keycloak to work with Postgres  
  `bin/kc.sh build --db postgres`

#### Create a certificate, pem file, to run keycloak on TLS

This is recommended even if you are will be putting Nginx or Apache as a reverse proxy. We do not recommend SSL termination - as that will means an unencrypted password in the internal network visible to production support engineers.

- Option 1: Commands for self-generated keys and certificates are given below. You should decide whether self-generated keys and certificate is the right option for you.

```shell
openssl req -newkey rsa:2048 -nodes -keyout domain.key -out domain.csr
openssl x509 -signkey domain.key -in domain.csr -req -days 365 -out domain.crt
```

- Option 2: Commands to generate keys and certificates using Certbot 

```shell
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo apt install net-tools
sudo certbot certonly --standalone -d keycloak-staging.avniproject.org --staple-ocsp -m <user_email> --agree-tos
mkdir -p ${HOME}/.keycloak/certs
cd 
sudo cp /etc/letsencrypt/live/keycloak-staging.avniproject.org/fullchain.pem ${HOME}/.keycloak/certs/public.crt
sudo cp /etc/letsencrypt/live/keycloak-staging.avniproject.org/privkey.pem ${HOME}/.keycloak/certs/private.key
sudo chown ubuntu:ubuntu ${HOME}/.keycloak/certs/public.crt
sudo chown ubuntu:ubuntu ${HOME}/.keycloak/certs/private.key
```

- A [basic starter realm](https://raw.githubusercontent.com/avniproject/infra/master/integration/configure/roles/keycloak/files/starter-realm.json) can be imported into keycloak by using `bin/kc.sh import --file=basic_realm.json`. Note the server must be stopped. The realm which we will use is called On-premise (not master). You can change the name of the realm before/after import too - the name is configurable.
- Each Avni user's authentication details are present in Keycloak, whereas application-specific details are in the Avni database. To ensure consistency and linking of a user entity across these two databases all user modification/creation calls are routed through the Avni server. In order to do this, the Avni server needs client access to Keycloak's realm. This client's name is admin-api, you should be able to find it in the Clients tab. Since in the above step you have imported a common realm file, you must regenerate the client secret. For this, open the admin-api client go to the credentials tab, and then `Regenerate Secret`. This secret value will be provided to the avni-server as described below.
- If you are running keycloak such that you cannot log in via browser from localhost as admin, then you can set two environment variables for first-time access. You should choose a strong password. Also, set KEYCLOAK_HOME to point to the Keycloak installation directory.  
  Add below lines to /etc/environment file:

```shell
KEYCLOAK_ADMIN=admin
KEYCLOAK_ADMIN_PASSWORD=<strong-password>
KEYCLOAK_HOME=/opt/keycloak-21.0.2/
```

- Logout user and log back in 

### Configure Web-origins

In keycloak, "clients" tab, edit the "avni-client" client, and set the Settings "Web origins" to the Host Avni-server url.

### Optional Configurations

#### Track Events

In order to show the last login time in the webapp, keycloak events need to be enabled and can be done via keycloak admin at `Realm -> Realm Settings -> Events -> User events settings / Admin events settings`. Also, ensure that one of the roles associated with the`admin-api` client has the `view-events` associated role configured.

#### Password Policy

Password policy can be setup at `Realm -> Authentication -> Policies -> Password Policy`

#### Security Defenses

Security defenses including brute force detection can be configured at `Realm -> Realm Settings -> Security defenses`

### Start keycloak

- Option 1: with configuration passed as CLI parameters:  
  `bin/kc.sh start --hostname-strict=false --db-url-host localhost --db-username keycloak --db-password password --https-certificate-file=./domain.crt --https-certificate-key-file=./domain.key`
- Option 2: with configurations specified in keycloak conf file `conf/keycloak.conf`:  
   `nohup $KEYCLOAK_HOME/bin/kc.sh start &`  
  Find more details [here](https://www.keycloak.org/server/configuration).

## Setup Super Admin User

Assuming that you have started avni-server after deployment at least once, you should have an `admin` user in your database who doesn't belong to any organization and is a super admin. Before you use the web app to log in as this user and create other organizations and users - this first user needs to be added to keycloak.

In Keycloak you can add a user in the same realm (not master). For this user please note the following:

- In server database,
  - create user in users table - set `disabled_in_cognito` to true.
  - Create an entry for the user created above in `account_admin` table.
- In keycloak,
  - User is enabled
  - Email is marked as verified
  - Add a custom attribute called `custom:userUUID` and in the value provide the UUID value from the Avni database for the `admin` user (present in the `users` table).

## Create Org User

In Organisations with Keycloak as the IDP, you need to be logged in as Keycloak Super Admin in-order to create the users. Or, if an org user with admin privileges already exists, use the same to create / update users in that organisation.

## Setup Avni Server to use Keycloak

Avni server by default uses Cognito, hence you need to set up the following environment variables to make it work with Keycloak.

```shell
export OPENCHS_KEYCLOAK_CLIENT_SECRET=dsfhdfdsfh323
export OPENCHS_KEYCLOAK_SERVER=https://keycloak.example.com
export OPENCHS_KEYCLOAK_ENABLED=true
export AVNI_IDP_TYPE=keyclaok
```

For local setups, setting `OPENCHS_KEYCLOAK_SERVER` to `http://localhost:8080 `will not work as react-native in dev mode will not be able to connect to it. Ensure to provide an FQDN or an IP to the keycloak instance being used.

### Test

Launch the avni web app and you must be able to log in using the `admin` user and create organization etc.

### A few more notes

Even though you are not using Cognito you may see the field `disabledInCognito` in Keycloak user attributes and in the REST API contract exchanged between the avni web app and the avni server.

# Minio

## Automated setup of MinIO using Ansible

We have ansible playbook to automate setup of MinIO for staging environment. This could be modified to suit changes in credentials, users, buckets, access and Minio-setup type (Cluster Vs Single Node), etc..

- On your machine, clone [avni-infra](https://github.com/avniproject/avni-infra) repository and install ansible and other dependencies required
- Navigate to "configure" folder with-in the repository and trigger following "make" command with credential values as args:
  ```
  minio_root_user=\<root_user> minio_root_password=\<root_pwd> minio_upload_user=\<upload_user> minio_upload_password=\<upload_pwd> **make minio-staging**
  ```
- Above command internally invokes following ansible playbook command:
  ```
  MINIO_ROOT_USER=$(minio_root_user) MINIO_ROOT_PASSWORD=$(minio_root_password) MINIO_UPLOAD_USER=$(minio_upload_user) MINIO_UPLOAD_PASSWORD=$(minio_upload_password) ansible-playbook staging_minio_servers.yml -i inventory/staging
  ```
- For setting up MinIO on any-other machine in a different environment, update the inventory file and/or targets and/or vars correspondingly

## Setting up MinIO server manually

Refer MinIO Quickstart guide to setup the MinIO server on Ubuntu Machine - <https://docs.min.io/docs/minio-quickstart-guide.html>

- Generate and apply SSL certificates to the MinIO server - <https://docs.min.io/docs/generate-let-s-encypt-certificate-using-concert-for-minio>
- Install certbot using snap

```shell
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo apt install net-tools
```

- Generate SSL certificates  
  `sudo certbot certonly --standalone -d minio-staging.avniproject.org --staple-ocsp -m <user_email> --agree-tos`
- Ensure that you configure Minio to have "ap-south-1" as the Region.
- Configure Minio server to use the SSL certificates

```shell
sudo cp /etc/letsencrypt/live/minio-staging.avniproject.org/fullchain.pem ${HOME}/.minio/certs/public.crt
sudo cp /etc/letsencrypt/live/minio-staging.avniproject.org/privkey.pem ${HOME}/.minio/certs/private.key
sudo chown ubuntu:ubuntu ${HOME}/.minio/certs/public.crt
sudo chown ubuntu:ubuntu ${HOME}/.minio/certs/private.key
sudo setcap 'cap_net_bind_service=+ep' ./minio
```

- You also need to set two environment variables for Minio web console app to correctly redirect to server.  
  Add below lines to /etc/environment file and logout and login back to the machine.

```shell
MINIO_BROWSER_REDIRECT_URL="https://minio-staging.avniproject.org"
MINIO_SERVER_URL="https://minio-staging.avniproject.org:442"
```

- Start minio server as a background process  
  `cd ~/minio`  
  `nohup ./minio server ./data --address ":442" --console-address ":443" >> /var/log/minio/minio.log 2>&1 &`

## Setup Avni Server to use Minio

Avni server by default uses local storage in "dev" environment and AWS S3 in Production and Staging environments, therefore you would need to set up the following environment variables to make it work with Minio.

## Configure the organisations to use Keycloak as IDP and Minio for Storage

Configure Organisations to use Keycloak and Minio as IDP and Storage service systems.

```Text SQL
UPDATE public.organisation_config SET settings = '{"languages": ["en"], "useKeycloakAsIDP":  true, "useMinioForStorage": true }' WHERE id in (org_ids);
```

### To ensure it works with Minio, set the following properties in application.properties:

```
avni.bucketName=${OPENCHSBUCKET_NAME:dummy}  
minio.url=${OPENCHS_MINIO_URL:<http://.*minio.*:9000}>  
minio.accessKey=${OPENCHS_MINIO_ACCESS_KEY:dummy}  
minio.secretAccessKey=${OPENCHS_MINIO_SECRET_ACCESS_KEY:dummy}  
avni.connectToS3InDev=true  
minio.s3.enable=true
```

### Important Note: Please ensure that the "minio.url" property has a value with the word "minio" in it. This is required for satisfying SDK requirements for connecting to Minio.

If setting up minio on local (localhost:9000, etc..), then please add a /etc/hostname file entry to map the minio ip and port to a hostname value with the word "minio". Specify that hostname as value for "minio.url".

# Avni Native components deploy

## Automated setup of Avni-server and Avni-webapp using Ansible

We have ansible playbook "avni_appserver" to automate setup of avni-server for on-premise environment. This could be modified to suit change in target servers, configurations and credentials as per requirement.

Download an avni-server jar file to be used for deployment

- You can download it by replacing the build number in the <https://circleci.com/gh/avniproject/avni-server/>\<server_build_number>#artifacts by the build server number you got from release info page.

Ex: ~/artifacts/avni-server-0.0.1-SNAPSHOT.jar

Download a avni-webapp tar file to be used for deployment

- You can download it by replacing the build number in the <https://circleci.com/gh/avniproject/avni-webapp/>\<server_build_number>#artifacts by the build server number you got from release info page.

Ex: ~/artifacts/avni-webapp.tgz

Navigate to "configure" folder with-in the "avni-infra" repository and trigger following "make" command with credential values as args:

```Text Deploy Avni-server and Avni-webapp
web_zip_path=~/<path>/ app_zip_path=~/<path>/ make avni-onpremise VAULT_PASSWORD_FILE=~/config/infra-pwd-file  
```

Above command internally invokes following ansible playbook command:

```
WEBAPP_ZIP_PATH=$(web_zip_path) WEBAPP_ZIP_FILE_NAME=avni-webapp.tgz APPLICATION_ZIP_PATH=$(app_zip_path) APPLICATION_ZIP_FILE_NAME=avni-server-0.0.1-SNAPSHOT.jar ansible-playbook onpremise_avni_servers.yml -i inventory/onpremise  --vault-password-file ${VAULT_PASSWORD_FILE}
```

## Automated setup of Rules-server using Ansible

We have ansible playbook "rules_server" to automate setup of avni-server for on-premise environment. 

Download a rules-server zip file to be used for deployment

- You can download it by replacing the build number in the <https://circleci.com/gh/avniproject/rules-server/>\<server_build_number>#artifacts by the build server number you got from release info page.

Ex: ~/artifacts/rules-server.tgz

Navigate to "configure" folder with-in the "avni-infra" repository and trigger following "make" command with credential values as args:

```Text Deploy Rules-server
app_zip_path=~/<path>/ make rules-server-onpremise VAULT_PASSWORD_FILE=~/config/infra-pwd-file  
```

Above command internally invokes following ansible playbook command:

```
APPLICATION_ZIP_PATH=$(app_zip_path) APPLICATION_ZIP_FILE_NAME=rules-server.tgz ansible-playbook onpremise_rules_server.yml -i inventory/onpremise  --vault-password-file ${VAULT_PASSWORD_FILE}
```

## Create a flavour of Avni-client for your avni-server instance

Please refer [this](https://avni.readme.io/docs/flavouring-avni#steps-to-create-a-new-flavor-in-client) document on steps to create your own flavour of Avni APK.