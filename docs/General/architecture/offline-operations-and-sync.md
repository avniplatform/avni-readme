---
title: Offline operations and sync
excerpt: >-
  How Avni Android app works in offline-mode while exchanging data with Avni
  server
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Offline-first Avni Android App

In most places where field work happens, there is usually no or quite poor internet connectivity. Therefore Avni has been designed group up to support robust offline capabilities, so that field work can easily happen offline, and data & photos, etc. can be synced whenever there is decent internet connectivity. Avni is already being used in production in this mode.

This page provides some of the details on Avni's offline-mode support. 

## Sync mechanism

Data in Avni is broken down into transactional data, reference data and media. We first discuss sync of transactional and reference data. This is called a data server sync. Then we talk about media, and then telemetry around sync. 

### Data server sync

The sync mechanism involves both push of transactional data from the android app to the server, and pull of both reference and transactional data from the server. 

First, transactional data in the android app is pushed to the server. Then the app pulls reference data from the server. Finally, transactional data is pulled from the server. 

The EntityQueue table is used to track data that has been created or updated in the app, These are then ordered based on their relationships and then pushed to the server. The app also maintains the last updated entity of each entity type in the EntitySyncStatus table. This is used to pull all changes for each entity type after the last sync. 

### Media sync

Media in Avni is usually some image/video that is associated to an observation. Media upload happens directly to S3 from the android app. Once upload is complete, the observation is modified to store the S3 url. Since this means a change in the observation a data server sync is performed once again. 

### Telemetry (for debugging issues)

Telemetry around sync is also synced as an entity to the server so that they can be used for monitoring of sync issues of users.
