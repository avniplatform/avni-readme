---
title: Environment setup for front end product development - Ubuntu
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
This page documents, how to set up your development machine so that you can contribute to Avni front-end code - mobile app or webapp. This assumes that you have access to some test avni server, with one or more implementations deployed, to which you can connect to from your development environment. If you want to set up a full-stack environment, please use [this](doc:developer-environment-setup-ubuntu).

This setup documentation assumes that the user knows how to install and use some of the commonly used utilities. There is enough documentation on the Internet for installing and using them (putting in your version of Ubuntu would give you better results). We may link these docs in some places but not always. The exact commands are `indicated as this always`. Please ensure you have the following software setup.

* Git
* Github account
* You OS user has sudo access
* Curl
* nvm
* (Optional) Automatically set node version when you change your directory based on the .nvmrc file present in that directory. Place the function code as it [in the file](https://raw.githubusercontent.com/avniproject/avni-readme-docs/master/example-code-files/cd_shell_func.sh).

### Other information

* The Github organisation for Avni is here - [https://github.com/avniproject](https://github.com/avniproject) which has all the repositories.

# Field mobile app components

### Android SDK

* Install Android SDK Manager
* Use SDK Manager to install - *platforms;android-28* and *build-tools;28.0.3*
* Set ANDROID\_HOME environment variable
* Modify PATH to include $ANDROID\_HOME/tools and $ANDROID\_HOME/tools/bin

### Android Emulator

* Install Genymotion or Android studio.
* Create a virtual android device and start it

### Avni Android Client

* Clone avni-client from Github
* Use nvm to install node version requires - `nvm install` from the avni-client directory. It would pick up the .nvmrc file to install the correct version.
* Install node dependencies - `make deps` (this would take some time in installing all the node dependencies)
* Verify unit tests are running fine - `make test`
* `cp packages/openchs-android/config/env/dev.json.template packages/openchs-android/config/env/dev.json`
* Modify the value of SERVER*URL in\_packages/openchs-android/config/env/dev.json* file to match your avni server.
* From the avni-client folder, perform *make run\_app* and then press sync button in the app. This should download all the configuration and master data that has been set up for this implementation.

# Web app components

* Clone avni-web-app
* Run `nvm install` from root dir of avni-web-app (to install the required node version)
* Install Yarn. Instructions [here](https://yarnpkg.com/en/docs/install)
* `make deps start` (ensure that you have started the server by providing the user)
* Open the app by going to [http://localhost:6010](http://localhost:6010)
* To start the web app as a specific user like admin\@jnpct or dev\@jnpct you can run - `REACT_APP_DEV_ENV_USER=admin@jnpct make start` or `REACT_APP_DEV_ENV_USER=dev@jnpct make start`

## **SETUP COMPLETE !!!**
