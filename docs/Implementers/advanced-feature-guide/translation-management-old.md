---
title: Translations
excerpt: ''
deprecated: true
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: creating-identifiers
      title: Creating identifiers
---
Avni allows the management of translations using the Admin web interface. Below are the steps to manage translations.

## 1. Add Languages to Organisation Config

First languages have to be added to organisation config. Only the languages that are added in the organisation config are made available to the translation framework

## 2. Download Keys

From the translations page of the Avni Admin app, download the keys after choosing the platform. For the mobile app choose 'Android'. This will download a zip file containing one JSON file per language available in the organisation config. The JSON file will contain keys for both platforms as well as implementation covering all labels in the app, form fields, and any other concepts created in the implementation. The file will contain values for any existing translations. 

## 3. Updating files with translations

The JSON files can be edited with any tool that the implementer is comfortable with. For use cases, where multiple translators are involved or a lot of keys are to be translated we recommend using an external translation management system (TMS) like [Lokalise](https://lokalise.com) which provides a sophisticated editor for performing translations. The TMS provides the ability to import/export JSON files and support a variety of use cases related to translations. Avni has an enterprise-free plan of Lokalise. If you would like to use Lokalise, please request the Avni team to create your account and project to get started.

## 4. Uploading Translations

When downloading translations to a JSON file in Lokalise, under `Advanced settings`, select `Don't export` as value for `Empty translations` field. Once the JSON files are available with updated translations, upload the file in the Avni admin interface by choosing an appropriate language. Be careful about choosing the correct language.

![](https://files.readme.io/d92456b-Screen_Shot_2019-10-21_at_5.58.47_PM.png "Screen Shot 2019-10-21 at 5.58.47 PM.png")

## Translation Dashboard

Once the translations have been uploaded, the translations dashboard will reflect the status. 

The users need to sync their devices to get the new translations.
