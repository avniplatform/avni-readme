---
title: Templates
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

## Overview
The template-based setup allows implementers to quickly bootstrap their Avni instance with pre-configured settings, forms, and metadata that closely match common use cases. This significantly reduces the time and effort required for initial configuration.

## Details
Applying a template deletes all data and configuration in the organisation to avoid conflicts with the template being applied. Due to this, templates are not allowed to be applied to any UAT or Production orgs so there is no accidental deletion of data.

If any configurations that might be useful have been setup, create a backup using the Bundle download (**App Designer** -> **Bundle**) feature.

You can change the configuration that the template provides after the setup is complete to suit your organisation's needs.

The operation takes a few minutes to complete.

## Privileges required
The following privileges are required to use this feature:
- UploadMetadataAndData
- EditOrganisationConfiguration
- DeleteOrganisationConfiguration

## How to Apply a Template

1. Navigate to **Create App** -> **Templates**
2. Select your desired template from the available options
3. Click **Apply Template**

