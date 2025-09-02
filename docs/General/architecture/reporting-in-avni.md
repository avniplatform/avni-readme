---
title: Reporting in Avni
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: self-service-reports-guide-for-avni
      title: Self-Service Reports Guide for Avni
---
Avni supports Data analytics in 2 different modes:

1. Extract data using Longitudinal exports (Old and new) and analyze it using applications such as SAS/SPSS.
2. Use Avni Platform Hosted Reporting Service instances of JasperReports, Superset or Metabase to analyze data in near real time.

## Longitudinal exports

Avni has a section to retrieve longitudinal exports of data that is analysis friendly with applications such as SAS/SPSS. This is also available in the browser if you login to the Avni web application.

<Image alt="Old Longitudinal exports" align="center" src="https://files.readme.io/327a38b7ee76e06144331079639ab99b7e70f3fb9723509cc2af4f2fec1d8cbf-old_longitudinal_export.png">
  Old Longitudinal exports
</Image>

<Image alt="New Longitudinal exports" align="center" src="https://files.readme.io/552ab3fac1f97a83dc533b3bb7c70d8dc406702c515f089bf93ef72e433256bf-new_longitudinal_export.png">
  New Longitudinal exports
</Image>

## Avni hosted Reporting services

As part of [Avni Hosted Service](doc:avni-hosted-service) we provide three different reporting systems using open source tools Metabase, Superset and Jasper Reports. There are links to access these in the Avni Web-application [login page](https://app.avniproject.org). 

Alternatively, you can directly go to the various reporting tools via following links:

* Metabase - [https://reporting.avniproject.org/](https://reporting.avniproject.org/)
* Jasper Reports - [https://reporting-jasper.avniproject.org/jasperserver/login.html](https://reporting-jasper.avniproject.org/jasperserver/login.html)
* Superset - [https://reporting-superset.avniproject.org/](https://reporting-superset.avniproject.org/)

### Self-Service Reports

Avni provides a Self-Service Reports feature that allows users to create and manage reports without requiring technical expertise. We uses Metabase for this purpose. You can read up more about it [here](self-service-reports-guide-for-avni).

<Image alt="Self-Service Reports" align="center" src="https://files.readme.io/0ffb57d71344858fd1f3fb5018f7af9425cc23fa9fb2ac517b741ea0322c7727-self_service_reports.png">
  Self-Service Reports
</Image>
