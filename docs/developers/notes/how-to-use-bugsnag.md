---
title: Use Bugsnag
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
## Type of errors

### Unfixable errors

If there are errors that are caused by network issues (like network timeout, socket timeout etc) then it is best to ignore those errors. Putting a fix to create more use-friendly error is not a very good use of time because - the number of different type of errors that one may get is very large. Secondly these errors can keep coming from different sources and hence one cannot completely handle them in the code.

DISCARD then IGNORE

### Errors fixed but is not deployed yet

Make a note of this on the Bugsnag issue.

DISCARD

### Errors fixed and is deployed

Note this applies only to web app and server which are online systems. In such cases one should mark it as fixed. This means that if such an issue is reported again then it has not been fixed.

FIXED

### An error is happening too many times

DO NOTHING (as by discarding one may lose the issue after 7 days). Mark Zenhub issue as high priority.

### Errors that should not have been reported

If an error is reported only because the code deals with it as an unhandled exception but it is a normal scenario then in such cases the code should be fixed. e.g. Login Failed because of bad credentials, or access denied, or random requests to the server on endpoints that don't exist (note in this case the client error may be relevant as the client is sending the request to the wrong endpoint but there is nothing to be done on the server).

### Configuration Errors

If an error is reported due to configuration issue then do not ignore the issue. Treat it as normal error.

### Errors without source map (avni-client)

If this happens then perhaps it is due to some miss during the release process. Sourcemap should be uploaded.

Mark as FIXED

## Analysing errors

* We should look at last 30 days, errors happening in production environment and not on developer machines (applies to emulators).
* You can look at additional information available in Bugsnag in its tabs like Release stages, Users, Models etc.
* Usually the higher frequency of error should be paid more attention to, in terms of prioritisation (note this applies across projects i.e. a higher frequency of server error is likely more important to fix than lower frequency client error and vice-versa).
* If an error doesn't have a source map associated with it then raise a bug to get the source map uploaded for that version.
* Use and maintain the bookmark created for analysis called - Production Open Issues.
* Check comment on the issue to check whether the issue is linked to Zenhub issue already. It is not linked via their issue tracking to Github as not sure what that will get us.

## Bugsnag Management

* When ignoring an issue, first discard it and then ignore it.
* Look for issue template in Zenhub/Github and add if necessary.
* Look for Zenhub issue from the comment on BugSnag issue
