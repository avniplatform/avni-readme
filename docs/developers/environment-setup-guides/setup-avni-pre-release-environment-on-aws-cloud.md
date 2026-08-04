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
   1. **Instance class: use `db.t4g.medium`.** The console defaults to `m6.xlarge`, which is extremely expensive. Do not go down to `db.t4g.small` either - that is 2 GB RAM against a restored database of well over 100 GiB.
   2. Edit Network config and
      1. Assign Prerelease Security-groups (Add db-sg SGs in corresponding VPC)
      2. Enable auto-assign of public ip
   3. **Storage: you will get 300 GB gp3, and you cannot choose less.** A restore cannot allocate less storage than the snapshot carries (every prod snapshot reports `AllocatedStorage: 300`), and allocated storage can never be reduced afterwards. An earlier version of this page said "assign only 20GB GP2" - that is impossible for a prod restore, and describes `staging-db` rather than prerelease. Budget accordingly: 300 GB gp3 is roughly $27/month, and you pay it twice over until the old prerelease DB is deleted.
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
5. create cognito user pool - there was no way to manua. **Note that prerelease currently shares production's Cognito user pool** (`avni_server_cognito_user_pool_id` is identical in the prod and prerelease vaults). That is what lets a production user log in to prerelease after a refresh, but it also means user administration on prerelease writes to production - `CognitoIdpService` calls `adminCreateUser` and `adminDeleteUser` against the shared pool. Never create, edit or delete users from the prerelease environment.
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

**Which parts of this page apply to a refresh.** Everything above is one-time setup and is already done - the EC2 instances, load balancer, S3 bucket, Cognito pool and IAM policy all exist, so reuse them. For a refresh you need only:

* the **Setup Avni Postgresql Database RDS** block at the top of this page, because a snapshot restore always creates a *new* RDS instance - there is no way to restore into the existing one
* this section
* two standing warnings from above: step 14 (never copy S3 fast-sync files from production) and the Cognito note at step 5 (never create, edit or delete users from prerelease)

> **Read this before you start.** In January 2026 the prerelease integration service was brought up while it still held Goonj's *production* integration settings. Two integration systems ran against Goonj's live Salesforce at the same time, creating duplicate records and inventory mismatches. It took ten days to resolve and needed manual data repair on both Salesforce and Avni.
>
> The integration settings live in the integration service's own database, which on prerelease is also a copy of production's. So a restore brings production's integration configuration with it. The first step below, and the verification step, exist because of that incident. Do not skip them and do not reorder them.

* **Stop *and disable* the integration service before anything else** - on the prerelease integration host:

  ```
  sudo systemctl stop avni-int-service_appserver.service
  sudo systemctl disable avni-int-service_appserver.service
  ```

  `disable` is not optional. The unit is generated from `appserver.service.template.j2` in avni-infra with `Restart=always` and `WantedBy=multi-user.target`, so stopping it does not survive a reboot - if the host restarts at any point during the refresh, systemd brings the service straight back up against the freshly restored production integration config. That is the January 2026 incident re-armed by an unrelated reboot. Re-enable and start it only after the clean-up and verification steps below have both passed.
* Stop the prerelease avni app server process - sudo systemctl stop avni_server_appserver.service
* Update the db of prerelease with prod db data. Do this before generating the prerelease apk, since generating of the apk is linked with updating platform translations in the server db.
* Update the route53 dns entry for prereleasedb.avniproject.org to point to the RDS endpoint if changed

  **This one DNS change swaps two databases, not one.** Both the server database and the integration service's `avni_int` database live on the same RDS instance and are reached through this same CNAME - compare `avni_server_db_host` and `int_appserver_db_url` in the prerelease vars. The integration service's connection string does not mention production anywhere, yet the moment this record moves it is pointed at a copy of production's integration config. That is why the service has to be stopped and disabled before you get here.
* Expect the restored instance to be slow for the first few hours, and do not treat it as a fault

  A restored RDS volume is materialised from the snapshot lazily. Every block is fetched from AWS's snapshot storage the first time it is read and only then written onto the volume; reads after that are local. This is why the instance becomes `available` in ~15 minutes instead of taking hours to copy 300 GB - the copying is deferred, not skipped.

  Measured during the August 2026 refresh, on first touch: **~3 MB/s throughput and ~100 IOPS**, against the ~125 MB/s and 3000 IOPS a gp3 volume normally sustains. A single `update individual set profile_picture = null` over a 2 GB table took about 12 minutes. Nothing is misconfigured; it is a one-time cost per block, and a bigger instance class will not change it.

  Pay that cost in bulk before handing the environment to QA rather than letting testers absorb it one screen at a time:

  ```sql
  VACUUM (ANALYZE) individual, encounter, program_encounter, program_enrolment;
  ```

  It will be slow - that is the point. It also refreshes planner statistics, which arrive sized for production's larger instance.
  <br />
* Apply manual data-fixes if needed
* Trigger build from circleci to deploy app and apply Platform migrations if not already done
* (Only if specifically required to clean up S3 files) Delete all S3 folders within prerelease-user-media bucket, retaining the bucket as is.([https://s3.console.aws.amazon.com/s3/buckets/prerelease-user-media?region=ap-south-1&tab=objects](https://s3.console.aws.amazon.com/s3/buckets/prerelease-user-media?region=ap-south-1\&tab=objects))
* Clean-up Integration-system-config to prevent cross environment usage **Very Important**

  Run this against the **`avni_int` database** - the integration service's own database - and **not** against `prereleasedb`. They are two different databases. Cleaning `prereleasedb` does nothing for integrations.

  ```sql
  -- Step 1: Clear all integration config and switch every integration system off.
  -- The DELETE is unconditional on purpose - every row here came from production.
  -- Deleting rather than blanking matters: with a key absent the service falls back
  -- to its sandbox defaults (e.g. Goonj's stage Salesforce), whereas a surviving
  -- production value would be used as-is.
  DELETE FROM integration_system_config;
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
  SELECT 'int_env', 'prerelease', false, id, false, uuid_generate_v4()
    FROM integration_system
   WHERE system_type IN ('Goonj', 'rwb')
     AND is_voided = false
     AND NOT EXISTS (
           SELECT 1 FROM integration_system_config c
            WHERE c.integration_system_id = integration_system.id
              AND c.key = 'int_env');
  ```

  The service compares `AVNI_INT_ENV`, which avni-infra sets per environment, against this `int_env` row and skips the job when the two disagree.

  **The `is_voided` updates in Step 1 are bookkeeping, not protection.** The scheduler reads integration systems and their config through repositories that do not filter on `is_voided`, so a voided row is still loaded at startup. What actually stops a job is a failed environment check or an unusable cron - which is why the `DELETE` is the load-bearing statement. Never treat "everything is voided" as a safe state.

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

  ```sql
  set role none;
  update report_card set icon_file_s3_key=null where icon_file_s3_key is not null;
  update subject_type set icon_file_s3_key=null where icon_file_s3_key is not null;
  update news set hero_image=null where hero_image is not null;
  update individual set profile_picture = null where profile_picture is not null;
  update concept set media = null where media is not null;
  ```

  The `concept` line changed. avni-server migration `V1_366__RefactorConceptMediaToJsonb.sql` dropped `concept.media_url` and `concept.media_type` and replaced them with a single `media` jsonb column, so the old statement had been failing with `column "media_url" does not exist` and concept media was never actually cleaned. Despite the column names, these all hold full URLs including the bucket - for example `https://s3.ap-south-1.amazonaws.com/prod-user-media/<org>/icons/<uuid>.jpg` - not bare S3 keys.
  <br />
* Media inside observations is deliberately left as it is

  Image, audio and video answers are stored as full production S3 URLs inside jsonb, and a restore brings them all across. As of this writing that is 11 columns on 8 tables - `individual`, `encounter`, `program_encounter`, `program_enrolment`, `individual_relationship`, `checklist_item`, `subject_program_eligibility` and `task`, several of them with a second `cancel_observations`, `exit_observations` or `program_exit_observations` column. Re-derive the list rather than trusting this one:

  ```sql
  SELECT c.table_name, c.column_name
    FROM information_schema.columns c
    JOIN information_schema.tables t
      ON t.table_schema = c.table_schema AND t.table_name = c.table_name
   WHERE c.table_schema = 'public' AND c.data_type = 'jsonb'
     AND c.column_name LIKE '%observations%'
     AND t.table_type = 'BASE TABLE'
   ORDER BY 1, 2;
  ```

  These are not rewritten, because rewriting jsonb across the largest tables in a 100+ GiB database costs hours on a 2 vCPU instance and buys nothing. The URLs cannot resolve through the application regardless of what they say: `AWSS3Service.generateMediaDownloadUrl` parses the stored URL and throws `AccessDeniedException` when its bucket does not match the configured `avni.bucketName`, which on prerelease is `prerelease-user-media`. Every media download from the webapp and the app goes through that method.

  The prerelease database has carried production observation URLs since the July 2025 refresh with no incident, which is the practical evidence that this is inert.

  > **That application check is currently the only control - IAM does not back it up.** `prerelease_iam_policy` grants its object-level S3 actions on `arn:aws:s3:::*/*`, which includes `prod-user-media`; only the bucket-level ARN is scoped to `prerelease-user-media`. The prerelease credentials can therefore read, overwrite and delete production media objects. Step 13 above intends this policy to be prod's "except that the S3 bucket is prerelease-user-media", and that exception was never applied to the object ARN. Until it is fixed, treat the code check as load-bearing and do not add a second environment on the assumption IAM will contain it.

  **Record the evidence instead of assuming it.** This is bounded to 1% of blocks and returns in seconds:

  ```sql
  SELECT DISTINCT substring(observations::text from '[a-z0-9-]+-user-media') AS bucket
    FROM individual TABLESAMPLE SYSTEM (1)
   WHERE observations::text ~ '[a-z0-9-]+-user-media';
  ```

  Expect `prod-user-media` in the output. That is the known and accepted state - note it on the refresh card so the next person does not read it as a leak. What would be a genuine problem is a *resolvable* production reference, and the two controls above are what prevent that.
* Delete the older prerelease db. Do this once the new one is serving - two 300 GB gp3 instances cost roughly $27/month each, so leaving the old one running doubles the bill.
