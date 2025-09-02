---
title: Pre-release Testing Checklist
excerpt: >-
  Please follow pre-release checklist strictly to avoid delays/headaches in prod
  release.
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## One time
 * Create a new (permanent) user pool called pre-release
 * Create users in the same pool for each implementation, with the right organization

## Prepare a new replicated environment
 * Replicate production environment and create a temporary environment called pre-release. Following are the steps to do this:
  1. Go to infra repo.
  2. Update minor\_version file at the path "server/version/minor_version" with the latest version that you need to deploy. You can get last successful version from "build" job of openchs-server workflow from CircleCI.
  3. Run `make plan-prerelease-from-prod`.
  4. Create prerelease environment using command `make create-prerelease-from-prod`.
  5. Check prerelease server logs to make sure that server has started. 
  6. Also make sure from the logs that all migrations have run successfully. 
7. **Warning**: If any migration is failing or taking too long make sure to fix the issue and please don't manually make the server skip a migration. Skipped migration will result in headache during the prod release. 

## Deployment
 * Deploy new server build
 * Deploy core metadata.
 * Deploy all the implementations that you are going to deploy on Production. 
 * **Warning**: If you miss out deploying any implementation then you lose the chance to catch errors that may come during prod release.

## Test existing app with existing implementations
 * Build the version of the app corresponding to the one available on the play store on your machine or circle-ci pointing to pre-release.
 * Use this app. Perform sync. Test the app.

## Test new app with existing/new implementations
 * Update the app on the device you are testing
 * Test the app for each implementation (Uninstall, Install, Login, Any other testing)
 * Ensure that you have run the "Run Rules" if required during the testing for the new customer. 

## Finally
* You are good to go for production release if there are no issues.
* Stop/Destroy the pre-release environment
* Right now, the make task to stop/destroy pre-release env, in infra code base is not fully functional. So terminate EC2 and delete prerelease db from RDS. Be careful while doing it.