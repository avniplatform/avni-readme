---
title: Troubleshooting and Error Debugging
deprecated: false
hidden: false
metadata:
  robots: index
---
<br />

## BugSnag Error Monitoring

For development and support team members:

1. **Access Bugsnag**: Log into the Bugsnag dashboard
2. **Search Inbox**: Navigate to the Bugsnag inbox search functionality
3. **Find Issues**: Search for relevant error reports and stack traces
4. **Analysis**: Use the detailed error information to understand and resolve bugs

## Upload Issue Info

When a user encounters an error in the application:

1. **Error Alert**: An alert popup is displayed on the screen
2. **Upload Button**: The alert contains a button labeled "Upload issue info"
3. **Upload Process**: Click this button to automatically upload application logs to AWS S3   
4. **Debugging**: The uploaded logs can be accessed (env-user-media bucket ->organisationName ->adhoc-dump-as-zip-username@orgName-uuid)by the development team analyze the root cause of the issue and Implement appropriate fixes

If the user's face an issue and the alert is not shown or does not contain Upload issue info they can click on more in the home page, and then click on Upload app info.

<br />
