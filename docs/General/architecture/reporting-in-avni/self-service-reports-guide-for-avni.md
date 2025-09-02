---
title: Self-Service Reports Guide for Avni
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
## Table of Contents

* [Introduction](#introduction)
* [Prerequisites](#prerequisites)
* [Setup Process](#setup-process)
* [User Management](#user-management)
* [Navigation](#navigation)
* [Reporting Features](#reporting-features)
* [Troubleshooting](#troubleshooting)
* [Refresh Process](#refresh-process)
* [Teardown Process](#teardown-process)
* [Appendix](#appendix)

## Introduction

### What is Metabase?

Metabase is a powerful open-source analytics and visualization tool that Avni integrates to provide comprehensive reporting capabilities. It allows you to create custom dashboards, run ad-hoc queries, and share insights across your organization with simple drag-and-drop operations.

### Self-Service Reports in Avni

Self-service Reports is a feature in Avni that allows users to create and manage reports without requiring technical expertise. It provides a user-friendly interface for creating and managing reports, and allows users to schedule and distribute reports via email.\
In Avni, we make use of Metabase to power Self-Service Reports.

> **Note:** This guide provides comprehensive documentation for setup, user management, and administration of Self-Service Reports. For hands-on training with practical exercises for using Metabase on your Avni Data, please refer to the [Getting started with Avni Metabase reports](getting-started-with-avni-reports) guide.

### Benefits of Metabase

* **User-friendly interface**: Create visualizations with simple drag-and-drop operations
* **Customizable dashboards**: Build tailored views for different stakeholders
* **Automated reporting**: Schedule and distribute reports via email
* **Data exploration**: Empower users to find insights without technical expertise
* **Secure access control**: Manage permissions at granular levels

### Inbuilt Capabilities of Self-Service Reports

Self-service Reports in Avni provides the following capabilities:

1. Creates a dedicated database user with appropriate permissions
2. Establishes a connection between Metabase and your Avni database
3. Sets up user groups and permission structures
4. Create standard Questions
5. Create collection with default dashboard

## Prerequisites

* ETL has to be enabled for your organisation, contact Avni-support team for any help regarding this.
* You need to be logged-in as a user, who belongs to a UserGroup with Analytics Privilege for your organization in Avni

<Image align="center" className="border" width="420px" border={true} src="https://files.readme.io/08e4962e2a1df9c5d3b5967ca92e0c5ac18acf0ee573971b547a4562f78e1c51-Screenshot_2025-05-20_at_7.25.43_PM.png" />

## Setup Process

### 1. Enabling Self-Service Reports

Self-Service Reports is managed at the organization level in Avni:

1. Log in to your Avni webapp
2. Navigate to Reports section
3. Click on "Self-service Reports" tab
4. Click on "Setup Reports" button

![Initial Setup State](https://files.readme.io/cdaa0376b8d1b7fbe8a1d776681b23a8f4643cbbc44c290888e5fcf356b23dd4-metabase_initial_state.png)\
*Figure 2: Initial state before Self-Service Reports setup with "Setup Reports" button*

### 2. Setup Process Stages

The setup process goes through several stages:

#### Initial Setup

When you first enable Self-Service Reports, you'll see the "Setup Reports" button. Clicking this button initiates the setup process.

#### Setup in Progress

During the setup process, you'll see a loading indicator:

![Setup in Progress](https://files.readme.io/34792bee15afc7e3ddf4a88a31e62ba4c23a8131971377c52df7a81c624c599d-metabase_loading_state.png)\
*Figure 3: Setup in progress with loading spinner*

The setup process typically takes 15-30 minutes to complete and involves:

* Database connection setup
* Initial schema synchronization
* Permission configuration
* Default Collection and Dashboard creation
* Default questions creation

#### Partial Setup

Sometimes, the setup may complete partially with only some resources available:

![Partial Setup](https://files.readme.io/61242bf6bcce5582259ac4ba46363b9cd90a102be6511728d18b6623e4417ada-metabase_partial_setup.png)\
*Figure 4.a: Partial setup with only Database resource available*

In this state, you can either:

* Wait for the remaining resources to be created automatically
* Click "Setup Reports" again to retry the setup process

### 3. Verifying Setup Completion

You can verify the setup was successful by:

1. Confirming the "Explore Your Data" button is available
2. Testing access with a user that has been added to the "Metabase Users" group in Avni

<Image align="center" src="https://files.readme.io/f0c5a313f302629bfa838cfcbbc368aac06d42a079c016331de3295e7df915a0-metabase_refresh_reports.png" />

*Figure 4.b : Successfully completed setup\
(Note: Delete button only available in development environments)*

## User Management

### User Group Synchronization

Avni automatically synchronizes users between the Avni platform and Metabase. This synchronization ensures that users added to the "Metabase Users" group in Avni can access analytics in Metabase.

#### Adding Users in Avni

To grant users access to Metabase analytics:

1. Navigate to User Groups Management in Avni Admin App
2. Select the "Metabase Users" group
3. Add the user(s) you want to grant access to the group
4. Save changes
5. User added to "Metabase Users" group, will receive an email with an account activation link

Note: Removing users from the "Metabase Users" group will remove their access to Metabase analytics.

![Avni User Groups](https://files.readme.io/5c483f0cd5480029f298a23961dfb5248f633d13195022546bc8af4248cddac7-metabase_user_groups.png)*Figure 5: Avni user groups management interface showing Metabase Users group*

#### Verification in Metabase

After adding users to the Metabase Users group in Avni, you can verify their synchronization in the Metabase admin interface:

![Metabase Admin People](https://files.readme.io/cf18e6606710fc311495adec917571095ba46e793afadd92e731a17f192fdaf1-metabase_admin_people.png)*Figure 6: Metabase Admin interface showing synchronized users*

The synchronization process:

1. Creates user accounts in Metabase with the same email addresses as in Avni
2. Includes the User in Metabase Group corresponding to their organization

## Navigation

You can navigate to Self-Service Reports from the Avni Sign-in screen, by clicking on the "METABASE REPORTS" button.

![Self-Service Reports Navigation](https://files.readme.io/5ca645c93da6b8a024963b55c58b245cb12f9c628d5a5e43eafbdf542a520699-metabase_navigate_from_avni.png)\
*Figure 7: Self-Service Reports Navigation via "Self-Service Reports" button available in Avni Sign-in screen*

## Reporting Features

### Canned Reports Dashboard

The Metabase integration includes a pre-configured "Canned Reports" dashboard that provides immediate value without requiring users to build reports from scratch.

![Canned Reports Dashboard](https://files.readme.io/7d99a2dae462bb202090ac6e4e6d73e21c47ce5a00c94c4eae4d2b09eb2ed74a-metabase_canned_reports.png)\
*Figure 8: Overview dashboard with multiple report visualizations*

Key features of the Canned Reports dashboard:

* Filter controls at the top (Date Range, Location filters, etc.)
* Multiple visualizations organized by subject area
* Interactive charts that respond to filter selections
* Donut charts showing distribution of key metrics
* Empty states for sections with no data ("No results!")
* Drill-down ability by clicking on section of Donut (or of any part of different type of vizualizations)

<Image align="center" src="https://files.readme.io/0ce0bacad8d607b479963694e94bcb13656b0f1068583aa0eaa2ed8cbe3b769b-Screenshot_2025-05-22_at_11.15.58_AM.png" />

*Figure 9: Drill-down ability, by clicking on Donut chart section or on other visualizations*

### Collection Structure

Metabase organizes reports and dashboards into collections. The default collection contains various pre-built reports:

![Collection Structure](https://files.readme.io/c62b3bd102960c9bbe38b26d36cd02980dd901cec84c54bba40c96dee98e4adf-metabase_collection.png)\
*Figure 10: Default collection structure showing dashboard and reports*

The collection includes:

* Canned Reports dashboard
* Individual report views (Completed Visits, Due Visits, etc.)
* Other Fundamental Database tables and views that power the reports

### Report Visualizations

Individual reports provide detailed visualizations of specific metrics:

![Completed / Due Visits Report](https://files.readme.io/9af7f3494e2d9173f4d6b4e74acfc9fadca9e606f95684af1f788575c5beaf2b-metabase_completed_visits.png)\
*Figure 11: Detailed visualization of Completed / Due Visits by type*

Visualization features include:

* Interactive donut charts with percentage breakdowns
* Clear labeling of data categories
* Total count displayed in the center
* Color-coded segments for easy differentiation

### Database Tables and Views

Metabase connects to your Avni database and creates optimized views for reporting:

![Database Tables and Views](https://files.readme.io/da4dc45abbb0f349603f03f05b2db26ed74f925793c729aa7199d464559ee17f-metabase_database_tables.png)\
*Figure 12: Database tables and views available in Self-Service Reports*

The database structure includes:

* Base tables (individual, household, address, etc.)
* Derived views (completed\_visits\_view, due\_visits\_view, etc.)
* Relationship tables (household\_individual, etc.)

These tables and views are automatically kept in sync with your Avni database.

### Data Exploration

Metabase allows users to explore raw data through table views:

![Individual Data Table](https://files.readme.io/cfbf4ea42ab9b037e39fc0fa1e23c7f4773c9c24bf0abd7ab598ac193815b637-metabase_individual_table.png)\
*Figure 13: Individual data table showing subject records*

![Child Data Table](https://files.readme.io/37921c0981d9e7914beaa68c5237b17baae544387131caef7ab1a4090476b09b-metabase_child_table.png)\
*Figure 14: Child data table showing specific program records*

Data exploration features include:

* Sortable columns
* Record counts and pagination
* Search functionality
* Filtering options
* Direct access to raw data

## Troubleshooting

### Error Reporting

When errors occur during the Self-Service Reports setup or synchronization process, they are displayed directly in the interface:

![Error Reporting Example](https://files.readme.io/e1e4f12702c93d949a5c05e685d120a159e2a42fee31ca6555dd4464312d883a-metabase_error_example.png)

*Figure 15: Example of an error message during Self-Service Reports setup\
(Note: Delete button only available in development environments)*

The error message includes:

* A clear indication that the attempt failed
* The specific Server error that occurred
* Details about what caused the error (in this example, a missing database table)
* A "Copy error to clipboard" button for easy sharing with support

Common errors include:

* Database connection issues
* Missing tables or schemas due to ETL failure
* ETL not enabled

### Common Issues and Solutions

| Issue                                   | Possible Cause                                                   | Solution                                             |
| --------------------------------------- | ---------------------------------------------------------------- | ---------------------------------------------------- |
| Dashboard shows no data                 | Database sync incomplete, ETL process not completed successfully | Wait for sync / ETL process to complete successfully |
| User cannot access Self-Service Reports | Not in "Metabase Users" group                                    | Add user to the group in Avni                        |
| Missing tables or fields                | ETL not enabled or not completed successfully                    | Contact Support                                      |

## Refresh Process

### When to Use Refresh

The refresh process is to be used, whenever there are new Entity Types created for the organization.

### Performing a Refresh

To refresh Self-Service Reports integration:

1. In Avni admin panel, navigate to Reports section
2. Click "Self-Service Reports" tab
3. Click "Refresh Reports" button

<Image align="center" src="https://files.readme.io/5b4264a93cd8614a6e0eb42c41a84420cb99372c773554667e95ad48d1bcb214-metabase_refresh_reports.png" />

*Figure 16: Refresh Self-Service Reports setup by clicking on "REFRESH REPORTS"\
(Note: Delete button only available in development environments)*

Wait for the process to complete.

The refresh process will:

* Create missing dashboards, cards and questions

## Appendix

### Glossary of Terms

* **Collection**: A group of questions and dashboards in Metabase
* **Dashboard**: A customizable display of multiple visualizations
* **Question**: A saved query that produces a visualization or data table
* **Sync**: The process of updating Metabase's understanding of your organization's database structure
* **Card**: An individual visualization on a dashboard
