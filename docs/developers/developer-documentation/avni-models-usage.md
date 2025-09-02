---
title: Avni JavaScript Libraries Usage
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
### Context

[Avni Models](https://github.com/avniproject/avni-models) is a reusable JavaScript module used in the avni web app, android app, and rules engine. In these dependent projects, you will find the package dependency by name openchs-models. Similarly, there are other dependencies like rules-config, avni-health-modules, and openchs-idi (all these repositories can be found in the same [Github organization](https://github.com/avniproject))

### Releasing Rules Config

We do not have to do any release of rules config and instead, we use GitHub hash for it. The CI process published a build in the build branch of rules-config.

### Releasing Avni Models and other libraries (not mentioned above)

* `make release` Provide the next version number.
* `make publish` Publishes to Github, where-from the CI picks up and releases to npmjs.com. The package will appear in npmjs.com after a lag.
