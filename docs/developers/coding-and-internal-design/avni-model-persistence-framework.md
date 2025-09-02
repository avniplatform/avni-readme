---
title: Avni Model Persistence Framework
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
### History

There were two ways to register object schema in the realm database.

1. Provide a class (and realm will look for "schema" property in the class to get the definition.
2. Provide a plan JavaScript object describing the object's schema.

In earlier versions of Avni models with Realm, we were using approach 1. This meant that when we did db.objects(...) the objects returned were of model class with all the domain methods on it.

### On upgrade

On the upgrade of Realm to 10.x, taking approach 1 now required that the registered class must have a constructor which takes specific arguments and extends from Realm.Object class (calling it super). This by itself was not an issue but the model class now cannot be constructed outside the realm database context (e.g. db.write taking a code block). One could not simply do new Encounter() anymore.

The above was a huge issue for us since we used our models in service, actions, and rules - in the avni client and in the web app. Avni models were imagined to be plain rich domain objects (like we use with Hibernate). Hence we had to choose option 2 and create the model class objects before handing them over to the rest of the code. Note that the rest of the code uses fields as well as methods of model classes.

### Solution adopted

When an object is loaded from the realm using db.objects(...).filtered(...) we will objects that are not instances of our model classes - as the realm is not aware of the model class. So before these objects are given to the rest of the code to call methods etc, we wrap them under model classes.

Since all objects fetched from the database come from db.objects(...), the db class is proxied. See RealmProxy class which proxies the actual realm class. Other realm classes like RealmResults, RealmList, etc can also be proxied now - so see classes RealmResultsProxy, RealmListProxy, etc. These classes ensure that the object getting back to the caller is model classes or collections that have models.

### What does the model look like?

See ProgramEnrolment ([https://github.com/avniproject/avni-models/blob/master/src/ProgramEnrolment.js](https://github.com/avniproject/avni-models/blob/master/src/ProgramEnrolment.js)) as an example.

* constructor with that argument
* note the getters/setters delegating to that

`that` is the actual object that realm got out from the database. Our proxies when creating model class instances pass `that` to the model class. Model classes in getters for other model object instances (like EncounterType) or model collections (like Encounters) instantiate the model classes backed by realm. You can follow the helper methods in PersistedObject which do this.

`that` is not meant to be used outside these framework classes (PersistedObject, framework classes, etc). See all framework classes here - [https://github.com/avniproject/avni-models/tree/master/src/framework](https://github.com/avniproject/avni-models/tree/master/src/framework)

When model instance classes are saved then we get hold of the underlying object `that` and persist that. `that`s are connected to children `that`. e.g. `that` in program enrolment is connected via program encounters or encounter type property to a `that`(s) correspond to them. Also, model instance ProgramEnrolment is not linked to EncounterType and ProgramEncounter model class instances.

### Pattern of usage

When we do db.objects(...) or db.objects(...).filtered(...) and we want to our model class instances, we can simply do .map(\_.identity)

### Limitations

* We cannot use JS spread operator in model objects
* We cannot use Object.keys on model objects (as it would give back that which is not what we want)
* lodash binds to Array.prototype functions that mutate array ('pop', 'push', 'shift', 'sort', 'splice', 'unshift'). this implies that if lodash is used on RealmListProxy which is an array as it extends from it, our overridden methods will not be called. Hence we cannot use these methods to mutate. We usually don't use lodash to mutate realm lists, but if it is done then we can simply use array methods available to us which have been overridden in RealmListProxy.

### Bypass for performance reasons

When we display a list of objects in the mobile view (like Individual Search Results), we are providing infinite scrolling to the user. Hence we cannot follow the approach of converting all the realm objects to model instance objects and providing it to the <FlatList/>. Hence in this case we can use the function getUnderlyingRealmCollection from our model framework. The underlying collection is provided to FlatList and when renderItem or renderRow etc are called then we materialize it to model class instance.

### More details

* RealmListsProxy is further proxied by JavaScript Proxy which handles length and indexer \[] operator. It can be seen here - [https://github.com/avniproject/avni-models/blob/master/src/framework/RealmResultsProxyHandler.js](https://github.com/avniproject/avni-models/blob/master/src/framework/RealmResultsProxyHandler.js)
* We had to implement custom toJSON as realm toJSON methods on realm objects don't work. They have a fix it seems which works only in hermes version of the JS engine. The code can be seen here - [https://github.com/avniproject/avni-models/blob/d54b6fcd2c8503ba5d5b55b871d93cbcaa8e7bb5/src/PersistedObject.js#L41](https://github.com/avniproject/avni-models/blob/d54b6fcd2c8503ba5d5b55b871d93cbcaa8e7bb5/src/PersistedObject.js#L41). toJSON is required sometimes when we post the models objects to the server.
* We should not declare properties in JS class like this anymore - [https://github.com/avniproject/avni-models/blob/30cdfffa23eb961055875cadd454a133040cfc4f/src/SubjectMigration.ts](https://github.com/avniproject/avni-models/blob/30cdfffa23eb961055875cadd454a133040cfc4f/src/SubjectMigration.ts) as these will come from getters and setters

### TOOL - INTELLIJ LIVE TEMPLATES

**for defining properties (as they can be mistake-prone)**

#### for entity collection properties

```javascript
get $prop$() {
    return this.toEntityList("$prop$", $entity$);
}

set $prop$(x) {
    this.that.$prop$ = this.fromEntityList(x);
}
```

#### for sub-entity properties

```javascript
get $prop$() {
    return this.toEntity("$prop$", $entity$);
}

set $prop$(x) {
    this.that.$prop$ = this.fromObject(x);
}
```

#### for primitive properties

```javascript
get $prop$() {
    return this.that.$prop$;
}

set $prop$(x) {
    this.that.$prop$ = x;
}
```
