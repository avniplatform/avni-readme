---
title: Upgrading Metabase
excerpt: >-
  Metabase is one of the reporting tools supported by Avni. This page explains
  how to upgrade Metabase.
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
##

In case of major changes, you should try upgrading the Metabase locally, before directly doing it on production. Once everything works on your local, proceed to deploy it on production. Before doing any deployment on production always remember to take a DB snapshot of the reporting DB. One can follow below steps for doing the Metabase upgrade.

* Take the reporting DB dump from the reporting server and copy it on your local.
* Deploy the DB dump on your local.
* Now deploy the Metabase version which is deployed in production.
* Stop the Metabase and run the version that you want to upgrade to.
* Now test out the Metabase locally to check everything is working fine.
* Once everything is working locally, create a snapshot of reporting DB before the upgrade.
* ssh into the Metabase server. Remove the old jar, and copy the new Metabase jar that you want to deploy.
* run ./reporting.sh, this will stop the old server and start the new server using the jar that you just copied.
* Login to Metabase and confirm the version and test that reports are working fine.
