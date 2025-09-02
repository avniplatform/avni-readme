---
title: Rollout your own Avni App from Play store
excerpt: Branding options available in Avni, and how to proceed
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
There are can be several reasons for rolling out your own app from the play store.

* You have a different deployment of Avni
* You want your own branding (icons, logos, etc)

You can change the following

1. App logo
2. App name
3. Splash screen (Splash screen is done through [extensions](doc:extension-points))

You may be required to change the following, if your hosting is not managed by Samanvay or not planned to be managed by Samanvay in future.

1. Firebase configuration
2. Bugsnag configuration
   1. Create an account in Bugsnag and create a project of type React Native.
   2. Get the Notifier API key from the project settings

A new app is called a flavor of the app (terminology from [Android flavors](https://developer.android.com/build/build-variants)). There are a few flavors already configured today. This configuration is done in the [avni-client](https://github.com/avniproject/avni-client) repository.

## Steps:

1. [Create new flavor in android-client](https://avni.readme.io/docs/flavouring-avni#steps-to-create-a-new-flavor-in-client)
2. [Configure to get build from circle-ci](https://avni.readme.io/docs/flavouring-avni#steps-to-do-in-to-get-build-via-circle-ci)
3. [Steps to follow in google playstore](https://avni.readme.io/docs/flavouring-avni#steps-to-do-in-google-play-store)

### Steps to create a new flavor in client:

* Under `packages/openchs-android/android/app/src`, create a folder with the flavor name.
  * Flavor naming conventions:
    * Use camelCase for flavor name, since it is used in the android [docs](https://developer.android.com/build/build-variants).  This is also inline with the folder names generated during the build process.
    * The flavor name, need not have org name, just app name will suffice. 
    * Eg: for `Teach Nagaland` app from LFE, the flavor name can be `teachNagaland` and not `teach_nagaland` or `lfeTeachNagaland`.
  * Under `assets` folder add `logo.png`. The file needs to be in `png` format for the animation in the screensaver to work and for the logo to appear in the Login page.
    * To resize the logo to a reasonable size, `Preview` app can be used. Open the file and go to `Tools -> Adjust Size`.
    * To convert the logo from say, jpg to png format, open the file, then go to `File -> Export -> Change the format to png -> Save`.
  * Under `res` folder, create folders for each resolution. This images in this folder is used to display launcher icon in android app. This [website](https://icon.kitchen/) can be used to generate circle and square icons for each resolution.
  * To integrate with firebase analytics, copy `google-services.json` from [firebase console](https://console.firebase.google.com/u/0/) by creating a project specific to the flavor or an app within an existing project as per need. To view data of an app within a project, you can : Add comparison > Dimension = "Stream name" > Match Type = "exactly matches" > Value: select your app via checkbox
  * When some resources are common across flavors, add it under `packages/openchs-android/android/app/src/main` folder (instruction for Avni product team only)
  * Add a flavor specific privacy policy under `docs` to be linked to from the play store app listing using the Avni privacy policy as a reference and make changes specific to the flavor such as app name.
* Changes to be made in `build.gradle`
  * Add the `signingConfig` for the new flavor. To create keystore, check [here](https://developer.android.com/studio/publish/app-signing#generate-key) or use the following command.
    * `keytool -genkey -v -keystore <flavor>-release-key.keystore -storepass <keystorepassword> -alias <alias> -keypass <keypassword> -keyalg RSA -keysize 2048 -validity 10000`
  * In `packages/openchs-android/android/app/build.gradle`, under `productFlavors` add the key value pairs for the new flavor. 
    * For applicationId, use the format, `com.openchsclient.{client_name}.{region_name}`, where `region_name` need to be given if different flavors of the app exists for different regions.
    * create new bugsnag app, and add its API key.
  * Using `sourceSets` config in the `build.gradle`, modules specific to flavors can be configured.
* In `flavor_config.json` add the config for the new flavor. The values here are used by make tasks.
  * super admin password of the server url need to be mentioned as value for `prod_admin_password_env_var_name` .

### Steps to do to get build via circle-ci:

* Update `.circleci/config.yml` to add flavor to enum of valid `flavors`.
* Add environment variables.
  * Go to `Project Settings -> Environment variables` in circleci.
  * Add values for key password (`<flavor>_KEY_PASSWORD`), key store password (`<flavor>_KEYSTORE_PASSWORD`), key alias (`<flavor>_KEY_ALIAS`), bugsnag api key.
* Refer this [link](https://avni.readme.io/docs/release-process-for-the-cloud#circleci-build) to know how to generate apks and aab from circle-ci for specific flavor.

### Steps to do in google play store:

* Create app on google play console.
* Under `Grow -> Store Presence -> Main store listing` and enter the details. For phone and tablet screenshots, same screenshots can be uploaded.
* Under `Grow -> Store settings`, enter the details similar to other app.
* Go to `Publishing overview` and finish the steps mentioned to be able to publish for review.
  * For privacy policy, make sure the privacy policy mentions the name of the app instead of `Avni`.
  * To complete the steps take the help of already filled values of other apps. For that refer, `Policy and programmes -> App content -> Actioned` 
* Create release. 
  * As per this [link](https://stackoverflow.com/questions/73132752/i-dont-use-ads-in-my-flutter-app-then-why-this-message-is-showing-in-my-play-co), seems like Firebase Analytics plugin needs permission `com.google.android.gms.permission.AD_ID`. Hence click `Yes` for `AD_ID` permissions and check `Analytics` for usage.
  * Upload the bundle downloaded from the playstore.
* Send the changes made above for review.

### How to use a specific flavor:

By default, generic flavor without organisation branding will be picked up . When need to run make tasks for specific flavor, pass the flavor variable to the make task.

Eg: ` make run_app_staging flavor='apf'`

### Other branding changes in Avni that are relevant

There are other places where icons/colours can be configured. Below is a table that summarizes the changes that are possible. All changes can be performed through the App Designer.

| Type   | Item              | Specifications                                                                                                |
| :----- | :---------------- | :------------------------------------------------------------------------------------------------------------ |
| Icon   | Subject Type Icon | jpg/png square images 75 \* 75 px                                                                             |
| Icon   | Report card       | jpg/png square images 75 \* 75 px                                                                             |
| Icon   | Menu Item         | Material Community Icon from [https://pictogrammers.com/library/mdi/](https://pictogrammers.com/library/mdi/) |
| Colour | Program           | RGB                                                                                                           |
| Colour | Report Card       | RGB                                                                                                           |
| Colour | Form Header       | RGB                                                                                                           |
