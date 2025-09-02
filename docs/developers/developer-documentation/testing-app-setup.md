---
title: Testing app setup
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
If you want to install a build which is not in production follow the steps below.
[block:api-header]
{
  "title": "1. Deploy the server to staging environment"
}
[/block]
Go to CircleCI, workflows, openchs-server, open. Choose the build (master latest should be fine). Unpause to deploy the build.

You can download the appropriate version of the app directly from [circleci](https://circleci.com/gh/OpenCHS). Since the generation of app installer (the apk) file is done through a manual trigger, you may have to go to the workflow choose a particular build and unpause it. Download the apk file to your file system or to your Android device.
[block:api-header]
{
  "title": "2. Setup for health module and implementation deployment - one time"
}
[/block]
In your bash_profile or bash_rc define the environment variable for:
STAGING_ADMIN_USER_PASSWORD

Install the following pre-requisites
git, make, node
[block:api-header]
{
  "title": "3. Deploy core health modules"
}
[/block]
You need to have the code for openchs-client checked out.
If you do not have the code then use:
git clone git@github.com:OpenCHS/openchs-client.git
If you already have the code just use git pull

To deploy the health modules run the following command from the openchs-client code root folder:
make deps deploy_metadata_staging
[block:api-header]
{
  "title": "4. Deploy the implementation you want to test"
}
[/block]
**Pre-requisites: git, make, node**
Ensure that the implementation organization has been created in the database. This requires access to the database.

You need to have the code for implementation checked out.
If you do not have the code then use:
git clone git@github.com:OpenCHS/<<?>>.git
If you already have the code just use git pull

To deploy the implementation run the following command from the implementation code root folder:
make deps deploy_staging
[block:api-header]
{
  "type": "basic",
  "title": "5. Install the app"
}
[/block]
Get the apk from someone in your team or download it from CircleCI.
If you are installing the app on your device, you may have to change the setting of Android to allow install from unknown sources. If you are installing in GenyMotion, just drag and drop the apk file on the running emulator device.
[block:api-header]
{
  "title": "6. Sync and choose your user"
}
[/block]
Run sync to get the reference data, metadata and any transaction data setup for this environment. Before the sync starts you would be asked for login details. Depending on which user you log in as you would be testing that implementation.
If you are switching from one environment to other then you will have to reinstall the app before changing the setting.