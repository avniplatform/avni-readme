---
title: Users and Catchments
excerpt: 'How to guide: Creating Users and Catchments'
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## How to guide: Creating Users and Catchments from the Avni web-app

To access the features of the Avni app, users need to have a unique username and password to log in to the app and perform the activities as and when required. These login credentials can be created through Avni web-app where certain permission can be provided to each unique user as per the area of work and authority to access the data generated in the app.

### Prerequisites before creating the users:

The following items must be configured in the web app before proceeding with the user creation process.

1. Location Hierarchy, Locations ([Refer to this guide \[TO BE ADDED\]]())
2. Languages
3. User Groups ([Refer to this guide](https://avni.readme.io/docs/user-groups))
4. Catchments

### Creating Catchments:

A catchment is a group of locations that are serviced by a user i.e. the locations that a user works in. Only data captured against the locations within the catchments assigned to the user are synced to the android app of the user. The following steps can be followed to create catchments in the web app:

1. Navigate to the admin console

![alt\_text](https://files.readme.io/d32ed8d-image8.png "image_tooltip")

2. Click on the catchments and create a catchment

![](https://files.readme.io/8c7e39e-image2.png)

<br />

3. Provide a unique name for the catchment in the field given below.

![](https://files.readme.io/537772e-image7.png)

<br />

4. Add the locations which are to be part of the catchment.

![](https://files.readme.io/4220102-image11.png)

<br />

### Creating Users:

Once the above-provided prerequisites have been created successfully, we can proceed with the user creation process.

1. Navigate to the admin console in the Avni web app.

![](https://files.readme.io/d32ed8d-image8.png)

<br />

2. Click on the Users section and Create button as given below.

![](https://files.readme.io/a60ebcd-image9.png)

<br />

3. Provide a unique Login ID for each user. Login ID allows to have alphanumeric values which will be followed by @ProjectName. A Login ID that is already in use cannot be re-used to create another user. **Note:** The login name is a case-sensitive field. The user needs to provide the same login ID while logging in to the Avni app.

![](https://files.readme.io/18b704a-image4.png)

<br />

4. While creating a user, the administrator can provide a custom password by clicking on the toggle button highlighted below. This would populate two additional fields to enter a custom password and verify it by giving the same password again. 

![](https://files.readme.io/ee06b59-image3.png)

<br />

5. In case the custom password toggle button is not on, the system will continue with creating the default password. The default password would have the first four letters of the username followed by the last four digits of the mobile number provided while creating the user.
6. Provide the full name of the user along with mobile number and email address. The same mobile number and email can be used multiple times to create different users.

![](https://files.readme.io/6185872-image5.png)

<br />

7. Catchment created as given in this guide can be set here while creating the user. The system doesn’t allow to assign more than one catchment per user.

![](https://files.readme.io/ef2a51f-image10.png)

<br />

8. Set user groups as per the operational roles of the user. Multiple user groups can be assigned to a user. 

![](https://files.readme.io/511929c-image6.png)

<br />

9. Further settings specific to the user can be setup to customise the user experience 

   1. Preferred Language
   2. Track location - Switches on visit location tracking on the Field App
   3. Beneficiary mode - Enables the Beneficiary mode - a limited mode that allows beneficiaries to use the Field App
   4. Disable dashboard auto refresh - Disables Auto-refresh of MyDashboard of the Field App. Use if the dashboard is slow to refresh
   5. Disable auto sync - Disables automatic background sync. Use it if you want to trigger sync only manually
   6. Register + Enrol - Adds extra quick menu items on the Register tab to register and enrol to programs in a single flow
   7. Enable Call Masking - Enables Exotel call masking for the user
   8. Identifier Prefix - Identifier prefix for ids generated for this user. See[ documentation](https://avni.readme.io/docs/creating-identifiers) for more information
   9. Date Picker Mode - Set default date picker for the Field App
   10. Time Picker Mode - Set default time picker for the Field App

   ![](https://files.readme.io/a73b680-image1.png)

   <br />