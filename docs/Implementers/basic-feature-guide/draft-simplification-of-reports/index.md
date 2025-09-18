---
title: '[Draft] Simplification of reports development and testing'
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
> 🚧 This document is still in draft

Since we develop a number of reports for each organisation. As the number of organisations grow maintenance and first time & ongoing testing of these reports can get difficult. This document covers some the ideas that can be employed to simply these two activities.

For this document, will use this example to discuss the points below. Lets take a report which shows:

* The number of children who are under nourished
* These numbers are shown by gender (Individual), age-group (Individual), and land-holding (Observation). Land holding could be `a) less than acre` `b) 1-2 acres` `c) 2-5 acres` `d) 5+ acres` In Avni this will be a coded concept with above four answers.
* The report allows the user to choose from address/location filter and view the numbers
* On clicking any number the user is take to the line list of children

## Report work management

* Reports work ideally should start when there is some good data available to write reports against and verify. This will help avoid manual setup of data - which is time consuming.
* During the period in which new implementation application is in use and there are no reports
  * The data available from data entry app or using master catchment and viewing all data from mobile
  * we will have canned reports in the platform which can support customers during this phase
  * as a last resort - temporarily we can support it via hand written operational reports **if absolutely necessary**
* When there are change requests to the application then there may be impact on reports. During analysis the reports that will be impacted should be identified and only those need to be regressed/tested.

## Report design

* Avoid aggregating on multiple **display dimensions** in a single report. This makes the report queries very large and difficult to understand/maintain. What is a dimension - gender, age-group, land-holding - these are each different dimensions and one may want the counts/averages etc of data across these dimensions.
  * All filters used in reports are typically also dimensions (Location from the example above)
  * **Display dimensions** are the type of dimensions for which the data is displayed to the user
* Put all the necessary fields in the line list on which the aggregates are calculated, so that some of the verification of aggregate numbers. If the under nutrition is decided based on weight, height, age, gender, upper-arm circumference then all these data points should also be available in the line list.
* For each implementation we should prepare a set of dimensions that will be used in reports and give it a name. In stories for line list only the name of set can be given. This will also help in code review to ensure all the dimensions have been included in the line list. For example:
  * Dimension Set 1 - Name = Child - for Child program related line list one has - Name, Age, Gender, Mother's name
  * Dimension Set 2 - Name = Woman - for Woman program related line list one has - Name, Age, Blood Group
  * Dimension Set 3 - Name = Pregnant Woman - Name, Age, Blood Group, LMP, EDD
* It may be useful to identify modules in the application. For example - mother and pregnancy program can one module and child program can be module two in some implementations. These modules may be loosely coupled with each other and hence changing one is **likely to not have any impact** on other modules. Identifying these modules can help with testing of change requests, support defects etc. Instead of testing everything one may restrict the testing to specific modules where change has been made.
  * The modules in a new implementation may or may not be obvious. As we gain more insight during development or change request - we can take advantage of this.

## Developer Checklist

**[Why checklists (a very short read)](https://blog.falcony.io/en/the-checklist-manifesto-book-summary#:~:text=In%20the%20book%2C%20The%20Checklist,applied%20to%20almost%20every%20field.)**

This is to ensure that the same type mistakes do not occur due to over sight. It is important to follow these checklist - since the cost of mistake is high on effort from developer and QA both.

### Code

* Ensure voided, exited, and cancelled are appropriately used for each table that is joined in the report. Ensure this is done for parents also (e.g. checking voided on encounter may not be sufficient as one may also want to check whether the parent i.e. individual or program\_enrolment voiding has been checked).
  * Include these fields (whichever is relevant for the report) in the line list in the end so that it is easy to test.
* Have you used inner join and left joins correctly
* If approvals are implemented then add the appropriate checks
* While adding the date filters make sure its on correct date (encounter date, earliest date, registration date, enrolment date).
* Is null or empty string important in any of the where/join clauses?

### BI Tools

* **SuperSet specific**
  * If we are implementing  the row level security then ensure that “$P!\{LoggedInUserAttribute\_LocationFilter}” Is added in query for jasper only
* **Metabase specific**
  * Use summarize feature to create the aggregate report so it will give drill down.
* **Jasper specific**
  * Its better to use the Variable in the jasper report to pass the param for drill down report, avoid use of direct passing where clause. refer this report.
  * [https://docs.google.com/document/d/1RXcoZwG0BoJtkR7IGd9weWJWytRAfmdPqJUgSuDwOrw/edit](https://docs.google.com/document/d/1RXcoZwG0BoJtkR7IGd9weWJWytRAfmdPqJUgSuDwOrw/edit)

### Documentation

* Story, doc links in the SQL

### Subjective review

* Code review should look for False-Negatives (see diagram below).

## Reports Testing

* Feedback into the code review process, if the checklist items are getting missed on development complete. Testing team can verify the checklist items **once in a while** to ensure that the checklist and code review process is working well.
* There are certain practices that should be avoided **as much as possible**.
  * Testing reports by changing data as this is slow and time consuming.
  * If above is avoided then it also eliminates the need to keep pointing the reports do database in different environment.
* There are three broad type of tests that we need to carry out with reports - as in the following diagram.

*source:[https://docs.google.com/presentation/d/1skfVtHmyAg2QEyTowcN2eZwIIoDo03s1K7WAWBE4BDc/edit#slide=id.p](https://docs.google.com/presentation/d/1skfVtHmyAg2QEyTowcN2eZwIIoDo03s1K7WAWBE4BDc/edit#slide=id.p)*

![](https://files.readme.io/5c5156c-image.png)

> **DEA** = Data Entry App

#### Note 1

The idea of verification using line list is that the logic behind the aggregate numbers can be verified by just looking at the line list. It is preferred because it is the fastest. Both False-Positive and True-Positive can be verified by just looking at the line list (again no reason to fiddle with data).

In case line list is not present then one can use DEA and lastly Mobile App. DEA should be preferred over mobile app because of speed of verification (no sync, back buttons, hyperlinks, etc).

> If any particular bug in DEA is hampering the testing then it could be raised to platform team as blocker, to be fixed.

#### Note 2

False-Negatives can be difficult to verify hence:

* Some of this should get covered by code review (mentioned in checklist). In fact if the developer and code reviewer both check this - it should cover most of such cases.
* In testing one can try any hypothesis on False-Negatives by using different filter values by checking for existing such data.
* Lastly, customers and usage of reports is the best way to identify them - as they will use the data most and will have *much better feel/intuition* of the numbers.

# To be explored (not recommended yet)

* Use of views to allow for re-usable code across reports

### Examples

[https://reporting.avniproject.org/question/2995-prevalence-of-anemia-linelist](https://reporting.avniproject.org/question/2995-prevalence-of-anemia-linelist)

* Instead of school, boarding, and boarding as different drop down it is better to have Location Type and Location as drop downs. They become `and` query with two items instead of 6 items.
* Use of full join instead of union, with `COALESCE`. 
* For haemoglobin entries create `json_each` and `join`. Same for age-group.
