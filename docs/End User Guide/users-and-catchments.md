---
title: Users and Catchments
excerpt: Guide for creating Users and Catchments
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

### Updating Catchments:

Updating Catchments are not encouraged, especially, when it involves removal of location or replacing one set of locations with another set of locations. Ideally, create a new catchment with required locations and assign the same to the user, keeping the old catchment as is.<br /><br />This is also the best practice regardless of whether the catchment is linked to a single user or multiple of them.<br /><br />Adding more **users** to a catchment is a different thing, and is perfectly safe — the catchment itself does not change. See [Adding many users to a catchment that already exists](#how-to-guide-adding-many-users-to-a-catchment-that-already-exists).

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

## How to guide: Adding many users to a catchment that already exists

When a team grows, you often need to add a batch of users to a catchment that is already set up. Creating them one at a time from the admin console works, but for a large batch use the **Users and Catchments** CSV upload instead.

You do **not** need to create a new catchment for these users, and you do **not** need to work out and type the full list of locations the catchment covers. One location is enough to point each row at the right catchment.

### Steps

1. Open the catchment in the admin console (Admin app → Catchments) and note two things — its exact name, and any one location listed inside it.
2. Go to the Admin app → **Upload** section and download the sample file for Users and Catchments.
3. Add one row per new user. Two columns connect the user to the catchment that already exists:

   * **Catchment Name** — the name of the existing catchment, spelt exactly as it appears in the admin console. Avni matches the catchment by its name, so the user joins that same catchment instead of a new one being created.
   * **Location with full hierarchy** — any **one** location that is already part of that catchment, written with its full lineage. For example: `Bihar, District1, Block11`.
4. Fill in the rest of the columns for each user. Username, Full Name of User, Email Address and Mobile Number are required. Preferred Language, Track Location, Date picker mode, Enable Beneficiary mode, Identifier Prefix, User Groups and Active are optional. If your organisation syncs by a registration attribute, the sample file also carries a sync attribute column, and a value for it is required.
5. Upload the completed file from the same Upload screen.

> 🚧 Choose a location that is already inside the catchment
>
> Avni does not check that the location you typed belongs to the catchment. If it does not, that location gets **added** to the catchment. The catchment then covers more area than intended for every user assigned to it, and all of those users are put through a full re-sync. Copy a location from the catchment as it stands today, rather than typing one from memory.

> 📘 The same file also updates existing users
>
> If a Username in the file already belongs to someone, that row updates the existing user instead of creating a new one — including moving them into whichever catchment the row names. Keep the file to genuinely new users, unless you intend to change existing ones as well.
