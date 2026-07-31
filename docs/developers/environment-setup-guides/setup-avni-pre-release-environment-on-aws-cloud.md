---
title: Setup an Avni environment on AWS cloud
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
The below steps are written down taking setup of prerelease environment as an example.

# Steps to setup the pre-release avni server on AWS

**Setup Avni Postgresql Database RDS**

1. Make use of a 2-3 day old Production RDS Automatic snapshots to create a pre-release RDS instance. While configuring it ensure the following
   1. **IMPORTANT: Use "t4g.small" type instance (2 cpu, 2 GB ram), it by default selects (m6.xlarge) which is extremely expensive**
   2. Edit Network config and
      1. Assign Prerelease Security-groups (Add db-sg SGs in corresponding VPC)
      2. Enable auto-assign of public ip
   3. Assign only 20GB GP2 storage
   4. disable system backups for this rds
   5. Update the prereelasedb.avniproject.org route to point to this RDS

**Setup Avni Server EC2 instancer**

1. From the pre-release ec2-template launch a new instance. **Use "t3.small" type instance (2 cpu, 2 GB ram). And enure Auto-assign public IP is enabled.**
2. Configure the above ec2 instance to include appropriate storage, network, Security-group, and CPU / RAM configuration

**Setup Network, Routes and permissions**

1. create internet gateway, and add it as target in your VPC route table.
2. create loadbalancer. setup SSL cert. Set target group to the EC2 instance.
3. Create a route for DB (type is cname), ssh, and application using avniproject.org hosted zone. openchs.org is deprecated.
4. create s3 bucket with existing bucket settings created for another env. (if S3 bucket exists, if needed empty it, to avoid using stale dumps)
5. create cognito user pool - there was no way to manua.
6. Create necessary user, iam policy and roles for ec2, s3 bucket and cognito user pool.

**Checklist for remaining setup (not detailed)**

1. setup avni-server
2. setup avni-webapp
3. set up rules server
4. Clean up stale s3 entries in DB
5. Create client pointing to pre-release
6. APK creation
7. login and test apk
8. Share apk

**Steps to setup avni-server, avni-client, avni-webapp, and rules-server with the above created AWS resources:**

1. > 📘 SSH in into pre-release server.
   >
   > Include the following in .bash_profile file:
   >
   > sudo vi ~/.bash_profile  
   > export LANG=en_US.UTF-8  
   > export LANGUAGE=en_US.UTF-8  
   > export LC_COLLATE=C  
   > export LC_CTYPE=en_US.UTF-8

2. run the above on the console as well

3. > 📘 create newRelic and openchs folder
   >
   > [ec2-user@ip-172-1-1-76 ~]$ sudo mkdir -p /opt/newrelic/
   >
   > [ec2-user@ip-172-1-1-76 ~]$ chmod 777 /opt/newrelic/  
   > chmod: changing permissions of ‘/opt/newrelic/’: Operation not permitted  
   > [ec2-user@ip-172-1-1-76 ~]$ sudo chmod 777 /opt/newrelic/  
   > [ec2-user@ip-172-1-1-76 ~]$ sudo mkdir -p /etc/openchs/  
   > [ec2-user@ip-172-1-1-76 ~]$ sudo chmod 777 /etc/openchs/  
   > [ec2-user@ip-172-1-1-76 ~]$ sudo vi /etc/openchs/openchs.conf  
   > ###paste pre-release openchs config from keeweb into this and save###

4. > 📘 Copy new-relic file to server
   >
   > scp newrelic.jar prerelease-server-openchs:/opt/newrelic/ newrelic.jar

5. Configure avni-server to use prerelease instead of prod for bugsnag

6. Trigger deploy of avni-server, ensure all deploy commands circle-ci config.yml of avni-server complete successfully (Triggering deploy will perform setup of the machine as required for backend app)

7. Once the avni-server backend app comes up, register the new instance as target in prerelease-openchs-load-balancer

8. Trigger deploy of avni-webapp, app should be soon available at [https://prerelease.avniproject.org/#/admin/user/6352/show](https://prerelease.avniproject.org/#/admin/user/6352/show)  
   (Triggering deploy will perform setup of the machine as required for web app)

9. Trigger deploy of rules-server (Triggering deploy will perform only initial setup of the machine as required for rules-server app)

10. > 📘 Fix pm2 setup issue for rules-server:
    >
    > a. Become rules user => sudo su - rules
    >
    > b. Follow steps specified in [https://medium.com/monstar-lab-bangladesh-engineering/deploying-node-js-apps-in-amazon-linux-with-pm2-7fc3ef5897bb](https://medium.com/monstar-lab-bangladesh-engineering/deploying-node-js-apps-in-amazon-linux-with-pm2-7fc3ef5897bb)  
    > till it asks for running command  
    > "sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemv -u rules --hp /home/rules"  
    > c. sudo mkdir -p /etc/pm2deamon  
    > d. sudo chmod 777 -R /etc/pm2deamon  
    > e. Run, below command to start rules-server after replacing the placeholders $1 and $2:
    >
    > sudo -H -u rules bash -c "cd /opt/rules-server && OPENCHS_UPLOAD_USER_USER_NAME=$1 OPENCHS_UPLOAD_USER_PASSWORD=$2 NODE_ENV=production pm2 start app.js --name rules-server --update-env"

11. > 📘 Go to Avni-client and run :
    >
    > make clean_all deps release_prerelease_without_clean upload-prerelease-apk
    >
    > Output : Pre-release APK Available at [\<[https://s3.ap-south-1.amazonaws.com/samanvay/openchs/prerelease-apks/prerelease-436d-2022-12-19-20-38-35.apk>](🔗)](🔗)

12. > 📘 In-order to avoid S3 errors during avni-client sync, connect to the DB and run below commands:
    >
    > update public.subject_type  set icon_file_s3_key = null where  icon_file_s3_key is not null;
    >
    > update public.news set hero_image = null where hero_image is not null;

13. Create IAM policy and associate it with IAM_USER
    1. Create a IAM policy prerelease_iam_policy similar to prod_iam_policy, except that the S3 bucket is “prerelease-user-media”.
    2. Associate prerelease_iam_policy with prerelease_iam_user.

14. **IMPORTANT: Never copy S3 content and specifically the Fast-sync files from Production to any other environment. When we apply the fast-sync, it modified the serverUrl, which will end up connecting our APK as client to Production environment.**

15. To setup newrelic agent on the server, refer [their](https://docs.newrelic.com/install/java/) documentation.

## Reference steps to deploy node and pm2 on Amazon linux:

> 📘 Update packages and install node and pm2:
>
> sudo yum update -y  
> Install necessary dev tools:  
> sudo yum install -y gcc gcc-c++ make openssl-devel git  
> Install Node.js:  
> curl --silent --location [https://rpm.nodesource.com/setup_10.x](https://rpm.nodesource.com/setup_10.x) | sudo bash -  
> sudo yum install -y nodejs  
> Install pm2:  
> sudo npm install pm2@latest -g
>
> Install git:  
> sudo yum install git  
> Setup env:  
> sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemv -u rules --hp /home/rules

## Steps specific to prerelease env setup before bugbash:

> **Read this before you start.** In January 2026 the prerelease integration service was brought up while it still held Goonj's *production* integration settings. Two integration systems ran against Goonj's live Salesforce at the same time, creating duplicate records and inventory mismatches. It took ten days to resolve and needed manual data repair on both Salesforce and Avni.
>
> The integration settings live in the integration service's own database, which on prerelease is also a copy of production's. So a restore brings production's integration configuration with it. The first step below, and the verification step, exist because of that incident. Do not skip them and do not reorder them.

* **Stop the integration service before anything else** - `sudo systemctl stop avni-int-service_appserver.service` on the prerelease integration host. Do not start it again until the clean-up and verification steps below have both passed. Starting it against a freshly restored database is what caused the January 2026 incident.
* Update the db of prerelease with prod db data. Do this before generating the prerelease apk, since generating of the apk is linked with updating platform translations in the server db.
* Stop the prerelease avni app server process - sudo systemctl stop avni_server_appserver.service
* Update the route53 dns entry for prereleasedb.avniproject.org to point to the RDS endpoint if changed
* Apply manual data-fixes if needed
* Trigger build from circleci to deploy app and apply Platform migrations if not already done
* (Only if specifically required to clean up S3 files) Delete all S3 folders within prerelease-user-media bucket, retaining the bucket as is.([https://s3.console.aws.amazon.com/s3/buckets/prerelease-user-media?region=ap-south-1&tab=objects](https://s3.console.aws.amazon.com/s3/buckets/prerelease-user-media?region=ap-south-1\&tab=objects))
* Clean-up Integration-system-config to prevent cross environment usage **Very Important**

  Run this against the **`avni_int` database** - the integration service's own database - and **not** against `prereleasedb`. They are two different databases. Cleaning `prereleasedb` does nothing for integrations.

  ```sql
  -- Step 1: Clear all integration config and switch every integration system off.
  DELETE FROM integration_system_config WHERE id >= 1;
  UPDATE integration_system SET is_voided = true;
  UPDATE goonj_adhoc_task SET is_voided = true;
  ```

  ```sql
  -- Step 2: ONLY when you later want specific integrations running on prerelease.
  -- Un-void them first. Step 1 voided every row, so an insert filtered on
  -- is_voided = false would match nothing and silently write no marker at all.
  UPDATE integration_system SET is_voided = false
   WHERE system_type IN ('Goonj', 'rwb');

  INSERT INTO integration_system_config (key, value, is_secret, integration_system_id, is_voided, uuid)
  SELECT 'int_env', 'prerelease', false, id, false, 'bea0db7a-719a-4b42-8840-db2047c54071'
    FROM integration_system
   WHERE system_type IN ('Goonj', 'rwb')
     AND is_voided = false
     AND NOT EXISTS (
           SELECT 1 FROM integration_system_config c
            WHERE c.integration_system_id = integration_system.id
              AND c.key = 'int_env');
  ```

  The service compares `AVNI_INT_ENV`, which avni-infra sets per environment, against this `int_env` row and skips the job when the two disagree.

  **That check does not cover every module.** It was added for Goonj and RWB; other modules ran without it. **Which modules are present, and which of them validate their environment, changes over time** as integrations are added and the check is extended - so do not trust the module names on this page as a current list. Confirm against the startup summary in the next step and against the integration-service code as it stands today. For any module that does not validate its environment, the clean-up above is the only thing protecting production.
  <br />
* Verify before trusting the clean-up **Very Important**

  Start the integration service only once the clean-up above has run, then read the startup summary in its log:

  ```
  ========== INTEGRATION SERVICE STARTUP SUMMARY ==========
  Active modules (n): ...
  Skipped modules (n): ...
  ==========================================================
  ```

  Every module listed as active must be one you meant to run on prerelease. If anything unexpected is active, stop the service before investigating. This summary exists because during the January 2026 incident nothing in the logs indicated that the Goonj job was running at all.
* Delete the urls of prod s3 icons in prereleasedb by doing the below:
  ```Text SQL
  set role none;
  update report_card set icon_file_s3_key=null where icon_file_s3_key is not null;
  update subject_type set icon_file_s3_key=null where icon_file_s3_key is not null;
  update news set hero_image=null where hero_image is not null;
  update individual set profile_picture = null where profile_picture is not null;
  update concept set media_url = null, media_type = null where media_url is not null;
  ```
* Delete the older prerelease db
