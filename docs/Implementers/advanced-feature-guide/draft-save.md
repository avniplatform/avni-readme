---
title: Draft save
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
Sometimes we have huge forms and all the information is not available when you start capturing the data of such forms. Avni gives you the facility to save the half-filled form as a draft. These draft forms are not synced to the server, and once you fill the form completely draft is automatically deleted.

## Enabling Draft save

You can enable draft to save for your organization using the admin app. Simply go to "organisation Details" and enable "Draft save".

![](https://files.readme.io/d824dc2-draft_save.png "draft save.png")

Once the "draft save" feature is enabled you can see the half-filled forms in the field app. Please note that these drafts will get deleted if the draft is left untouched for more than 30 days.

If a draft is opened up and the form is saved, it becomes an item that will be synced to the server.

![](https://files.readme.io/8386271-d.png "d.png")

<br />

# How Drafts Work Differently by Form Type

1. ## Registration

   * Draft is saved on clicking "Next".
   * Draft is NOT saved on clicking "Previous" or "Back".
   * Registration drafts are displayed as cards on the Register screen with edit and do actions.
   * On reopening, the draft reconstructs the individual/subject with previously filled observations.

   <Image align="center" border={true} width="300px" src="https://files.readme.io/36d013626c1150e15af521af8c2fe286ee48d5757553ee56c796f71c51def157-subjectRegisterDraft.gif" className="border" />

2. ## Enrolment

   * Draft is saved on clicking "Next", "Previous", or "Back".
   * Only the enrolment flow is drafted. If the user is filling a program exit form, no draft is saved.
   * Enrolment drafts are displayed with the previously provided values on attempting enrolment again for the same program.
   * There is no separate "Drafts" section on the dashboard for enrolments; instead, the draft loads automatically when the user initiates enrolment again.

   <Image align="center" width="300px" src="https://files.readme.io/8bd82a5e32105748b856bef6ffa7517ae19a989080f547b65e67cd0564c27293-programenrolement.gif" />

<br />

3. ## General Encounter

   * One draft per encounter type for unscheduled encounters.
   * Multiple drafts can exist per encounter type (one per scheduled visit with a different date).
   * Draft is saved on clicking "Next" or "Previous".
   * Unscheduled encounter drafts are displayed under the "Drafts" section on the General tab of the Subject Dashboard.
   * Scheduled (Planned) encounter drafts are accessible by tapping "Do" on encounters under the "Visits Planned" section. The draft loads automatically when the user opens the scheduled encounter.
   * Users can delete or edit drafts directly from the dashboard.

   <Image align="center" width="300px" src="https://files.readme.io/d25f086f4ff2ca5b0abe4129926626d7d494cbc4748378622e8bb642652dc908-GEUPV.gif" />

<br />

3. ## Program Encounter

   * One draft per encounter type for unscheduled program encounters.
   * Multiple drafts can exist per encounter type (one per scheduled visit with a different date).
   * Draft is saved on clicking "Next", "Previous", or "Back".
   * Unscheduled program encounter drafts are displayed under the "Drafts" section on the Program tab of the Subject Dashboard.
   * Scheduled (Planned) program encounter drafts are accessible by tapping "Do" on encounters under the "Visits Planned" section on the Program tab. The draft loads automatically when the user opens the scheduled program encounter.
   * Users can delete or edit drafts directly from the dashboard.

   <Image align="center" width="300px" src="https://files.readme.io/4f2cdf94d820ff80083777b6866c2c2433fcec61c7b7625294bc4ad0cfc3b489-PEUPV.gif" />

<Image align="center" width="300px" src="https://files.readme.io/6dbc5f6177e879cfd200737debba645cf39a02e9a6007b235022299a310e910c-planned.gif" />

<br />

## Key points

* **Applicability:** Currently, this feature works for Registration, Enrolment and Encounter forms. Cancellation and Exit forms are not supported as drafts.
* **Display:** Registration drafts are displayed on the Register screen. Encounter drafts are displayed under the on the 'General' tab on the Subject Dashboard. Unscheduled encounter drafts are displayed under the 'Drafts' section and scheduled encounter drafts are accessible by tapping 'Do' on encounters under the 'Visits Planned' section. Enrolment drafts are displayed with the previously provided values on attempting enrolment again. Program encounters are displayed similar to Encounters on the 'Program' tab.
* **Save Checkpoint:** A draft save action is performed on clicking "Next" or "Previous" buttons while filling in a form, therefore, if User fills in a page but does not click on "Next" or "Previous" buttons, then the Draft saved would have content only till the previous page (On which "Next" button was clicked)
* **Exiting a form:** To exit from a form in-between, user may click on the "Header" "Back" button or click on "Footer" "Home" buttons** . For enrolments and program encounters, the back button also triggers a draft save.
* **Stale Drafts clean-up:** Usually drafts get deleted once you perform a final save operation to convert it to an actual entity. Along with that we have a periodic drafts clean-up which gets executed once a day, to delete drafts that were last updated more than 30 days ago.
* Timer Behaviour: When a draft is loaded, the encounter date/time timer is not re-initialized. It retains the original timestamp from when the form was first started. For new (non-draft) forms, the timer is initialized fresh.
