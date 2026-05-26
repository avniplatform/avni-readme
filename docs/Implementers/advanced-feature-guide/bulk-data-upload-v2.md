---
title: Bulk Data Upload v2
excerpt: applicable release 13.0 onwards
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Purpose

* Prepare data in bulk, review, and upload.
* Migrating away from an existing implementation, and need to seed with existing data.
* Your organization has a separate component where data is collected outside Avni, but you still need this data to be present with field workers using Avni.

## Using the Admin app to upload data

The Admin app of the web console has an upload option. Currently, this supports the following. Essentially for each form present in you organisation there is a corresponding upload option in the dropdown, with a sample file.

* Upload subjects
* Upload program enrolments — active, or already exited
* Upload program encounters — scheduled, completed, or cancelled
* Upload encounters — scheduled, completed, or cancelled
* [Upload locations](location-and-catchment-in-avni)
* Upload users and catchments
* Upload metadata zip file downloaded from a different implementation

## Sample file

Sample files are available in the interface. Download the file, fill in values and then upload. The file is in a [CSV](https://www.howtogeek.com/348960/what-is-a-csv-file-and-how-do-i-open-it/) format.  
Sample file acts as an up-to-date documentation on the following.

* fields
* whether they are mandatory for upload
* possible values
* format of the value

> 📘 Since above has already been documented and maintain in the sample file these are not documented here again, please refer to it as a reference documentation.

## Mandatory fields in the form

The mandatory fields in the form element are not applicable when uploading the data via CSV files - since we have seen when made mandatory especially for the legacy data, the users are force to upload some junk information (this may be added in the future).

## Rules

No rules are run as part of CSV upload. This implies that:

* field values created automatically via form element rules will not get created (such columns are present in the sample hence can be uploaded manually)
* observations created by decision rules will not be created automatically (such columns are present in the sample hence can be uploaded manually)
* Validation rule is not applied
* Edit rule is not applied

> 📘 Avni currently doesn't have a robust framework to run these rules on the server side. This may be added in future, if we observe that users need these.

## Identifiers

The primary purpose of these identifiers is for the users to be able to link different CSV file types upload data to each  other - in the same way as foreign key linkages between different records. These linkages can be created using identifiers of user's choosing. Lets try to understand this via an example. Lets assume there are three forms.

* Woman Registration (Subject)
* Pregnancy Program Enrolment (Program Enrolment)
  * links to woman
* Ante Natal Visit Form (Program Encounter)
  * links to pregnancy program

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        Form
      </th>

      <th>
        Columns
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        Woman Registration
      </td>

      <td>
        Id from previous system
      </td>

      <td>
        Any unique identifier that you may want to use. Note that you can make this up if you don't already have one. e.g. WOMAN-100001, WOMAN-100002
      </td>
    </tr>

    <tr>
      <td>
        Pregnancy Program Enrolment
      </td>

      <td>
        Id from previous system
      </td>

      <td>
        Any unique identifier that you may want to use. It should unique for all program enrolments. They can be same as woman registration id, but we recommend you use something like e.g. WOMAN-100001-01, WOMAN-100001-02 so that you can use multiple enrolments for the same woman.

        It is possible that at the time of preparing this data, you are don't plan to upload woman registration via CSV and it is already present in Avni. In such a case you should use the Avni UUID value of the woman registration in this field.
      </td>
    </tr>

    <tr>
      <td>
        Pregnancy Program Enrolment
      </td>

      <td>
        Subject Id from previous system
      </td>

      <td>
        This should be used to match the pregnancy enrolment record woman registration record. Hence, for our example used so far, this field would have values like - WOMAN-100001, WOMAN-100002
      </td>
    </tr>

    <tr>
      <td>
        Ante Natal Visit Form
      </td>

      <td>
        Id from previous system
      </td>

      <td>
        You can leave this blank, if you intention is to create new records only and not edit them.
      </td>
    </tr>

    <tr>
      <td>
        Ante Natal Visit Form
      </td>

      <td>
        Program Enrolment Id
      </td>

      <td>
        This should be used to match the program ante natal visit form record with woman registration record. Hence, for our example used so far, this field would have values like - WOMAN-100001-01, WOMAN-100002-01

        It is possible that at the time of preparing this data, you are don't plan to upload pregnancy enrolment data via CSV and it is already present in Avni. In such a case you should use the Avni UUID value of the woman registration in this field.
      </td>
    </tr>
  </tbody>
</Table>

> 📘 The identifiers used above, for Id from previous system, are saved in Avni but is not visible in Avni after uploading, it is used only for matching records during CSV upload.

## Encounter and enrolment upload modes

When the upload type is an encounter, program encounter, or program enrolment, a _mode_ radio group appears below the upload-type dropdown. Each mode has its own sample file, since the required columns differ.

### Encounter modes

Available for both general encounters and program encounters:

* **Schedule visits** — creates a scheduled (not-yet-completed) encounter. Sample includes `Earliest Visit Date` and `Max Visit Date`; no visit observations.
* **Upload visits** — creates a completed encounter. Sample includes `Visit Date` and the encounter form's observations. _(Previously labelled "Upload visit details".)_
* **Upload cancelled visits** — creates an encounter already in the cancelled state. Sample includes `Earliest Visit Date`, `Max Visit Date`, `Cancel Date`, optional `Cancel Location`, and the encounter's cancellation form observations. There is no `Visit Date` column. Use this when migrating cancelled encounters from another system (typically during an organisation merge).

Scheduling a visit and then later uploading the visit details for the same visit is not supported — that would be an edit, which CSV upload does not handle.

<Image align="center" border={true} src="https://files.readme.io/dec1912d350d0762948915daeb49bef3d9bb3a4a753264e2640305d540779057-image.png" className="border" />

<br />

### Program enrolment modes

* **Upload enrolments** _(default)_ — creates an active program enrolment. Same shape as before.
* **Upload exited enrolments** — creates a program enrolment that is already in the exited state, from a single row carrying both regular and exit observations. Sample includes `Enrolment Date`, optional `Enrolment Coordinates`, the enrolment form's observations, `Exit Date`, optional `Exit Coordinates`, and the program-exit form's observations. Use this when migrating exited enrolments from another system (typically during an organisation merge).

#### `Exit:` prefix convention

In the _Upload exited enrolments_ sample, every column drawn from the program-exit form is prefixed with the literal string `Exit: ` to disambiguate it from enrolment-form columns (the same concept may appear on both forms).

* Valid: `Exit: Weight at exit`, `Exit: Vitals at exit|Weight`, `Exit: Medications stopped|Drug name|1`.
* The prefix is recognised **only at the start of the column header**. A header containing `Exit:` anywhere else (e.g. `Vitals|Exit: Weight`) is rejected.

On import, `Exit:`-prefixed columns are stored as exit observations; remaining concept columns are stored as regular observations.

<Image align="center" border={true} src="https://files.readme.io/9d0721b153ded7c35fe91b779306adc20d4c7e21e14313dba0fbc1a31856a554-image.png" className="border" />

<br />

## Important Notes / Gotchas

* **Limited Concept Support in CSV Upload**: Not all concepts are supported when uploading data via CSV. Specifically, the following are not supported:
  * GroupAffiliation
  * Id
  * File
* **Id Confusion**: The identifiers (used in Id from previous system) are different from Id elements in the form, if you have them.
* **Form Data Editing**: Editing previously submitted form data is not currently supported through the CSV upload process.
* **No Metadata Rollback**: For metadata zip uploads, uploading an older zip file version does not restore a previous version of the metadata. The result of uploading an older version will be an attempted merge of the old version over the current version which might not be what is expected. Avni does not support rolling back of the metadata configuration to a previous state. One can only move forward from the current configuration.
* **Multi-select Coded Concepts**: For multi-select answers, expected input format would be:
  1. Answer 1, Answer 2, ...
  2. Answer 1, "Answer2, has, commas", Answer 3, ...
  3. "Single answer, with commas" or "Single answer, with commas"
  4. CSV-style escaped quotes: """Answer, with, commas""" ... etc.

# Questions

### What if I have a comma in my observation value?

* Wrap your value in quotes.

### Why are decision concepts not appearing the sample file

If you are using decision concepts in the rule but not linked those concepts then this will happen.

### Is the order of values important?

* No. Columns can be in any order.

### How do I upload images?

* For images, use a url that the avni server can download. Ensure that
  * The images are a direct download link (not a redirect to a page that uses javascript to download). Starting from v16.13, Avni will follow 5 redirects and attempt to download the file to be able to support google drive share links.
  * The image urls end with the image type. eg: [https://somedomain.com/images/abc.png](https://somedomain.com/images/abc.png)
