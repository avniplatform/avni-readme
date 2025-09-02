---
title: Avni Client Coding Guide
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
Avni client is developed using ReactNative, NativeBase, RealmJS, and Redux. Some of the core concepts for these frameworks and libraries are:

* [ReactNative Basics](https://reactnative.dev/docs/0.65/getting-started) 
* [React main concepts](https://reactjs.org/docs/hello-world.html) be also useful
* Official documentation of RealmJS, NativeBase, and [Redux](https://devdocs.io/redux~3-basics/).

The main building blocks of Avni Client are as follows:

### Views and components

There is one view component class for each screen in the mobile app. These use other reusable components.

Views **get all** their data (state) by dispatching actions that return the state for the view or via the props. Views must not call the service to get data. Typically each view is backed by one redux action function collection (or file). Views should not call services to save data directly. Note that when services are called in render it can lead to performance degradation of the UI as it bypasses the differential UI update based on the state provided by React.

The data used by view can be avni model classes or state objects. Avni models provide business methods where domain logic can be written. The state should have transient data meant for UI, workflows, etc.

Redux dispatches are synchronous calls by themselves.

For long-running redux actions, setTimeout should be used when calling redux actions.

Avoid defining anonymous functions which are more than 2-3 lines in the render method. Define these functions at the class level and then make an anonymous function to call this function. If large functions are defined in render then the function definition is created on every render.

When navigating to a view using the navigator, pass minimal information via query params. The view to which navigation is happening can load the rest of the information from the database. This ensures minimal contract and coupling. e.g. instead of passing programEnrolmentUuid and individualUuid both just pass the former. Also, pass ids instead of objects. e.g. in the previous example do not pass the program enrolment object as ID is sufficient.

### Redux Actions (or Reducers)

Redux actions call services to interact that interacts with the database or to make server calls. Since avni-client is an offline application server calls are made in very few places. SyncService primarily handles server interaction.

Each Redux action has a single top-level state object passed onto the views after mutating.

Realm DB calls are synchronous.

Redux actions call clones in several places. One should be careful about making very deep/large clones as that may affect the performance. Such clones can happen when cloning models. e.g. a deep clone of an individual can end up cloning a very large number of entities - technically the entire database as individuals can be connected to other individuals.

### Services

Services are responsible for the interaction with the database, making server calls, and other IO calls. The service can/should use other services. (Services plus Repository as in DDD).\
Services also manage database transaction boundaries.

Services must not dispatch any action to redux.

### Avni Models

Avni models are objects mapped to the realm database (Entities as in DDD).

### State

Actions call services to fetch data and are responsible for constructing a new state to be returned to the view. Sometimes actions do make use of State classes that encapsulate state manipulation logic specific to one action/view. State classes also make it easy to write unit tests without mocking/stubbing services - that would be required if actions need to be tested.
