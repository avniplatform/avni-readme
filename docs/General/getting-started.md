---
title: Introduction
excerpt: A quick overview of Avni software for field data collection and reporting.
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
Avni is an open-source platform for on-field service delivery and data collection. Designed for the development sector, Avni strengthens field capacity for non-profits and governments across sectors - like health, water, education, and social welfare. 

This documentation site is geared to introduce Avni to those who would like to implement their program workflows using this system.

**Avni consists of 4 major components which perform various functions as explained below:** 

1. **Avni Field App (Mobile)** : This is an Android app designed to be used in the field for data entry and collection. It can work in offline mode and can sync data whenever it has internet connectivity.  One can install the field app easily from the Google Play Store or from an APK file.

2. **Avni Web App**: This is the **Administration** Interface where you can configure and set up the Avni server,  for example configuring master data for Locations, Hierarchies, Users, designing the Program Forms, etc. It can also be used for Data Imports, Exports, and Data-Entry. It runs in a web browser. 

3. **Avni Reporting and Dashboard**: This connects to the Avni server to pull out custom reports and dashboards that can be viewed in the web browser, or exported as PDFs and emailed. 

4. **Avni Hosted Service**: The central instance of the Avni server running on the cloud, which stores all the master data, configuration, and field-entered data. If you don't want to use the cloud instance, then you can set up your own local instance of Avni in your data center/server. Avni is fully Open-Source. 

See [Avni Hosted Service](doc:avni-hosted-service) for details of the Avni hosted service

**See Diagram Below**

![](https://files.readme.io/feb07ae-Avni_6.png "Avni (6).png")

# Documentations

1. [Implementer's guide](doc:implementers-concept-guide-introduction)
2. [Production Environment Setup guide](doc:environment-setup-guides)
3. [Design Documentation](doc:architecture)
4. [Avni Code of Conduct](doc:avni-code-of-conduct)
