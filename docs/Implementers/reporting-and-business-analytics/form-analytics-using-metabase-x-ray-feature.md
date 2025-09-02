---
title: Form analytics using Metabase X-Ray feature
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
Metabase xRay allows for generating basic analytics on click of a button from the database table. Please follow the steps below for setting up table analytics that can be used. The steps below having been provided at a logical level.

> 📘 Note: The dashboard created using this approach cannot be easily migrated to another database hence the development should be done in production database, else it will involve rework.

### Features relevant to us

- Auto generated breakup by coded fields
- Can see line list for each breakup
- Related tables can be mapped to logical names
- Internal columns can be removed
- Related table’s data can be seen from the line list (e.g. by clicking on Individual name)
- In pie-chart form also see the percentage
- Can be used with custom models feature

### Standard ETL table or Custom Model

It is possible that your requirement involves using joins with other table like location for using in filters. To achieve this use Custom Model feature and use the metabase designer to join. Using native query is not recommended - as that requires configuring the columns to make it understanding to metabase features.

In your custom model you may want to take filter out records like voided = true, or exited, cancelled etc.

### Table configuration changes

Available from Admin ->> Table Metadata tab.

Remove visibility of fields that do not concern the user. Some you can remove from **everywhere** and some only in **detail view** (line list view). Discuss with functional people in your team about the exact system fields to change.

### Generate xRay

1. Find a table from data source
2. Click xRay
3. Choose more details
4. Save the dashboard automatically created. You can also move this to the right place using move option. By default they do in the `Automatically Generated Dashboards`. Note - You cannot add filter before generating dashboards.

### Dashboard changes

- Remove any unnecessary generated filters and cards first. With fewer cards the performance of the dashboard during the design process will be better.
- Any field directly on the table/form can be added as filter.
- Only one address filter can be added per table/dashboard (note that table metadata should be changed to map too).

### Table configuration changes to make certain fields more useful

Metabase allows to map a foreign key field such that one can see a logical text instead of seeing a number. For example - individual_id can be mapped to Individual.first_name; address_id can be mapped to Address.Title.

### Known Limitations

- Cannot do percentage only totals (why? - <https://avni.readme.io/docs/form-analytics-using-metabase-x-ray-feature>) in non-pie chart form.
- Once xRay dashboard is generated subsequent addition of fields will have to be manual, otherwise the previous changes will be lost.