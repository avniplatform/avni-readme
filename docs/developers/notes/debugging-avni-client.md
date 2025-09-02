---
title: Debug Avni Client
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
### Version compatibility (Hermes, Chrome, React Native)

* React Native supports debugging via Chrome Debugger (DevTools)
* Realm doesn't support debugging with JSC only with Hermes
* Since Chrome keeps getting updated on our machines, so even with hermes one needs to be at a certain version for chrome debuggers for specific versions of react native (and maybe realm also).
* The above problem is solved by Flipper (desktop app) which has an embedded chrome debugger - hence at fixed compatible version.
* For our code, Flipper Version 0.176.0 (50.0.0) has been tested to work.

### How to use

When switching the JS engine `make clean_app` is required. `enableHermes`default value is false.

```shell
# switch to hermes
enableHermes=true make clean_app run_app
# continue to use hermes
enableHermes=true make run_app

# switch to JSC
make clean_app run_app
# continue to use JSC
make run_app
```

In the `make run_app` log (not Metro) verify the engine by looking for `[Avni] Hermes Enabled:`

### Flipper screenshot

If you are running JSC you will get a clear error when using the Hermes Debugger menu in Flipper. Do learn about Chrome debugger shortcuts. Command+P in sources to open a file.

![](https://files.readme.io/542790a-Screenshot_2023-02-24_at_9.34.09_AM.png)

### Limitation

Cannot debug the errors that happen on start-up of the app as by the time one attaches the Flipper the application may have crossed the code that one may be interested in debugging.
