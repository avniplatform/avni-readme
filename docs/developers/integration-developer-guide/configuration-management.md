---
title: Configuration management
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
**Where to define the property?**\
All properties must always be defined in the module's main properties file. The modules that depend on this should include the properties file using the `spring.config.import`. This allows for not having to define a property over and over again.

**What to give as property value?**\
Unless it is a secret or change based on context give it the actual value. If you are calling the property value dummy then call it something like amrit-main-dummy so that another developer can figure out where it is being picked from.

**How to manage secrets when running integration tests?**\
Firstly the integration tests that depend on test environments that may or may not be accessible in the future should be disabled before pushing. But since one may want to run these tests from the IDE setting their actual value every time via environment variables can be a pain. The secret properties file comes in here (but should be used only for test environments and not production). You can keep a secret properties file and it should be `git ignored`. The secrets can be maintained in the password managers like KeeWeb. In spring the property which is defined later overrides the property defined earlier.
