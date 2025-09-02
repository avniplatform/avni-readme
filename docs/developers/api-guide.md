---
title: API Guide
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
## About the API

Avni API is available at `/api` of your Avni server. Avni offers four resources - **subject**, **enrolment**, **programEncounter** and **encounter**. Currently, Avni supports the only authentication and pulling data. Other APIs will be added over time, but if you need them for your project please contact us.

*Please note that there are many rest endpoints in Avni which are used for communication between avni mobile field app and avni web app. These are not meant for integration with third-party applications/services because they have not been designed for that purpose and hence are not documented.*

If you are unfamiliar with Avni's **domain model** then read the following pages first, but in very short - *subject* is a top-level entity, *enrolments* and *encounters* are children of the *subject* and finally, *programEncounters* are children of *enrolment*.

1. [Terminology](doc:terms-and-their-definitions) 
2. [Implementer's concept guide - Introduction](doc:implementers-concept-guide-introduction) 

### Helpful Reference

1. Swagger documentation is available here:\
   [https://editor.swagger.io/?url=https://raw.githubusercontent.com/avniproject/avni-server/master/avni-server-api/src/main/resources/api/external-api.yaml](https://editor.swagger.io/?url=https://raw.githubusercontent.com/avniproject/avni-server/master/avni-server-api/src/main/resources/api/external-api.yaml)

2. If you use Postman you can use this public collection (it may not have all the API requests in it)\
   [https://www.getpostman.com/collections/cd269030533a58e1c072](https://www.getpostman.com/collections/cd269030533a58e1c072)
   1. For Glific postman collection, check [here](https://drive.google.com/file/d/1juM7NfMevlo50FwIeIVFqtgADlRa2-BF/view?usp=drive_link) - Some of the API endpoints are not exported correctly by postman. Refer the [Glific API guide](https://glific.github.io/slate/#introduction) or server code incase of any confusions.

### Pulling data from Avni

If your objective is to pull data from Avni into your own database then you should use `/api/subjects`, `/api/enrolments`, and so on. These endpoints provide the response in the form of pages of 100 entries each by default (see the swagger documentation). The entities in these pages are arranged chronologically i.e. it will give the oldest entity first followed by newer and so on. The chronology is followed within the page and in page order.

The *lastModifiedDateTime* field is the key. If you are reading the entities for the first time it is advisable that you start from something like *1900-01-01T00:00:00.001Z*. Once you finish reading all the entities you can store the last entry's date, and next time you can start from the value of the field "Last modified at" (not inclusive) in the audit section of the last entity you have successfully read.

Note that entities can be repeated over time if the same entity has been updated by the user.

### Media observations

In order to create /  update media type observations one can pass the URL of the media file (the URL access should not require any login). Avni will download this file and upload it to its own media server. If the URL provides is from the Avni media server then Avni will simply update the observation but not try to move the media file.

To know how to create download URL for media files stored in google drive, refer [here](https://avni.readme.io/docs/structure-import-metadata-excel-excel#google-drive-files)

### Authentication

1. Using the Avni Admin web app set up a user that you will be using to login via the API. This user should have the role *User*. Ensure that the user is not challenged with a change in the password on the first login. You can do that by logging in once from the web application. 
2. Avni uses AWS Cognito for authentication, hence the API code has to authenticate itself too. Please use the following references for authenticating with Cognito for various languages.  
   * **Java** (check the `signIn( )` method in the example) - [Readme](https://gorillalogic.com/blog/java-integration-with-amazon-cognito/)  
   * **JavaScript** - [Readme](https://aws-amplify.github.io/docs/js/authentication#sign-in)  
   * **Python** - [Readme](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/cognito-idp.html#CognitoIdentityProvider.Client.initiate_auth)
3. The token provided by Cognito has to be used in the API (as documented in swagger)

### Getting a token for using Avni API via postman, curl etc

A simple way is to login via the web app. Open the Chrome dev tools (or similar in other browser) and follow the following

* go to the Application main tab 
* Local storage sub-tab
* Server inside local storage
* Copy the value of auth-token (this value can be used for auth-token header mentioned in the API swagger doc)

If the web app was open for a while, you should refresh the page and then do the above activities.

### Versioning

Avni API supports two versions 1 and 2. 2 is preferred version as it is the latest.

* 1 - The observation dates are in ISO format `yyyy-MM-dd'T'HH:mm:ss.SSSZ`, e.g. `2000-10-31T01:30:00.000Z`
* Same as 1 but with observation date in`yyyy-MM-dd` format i.e. "2023-10-05". This allows one to get the date as intended by the user and not having to do timezone adjustment.

### Recommendation (not a requirement)

1. If you are pulling the data in a background process, write your code such that it can handle failure elegantly. The failure can happen with the network or in your code. Most likely you would need to save the lastModfiedDateTime value for each entity in your database. So that when the background processes run subsequent times it can read it from the database. If you save in the memory and your server has restarted you may lose the context of how much you have already read.
2. For the background process, consider the transaction boundary carefully between saving the lastModifiedDateTime and the actual entity in your system. A transaction boundary where one entity is processed and lastModifiedDateTime is updated is the best. But you may choose some other solution.
3. This may be obvious but you need to run the background process in a sequence so that you process all the subjects first, followed by their respective programEnrolments and so on.
4. The POST method for Avni entities is an Idempotent operation, which enables the API client to perform Create, as well as Update of an existing entity, without having to go through the trouble of checking for its existence before making an update call. Its currently exposed only for following entity types:
   * Individual
   * Encounter
   * Program Enrolment
   * Program Encounter and
   * Task
