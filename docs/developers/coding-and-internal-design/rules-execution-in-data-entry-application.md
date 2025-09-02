---
title: Rules execution in data entry application
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
One of the key features of Avni is that it allows the execution of rules from various hook points in the mobile app. The same set of rules need to be also run from the data entry app. These rules are written in JavaScript (because of the universality of the language and that front end is also developed in JS using React Native). In a mobile app, the rules can access the complete object graph from the current object (subject, program encounter, encounter and program enrolment) from the offline database. In the web application, this approach cannot be followed without creating terrible user experience and server resource utilisation. This is because one subject could have 200 program encounters, having thousands of observations and referring to hundreds of concepts, in them in some cases which would need to be loaded onto the web app.

The performance gains and resource utilisation be achieved in the following way:
 * Do not load all the dependent entities, like program encounter for an enrolment beforehand. Load them lazily. Doing this in the browser is not helpful because each one would require a network round trip - leading to hundreds of calls.
 * Run the rules on the server in a separate node process. It cannot be run within Java*. The web app submits its data to Java server, Java server posts the data to a node process which runs the rule,  returns the result back to Java and that goes back to the web app.
 * Node process loads concepts and transactional objects on-demand from the database. Node process will have read-only access to the database.

One of the driving design factor is to keep the knowledge of the rule server's knowledge of avni database to the minimum. Given the size of the JS bundles, it made sense to allow rule server to load from the database instead of serialising from the avni server.

## Responsibilities
**Avni Server (Java)**
 * Receives transaction object from the data entry app.
 * Loads all the entities required from the database and puts them in objects. These objects have the same contract/data-model as the contract between Avni server and data entry app.
The linked objects have to be loaded but which ones? The logic behind loading something and not loading others is that all objects that are likely to influence the rule will be loaded and others won't. A chronological view is taken to decide this. For example - when a subject is created program enrolments are not even created, hence they should not be loaded. It is possible that when the subject is edited the program enrolments exist - but so far we have not tried to support rules which work differently on create and edit. This is because we consider edit to be largely data correction and not part of the workflow. In that sense, Avni transaction objects are all event like and data created are not changed - only new data is created. It is possible that genuine edits of subjects and program enrolments are done - but we work under the limitation that those won't require rules to know the latest data.
Lastly, as we are linking subjects to each other, and have individual relationships we would need to modify some of the things below but not too much (this is to keep things simple to develop the first version of this). In absence of that subjects and program enrolments are sandboxes - not dependent on other subjects and program enrolments.
In all of the following, the current object is always loaded.
  * **Subject** - Nothing else.
  * **Program Enrolment** - Parent subject
  * **Program Encounter** - Parent enrolment and sibling program encounters of all encounter types, older than current program encounter's DateTime. It should also include sibling planned program encounters. Include canceled also but only the ones which are older than the current program encounter's DateTime.
  * **Encounter** - Parent Subject, sibling older encounters (not program encounters) of all types and older program enrolments. It should also include sibling planned encounters. Include canceled also but only the ones which are older than the current encounter's DateTime.
 * Load applicable rules database identifier and send it to the node server. Database identifier to be used is UUID and primary key.
 * Receives rule execution errors from rule server and saves it to the database.

**Avni Rule Server (node)**
 * Maps the data received from Avni server to the data model expected by the rules.
 * Loads rule code from the database based on database identifiers provided.
 * Executes the rule (of course)
 * Puts together rule errors and sends back to the Avni server in the response.

*We tried using Rhino but there was too much mapping code which is not easy to understand. The rules access objects using something like programEncounter.enrolment.observations - implementing which in Rhino is quite difficult. We also noticed that the performance was quite poor. The date values were coming out different. And we were only scratching the surface with a few simple rules.*