---
title: Advanced Configuration
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
### Blacklisting of URLs

You can set the `AVNI_BLACKLISTED_URLS_FILE` environment variable to the JSON file location from the working directory of `avni-server`. The [ant pattern](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/util/AntPathMatcher.html) is used to match the request path and if the path is present then the response will be rejected with 403 HTTP status. The JSON file should have an array of strings - each representing a path pattern.

This file is read at the startup. If a file is specified and there is problem with the file then server wouldn't start.