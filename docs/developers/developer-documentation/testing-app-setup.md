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

## 1. Deploy the server to staging environment

Go to CircleCI, workflows, openchs-server, open. Choose the build (master latest should be fine). Unpause to deploy the build.

You can download the appropriate version of the app directly from [circleci](https://circleci.com/gh/OpenCHS). Since the generation of app installer (the apk) file is done through a manual trigger, you may have to go to the workflow choose a particular build and unpause it. Download the apk file to your file system or to your Android device.

## 2. Setup for health module and implementation deployment - one time

In your bash\_profile or bash\_rc define the environment variable for:\
STAGING\_ADMIN\_USER\_PASSWORD

Install the following pre-requisites\
git, make, node

## 3. Deploy core health modules

You need to have the code for openchs-client checked out.\
If you do not have the code then use:\
git clone [git@github.com](mailto:git@github.com):OpenCHS/openchs-client.git\
If you already have the code just use git pull

To deploy the health modules run the following command from the openchs-client code root folder:\
make deps deploy\_metadata\_staging

## 4. Deploy the implementation you want to test

**Pre-requisites: git, make, node**\
Ensure that the implementation organization has been created in the database. This requires access to the database.

You need to have the code for implementation checked out.\
If you do not have the code then use:\
git clone [git@github.com](mailto:git@github.com):OpenCHS/\<\<?>>.git\
If you already have the code just use git pull

To deploy the implementation run the following command from the implementation code root folder:\
make deps deploy\_staging

## 5. Install the app

Get the apk from someone in your team or download it from CircleCI.\
If you are installing the app on your device, you may have to change the setting of Android to allow install from unknown sources. If you are installing in GenyMotion, just drag and drop the apk file on the running emulator device.

## 6. Sync and choose your user

Run sync to get the reference data, metadata and any transaction data setup for this environment. Before the sync starts you would be asked for login details. Depending on which user you log in as you would be testing that implementation.\
If you are switching from one environment to other then you will have to reinstall the app before changing the setting.
