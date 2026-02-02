---
title: Troubleshooting and Error Debugging
deprecated: false
hidden: false
metadata:
  robots: index
---
<br />

## Method 1: Upload Issue Info

When you encounter an error in the application:

1. **Error Alert**: You will see an alert popup displayed on screen
2. **Upload Button**: The alert contains a button labeled "Upload issue info"
3. **Upload Process**: Click this button to automatically upload application logs to AWS S3 (env-user-media bucket ->organisationName ->adhoc-dump-as-zip-username@orgName-uuid)
4. **Debugging**: The uploaded logs can be accessed by the development team analyze the root cause of the issue and Implement appropriate fixes

If you face an issue and the Alert is not shown or does not contain Upload issue info you can click on more in the home page, and then click on Upload app info.

## Method 2: Bugsnag Error Monitoring

For development and support team members:

1. **Access Bugsnag**: Log into the Bugsnag dashboard
2. **Search Inbox**: Navigate to the Bugsnag inbox search functionality
3. **Find Issues**: Search for relevant error reports and stack traces
4. **Analysis**: Use the detailed error information to understand and resolve bugs
