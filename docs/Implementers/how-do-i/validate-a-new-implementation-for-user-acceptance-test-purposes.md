---
title: Validate a new Implementation for User Acceptance Test Purposes
excerpt: ''
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
<br>

**UAT Test Scenarios** 

**Step 1: Download the Avni App** from Playstore to proceed with the test cases given below.

![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.001.png)

**Step 2: Login :**

- **Valid User Login:** Verify that a user can successfully log in with a valid username and password. (Ex. Username: xyza@ProjectName, Password: xyza7988)

- **Invalid User Login:** Confirm that the system handles login attempts with invalid usernames and passwords.

- Test Ex. 1:\*\* With an invalid username and valid password (Authentication error)

Username: xy@ProjectName, Password: xyza7988

- Test Ex. 2: With an invalid username with space applied anywhere in the user (Error should be displayed as incorrect username) 

User name:   xyza@cini\_uat, Password: xyza7988

- Test eg 3: When the user name is incorrect , it does not exist in the system. It will give an authentication error

- ` `Username: dinesh or dineshProjectName, Password: dine7988

- Test eg 4:\*\* With a valid username and invalid password (Authentication error)

  Username: xyza@cini\_uat, Password: xyza798

- Test eg 5:\*\* With a valid username and invalid password special characters (Authentication error)

    Username: xyza@cini\_uat, Password: xyza@7988

    ![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.002.png)  
  \*\*

- **Password Visibility**: Ensure the password field can be shown or hidden upon using the ‘show password’ toggle.

  `	`![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.003.png)

- **Forgot Password:** Forgot password option on the login page allows the user to generate a new password.

- By clicking on the forgot password, user can see the page where the registered user ID needs to be submitted. On providing the correct user ID, a pop-up will be displayed ‘We have sent an OTP on your registered Mobile Number’.

  ![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.004.png)![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.005.png)

- With that, the next page will be displayed with 3 fields to enter the one-time password received on the mobile number, the old password, and the new password. 

  ![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.006.png)

- By successfully submitting all the details, the user can change the password and log in with the user ID and new password.

`   `**3. Home Page:**  
\*\*

- **Home Dashboard:** The home page would have a dashboard to populate the aggregate (count) of different types of data, Ex. number of registrations, number of visits due, number of visits overdue, number of enrolments in the program, etc. By clicking on any of these cards, a list of individuals or other subjects will be displayed where the user can view profiles and details submitted in the registration form of any individual.

![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.007.png)

- Home Dashboard can have filters to update the data as per date or any other parameter to display card’s data accordingly.

![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.008.png)

**Last 24 Hours Statistics :**

- **Last 24 hours registration:** The user should be able to see the count of Registered individuals and click on it to list the details               
- **Last 24 hours visits:** The user should be able to see the count of Visits and click on it to list the details   

![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.009.png)\*\*      

**4. Sync:** 

- Sync button available on top right corner of the home page, allows user to sync the registration, enrollments, visits and changes done to the existing data. 

- ![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.010.png)

- By clicking on the sync button, system syncs the changes done in particular in device’s app with server. Data synced in app can be seen in the reports.

- Number shown on the sync button suggests the changes are which ready to be synced.

- A successful sync would display a pop-up as shown below and If sync isn't done popularly or it displays some error like a Fatal error or Association error we have to contact the administrator ![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.011.png)

![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.012.png)

- If sync fails with the reason Network request then users have to check the internet connection and try to resync.

**5.  Registration :**

- The register section should allow the user to register the subjects as per the project, Ex. Individuals, Anganwadi, Camps, Patients, etc.

![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.013.png)

- The user should be able to save, register the registration form, and proceed to the next registration form.
- After Registering the individual/any other subject in the mobile app, sync the data and validate that the data is reflected in the web app
- Register the individual/any other subject in the mobile app. Do not sync and validate that the data is not reflected in the web app.
- Register the individual in the mobile app using without turning on the network. Turn on the network, don't sync the data, and validate that the data is automatically synced after 10 minutes.

**6**. **Search Page :**

- Click on the Search button
- Select the subject (i.e. individual, camp, student, etc.) under Choose type, select other filters and click on Submit. 

![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.014.png)

- On the search page, option called included voided if the user toggles it and clicks on the search it should display all the voided and unvoided data
- The result should display the list of subjects as per the filter provided. Along with the list of subjects, ‘Total matching results will be displayed’ to populate the count of subjects as per the filter provided.

`	`![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.015.png)

- Please note that user can use any combinations of filter simultaneously to populate the results as required.

**6. More Page:**

- **Edit Settings:** In the ‘More’ section, the user should be able to click on the user icon to open ‘edit settings’. The edit setting should have configuration fields of Language, Location, Dashboard, and Auto-sync as shown below.

  ![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.016.png)

- In the **Language,** select the language the app content should be displayed and the app content should be displayed in the selected language. The default language is English

- If the language is not updated as select in the ‘edit settings’, then it is a bug.

- **Track location,** if it is enabled it will ask the user for permission if the user accepts the permission  then it will capture the longitude and latitude of the current location 

- Track location if it is disabled or they refuse to give the permission then it should not capture the user's location

- **Dashboard Auto-Refresh,** disabling this toggle would restrict the user from seeing updated version automatically

- If the user disables the auto refresh then the dashboard should not update the data on the dashboard automatically.

- **Auto Sync,** if the user enables the auto sync then data should sync automatically for every 10 minutes

- If the user disables the auto sync then data should not be synced automatically.

`		`![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.017.png)

- **Dashboard,** click on this it should display offline dashboards where aggregate cards of different visits due/overdue, registration, and enrolments are done. 

  ![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.018.png)

- By opening the aggregate cards given on the dashboard, the user can see the list of individuals/subjects and their profiles which aggregates to a count in the dashboard. (Refer to point#7 Profile more details)![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.019.png)

  ![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.020.png)

- **Entity Sync Status**

- **Setup Fast Sync**

- **Change Password,** click on it directly to the password change page

- Users can enter their current password 

- The user should be able to enter the new password

- Password Visibility, ensure the password field can be shown or hidden upon user selection.

- Password Visibility (Toggle): Verify that toggling the password visibility option works as intended.

- Password mismatch if the user gives the current password as invalid then it should display the incorrect password or user ID 

- Forgot password, If the user doesn't remember the current password while clicking on it. It will send you the OTP using that user enters the new OTP and also a new password

- Password mismatch if the user gives the mismatch value for Enter a new password and Confirm new password then it should display an error password mismatch

Eg: Enter a new password: din123, Confirm new password: din321

- Password successfully: if the user gives the match value for Enter new password and Confirm new password then it should display password changed successfully.

  ![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.021.png)

- **Logout** should help the user close the current session and return to the login screen. Upon clicking the logout button, the user should be able to see a pop-up to confirm to end the current session and logout.

  `	`![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.022.png)

**7. Profile:** 

- **Subject Profile:** The profile should typically contain details submitted in various forms and will populate details of enrollment and forms that are scheduled and filled previously.
- Subject profile would typically have name, gender, age, address on top.
- Profile page would contain the list of program subject enrolled to along with the option to enroll in a new program if eligible but not enrolled yet.
- Profile page would have the summary section to display important details which are filled in different forms.
- Profile would also contain visit planned which would display the visit scheduled along with completed visits section.

![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.023.png)![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.024.png)

![](Aspose.Words.e7a1731f-5ee8-4023-8075-158ab95af182.025.png)

**Important note :**  
\*\*

**The changes done in the application should be synced to save these changes on the server. Sync can be done manually from the button on the home page's top right corner.**