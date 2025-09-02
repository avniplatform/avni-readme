---
title: saga(s), middleware(s), reducers, and redux store
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
Avni web app uses redux-saga, redux stores, and react admin. Redux Store is where everything comes together. It is made of up all different types of middleware and reducers.

**Different types of middlewares and reducers**

* Framework level (React routing, React Admin Form)
* Saga
* Avni Application (thunk middleware)

**About Saga (Saga provides frontend cache)**\
It is composed of React Admin Saga and Avni application sagas.\
Saga (watcher functions) watches for specific redux actions to be dispatched. The watcher calls the mutating reducers which set the data in the store. The watcher could also return the data back if it is already present.

One implication of having multiple functional-only sagas is that each saga can have a different cache and *currently*, there is no way to clear the cache across sagas. If possible there should be common sagas that can be used across modules.

**About Application Reducers**\
One should not that the reducer JS files provide the reducers but also provide convenience functions to invoke the reducers.

### To understand the full wiring - in the source code follow from

*index.js ->> MainApp ->> common/store -> createStore.js*

**createStore.js is the main file to understand the wiring**\
&#x9;sagaMiddleware (generic error handling here)\
&#x9;Redux store with root reducer\
&#x9;Root Saga

### TBD

* What are the different frontend modules?
  * UI and non-UI
* What type of cross-module communication is required?
  * to support workflow that go across modules
  * management of cache across modules
* What is the mapping between modules, redux store, and sagas?
  * Currently, one big store is created. Should this be multiple redux stores?
* Ability to work on only some modules so that the hot loading works faster.
