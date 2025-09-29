---
title: Release process for the cloud
excerpt: This outlines the release process for Avni cloud
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Avni release process

### What is the current deployed release?

The current release on production can be identified through the following mechanisms

1. The latest [release notes](https://github.com/avniproject/avni-product/releases/) on avni-product
2. The latest release blog on the [Avni blog](https://avniproject.org/blog/)
3. The latest communication via email sent to [avni-users@googlegroups.com](mailto:avni-users@googlegroups.com) (usually with the subject "Avni Release Announcement")

### What is the release we are working on?

Releases are marked "Active" on the [Roadmap](https://github.com/orgs/avniproject/projects/2/views/7)

### Release numbering

All versions of below specified end-deployables are marked with a release number that corresponds to the release on the project board, whether they are deployed or not. This makes it easier to understand the right version of the production. For the release, we also mark avni-product with the same release number and provide combined release notes there.

* avni-client
* avni-server
* avni-webapp
* rules-server
* avni-models
* integration-service
* integration-admin-app
* avni-etl
* avni-media
* avni-product
* avni-infra

Other smaller packages such as avni-models and rules-config have their own release cycles that do not correspond to anything. We use semantic versioning for these packages.

### Branch cut-off for upcoming release

* Ensure that all previous release changes have been merged to the repo's master/main branch as well as the upcoming release branch

  ```shell Commands to check extra commits in previous release not present in master/main
  git fetch --all
  git checkout <prev-release-number>
  git pull

  git checkout master
  git pull
  git log master..<prev-release-number>
  ```
  ```shell Merge previous release branch to master if needed
  git merge <prev-release-number>
  git push origin master
  git status
  ```
* Create a new branch. Ensure builds pass and deployment happens on the staging server
  ```shell
  # Perform this for all end-deployables
  # avni-product, avni-client, avni-server, avni-webapp, rules-server, avni-models
  # avni-infra, avni-etl, avni-media, integration-admin-app and integration-service
  # release-number: 3.36 , tag-number: v3.36.0
  git stash
  git pull --rebase
  git checkout -b <release-number>
  git push --set-upstream origin <release-number>
  ```
* Inform in the common channel(both team members present) about the new branch created

### Patch deployments

* Happens from the release branch by increasing the patch version by a number (eg: 3.35.0 if patched will have a version of 3.35.1).
* Create release notes in the respective repos and in avni-product repo and tag the commits in the respective repos with the patch release version. This applies to the patch fixes that we do for the issues raised for the app in Open testing track. When a patch release is done, to fix issues of the app in Open testing track, in its corresponding release notes, add a line mentioning the link for the main release notes.
* The latest tag with the bigger no(across repos) will be our release version to be mentioned in the mail to our users. If this deviates from mobile app version, or if a release doesn't have a mobile app release, mention the same in the mail to the users.
* Merge the branch across repos to the main branch / next release branch if already cut.
* Update [release tracker](https://docs.google.com/spreadsheets/d/1LcyE3_Ht_YztBjfPpedvjUtvzcJezP5dFOZFOyzWs2E/edit#gid=0) if applicable.

### Release preparation

* Ensure that all previous release changes have been merged to the repo's master/main branch as well as the upcoming release branch

```shell Commands to check extra commits in previous release not present in master/main
git fetch --all
git checkout <prev-release-number>
git pull

git checkout master
git pull
git log master..<prev-release-number>
```

```shell Merge previous release branch to master if needed
git merge <prev-release-number>
git push origin master
git status
```

Update the avni-models release version if merge was done there as part of above step

* If models version was updated, also update the client, webapp and rules-server repos with the latest models version
* Create release on Github avni-product Repo Only, using the new release branch. Configure the release on github to automatically create a tag on the release branch when we publish the release.
* To create release notes with changes across all the repos, you may use GitHub command to fetch list of card titles
  ```Text Shell
  gh project item-list -L 3000 --owner avniproject --format json 2 | jq -c '.items[] | select (.release == "<Specify release number, ex:5.1.0>") | [ .title, .content.url]'
  ```

### Deploy to production

* Inform in the common channel(both team members present) about the release progress
* Tag all end-deployables in release branch, if not done
  ```shell
  # Perform this for all end-deployables
  # avni-product, avni-client, avni-server, avni-webapp, rules-server, avni-models
  # avni-infra, avni-etl, avni-media, integration-admin-app and integration-service
  # release-number: 3.36 , tag-number: v3.36.0
  git tag <tag-number>
  git push origin --tags
  ```

#### Backup Latest PROD DB snapshot for long term use

* Find the Latest System Snapshot for Avni PROD DB RDS, and invoke "Copy snapshot" action on it. Name the snapshot "bkp-$\{Source snapshot name}". Ex: bkp-proddb02-2024-02-13-23-12

#### avni-server

* Find the passing circle-ci job for the tag. If the job was triggered more than a week ago, retrigger a fresh build from circleci after selecting the repo and the branch so that the artifacts from the build step are available in the deploy steps.
* Check if there are any running Background jobs on Prod and if needed wait for them to complete
* ```Text Commands to show status of Started and Pending Background jobs that were triggered today
  select users.username, jobs.*
  from (select bje.job_execution_id                                                                                execution_id,
               bje.status                                                                                          status,
               bje.exit_code                                                                                       exit_code,
               bji.job_name                                                                                        "Type of Job",
               bje.create_time                                                                                     create_time,
               bje.start_time                                                                                      start_time,
               bje.end_time                                                                                        end_time,
               string_agg(case when bjep.parameter_name = 'uuid' then bjep.parameter_value else '' end::text, '')  uuid,
               string_agg(case when bjep.parameter_name = 'fileName' then bjep.parameter_value else '' end::text,
                          '')                                                                                      fileName,
               sum(case
                       when bjep.parameter_name = 'noOfLines' then CAST(nullif(bjep.parameter_value, '') AS integer)
                       else 0 end)                                                                                 noOfLines,
               string_agg(case when bjep.parameter_name = 's3Key' then bjep.parameter_value else '' end::text, '') s3Key,
               sum(case
                       when bjep.parameter_name = 'userId' then CAST(nullif(bjep.parameter_value, '') AS integer)
                       else 0 end)                                                                                 userId,
               string_agg(case when bjep.parameter_name = 'type' then bjep.parameter_value::text else '' end::text,
                          '')                                                                                      job_type,
               max(case
                       when bjep.parameter_name = 'startDate' then CAST(nullif(bjep.parameter_value, '') AS timestamp)
                       else null::timestamp end::timestamp)                                                        startDate,
               max(case
                       when bjep.parameter_name = 'endDate' then CAST(nullif(bjep.parameter_value, '') AS timestamp)
                       else null::timestamp end::timestamp)                                                        endDate,
               string_agg(case
                              when bjep.parameter_name = 'subjectTypeUUID' then bjep.parameter_value::text
                              else '' end::text,
                          '')                                                                                      subjectTypeUUID,
               string_agg(case when bjep.parameter_name = 'programUUID' then bjep.parameter_value::text else '' end::text,
                          '')                                                                                      programUUID,
               string_agg(
                       case
                           when bjep.parameter_name = 'encounterTypeUUID' then bjep.parameter_value::text
                           else '' end::text,
                       '')                                                                                         encounterTypeUUID,
               string_agg(case when bjep.parameter_name = 'reportType' then bjep.parameter_value::text else '' end::text,
                          '')                                                                                      reportType,
               max(bse.read_count)                                                                                 read_count,
               max(bse.write_count)                                                                                write_count,
               max(bse.write_skip_count)                                                                      write_skip_count
        from batch_job_execution bje
                 left outer join batch_job_execution_params bjep on bje.job_execution_id = bjep.job_execution_id
                 left outer join batch_step_execution bse on bje.job_execution_id = bse.job_execution_id
                 left join batch_job_instance bji on bji.job_instance_id = bje.job_instance_id
        group by bje.job_execution_id, bje.status, bje.exit_code, bje.create_time, bje.start_time, bje.end_time,
                 bji.job_name
        order by bje.create_time desc) jobs
           join users on users.id = jobs.userId
  where status in ('STARTING', 'STARTED')
    and create_time >= now()::date + interval '1m';
  ```
* Approve deployment to production

#### avni-webapp

* Find the passing circle-ci job for the release tag. If the job was triggered more than a week ago, retrigger a fresh build from circleci after selecting the repo and the branch so that the artifacts from the build step are available in the deploy steps.
* Approve deployment to production
* Deploy platform translations for all app flavors.

#### rules-server

* Find the passing circle-ci job for the tag.
* Approve deployment to production

#### avni-client

Binaries for avni-client can be generated on the local machine or via circleci(Preferred for Avni Production). Steps for both are mentioned below.

##### Local Machine Build

Make sure the following environment variables are set (values available in keeweb) for the flavors that are being built:

`<flavor>_KEYSTORE_PASSWORD`

`<flavor>_KEY_ALIAS`

`<flavor>_KEY_PASSWORD`

* For releasing all flavors:

```shell
## Create prod bundles
versionName=3.5.1 versionCode=30501 make release_prod_all_flavors

## Deploy platform translations
make deploy_platform_translations_live_for_all_flavors
```

* To release a particular flavor, say lfe:

```shell Shell
## Create prod bundle
versionName=3.5.1 versionCode=30501 make bundle_release_prod flavor='lfe'

## Deploy platform translations
make deploy_platform_translations_for_flavor_live flavor='lfe'
```

* To release security flavor, follow instructions specified [here](https://avni.readme.io/docs/release-process-for-security-testing)

##### CircleCI Build

* Go to [https://app.circleci.com/pipelines/github/avniproject/avni-client](https://app.circleci.com/pipelines/github/avniproject/avni-client)
* Select the branch that the release is being made from
* Click on the 'Trigger Pipeline' button. Note that Trigger can be done only on HEAD of a branch, if you need to build from one of the previous commits, then create a new branch and use that for build purposes. Merge it back to the parent after build.
* In the popup that is opened, add `flavor`, `versionCode` and `versionName` parameters. `flavor` by default is set to `generic` and can be skipped if generating the generic avni flavor.

<Image border={false} src="https://files.readme.io/ef90846-image.png" />

* Click on 'Trigger Pipeline' in the popup.
* Once the test and build jobs pass, approve the **hold_live** job.
* Trigger platform translation upload for live
  ```shell
  ## Deploy platform translations
  make deploy_platform_translations_for_flavor_live flavor='generic'
  ```
* Once the pipeline completes, AAB and APK files will be available in the artifacts for the release_android_live job. The AAB is meant for uploading to play store. APK can be used for manual testing if required.
  * For some reason the AAB downloaded is in `.zip` format. From terminal, do something like `mv avni.zip avni.aab` before uploading it to google play store.
  * OR, copy the download link for the AAB file and use terminal to download it via wget command (Ex: wget \<url>.aab), upload the same to google play store
* Open the [Google Play console](https://play.google.com/console)
* Open the Avni app and go to Release Menu -> Testing -> Open Testing
* Create a new Beta release and upload AAB generated
* Name the version and provide the release notes. In the release notes, no need to mention cards with label 'Epic' or with category as 'regression'.
* Send changes for review
  * On Open-testing track, to 100% of  users
  * On Prod track, partial rollout to 5% of Prod track users
* After we receive Playstore review approval and QA team gives "Go ahead" on performing Sanity testing of Prod APK, manually publish the changes
* Email to [avni-users@googlegroups.com.](mailto:avni-users@googlegroups.com.)
* Message in the common channel(both team members present) tagging the QA with release notes link, that release is out for sanity testing.
* Wait for 7 days for feedback from users. If none, then increase roll-out to 20% of the production user-base
* Wait for another 7 days(14 days from initial rollout) for feedback from users. If none, then increase roll-out to 100% of the production user-base

#### Deployment of secondary applications

Avni secondary components are:

* etl
* integration-service
* integration-admin-app
* media-service(Server and client)

For each of the Avni secondary components listed above, repeat the following steps

1. Find the passing circle-ci job for the tag corresponding to the release
2. Approve deployment to production

Additionally, if there are any changes in Lambda scripts of Avni-media, deploy them to S3.

```c Shell
#> ./deploy-lambda-functions.sh <environment>; // Needs AWS CLI config to be done
```

### Post-deployment

* Message in the common channel(both team members present) tagging the QA with release notes link, that release is out for sanity testing.
* Publish release for  "avni-product" repo only

```Text Shell
gh project item-list -L 3000 --owner avniproject --format json 2 | jq -c '.items[] | select (.release == "<Specify release number, ex:5.1.0>") | [ .title, .content.url]'
```

* **Optional:** Create a blog on avni-website repository with details of the release. Make sure to include relevant documentation links and videos if necessary. This is meant for a non-technical user while the release notes on Github can be for developers and implementers
* Send brief information on 
  * [avni@samanvayfoundation.org](mailto:avni@samanvayfoundation.org),  [avni-users@googlegroups.com](mailto:avni-users@googlegroups.com) (usually with the subject "Avni Release Announcement - release_number")
  * avni-community discord channel
  * avni-community whatsapp group
* If you are sending this communication for the first check past examples. 
* **Merge the release branch to following branches across all repositories**
  * master/main
  * any upcoming release branches already cut-off (Ex: 7.1 into 8.0)
* Update release version in the [release tracker](https://docs.google.com/spreadsheets/d/1LcyE3_Ht_YztBjfPpedvjUtvzcJezP5dFOZFOyzWs2E/edit?usp=sharing)
