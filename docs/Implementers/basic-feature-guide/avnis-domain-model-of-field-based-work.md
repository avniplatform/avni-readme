---
title: Avni's domain model of field based work
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: >-
    Understand how some of the key system data flow happens in Avni and hence
    resulting in a working application specific to your
    organisation/implementation.
  pages:
    - type: basic
      slug: key-system-and-data-flows
      title: Key system and data flows concepts
---
To understand how Avni works lets first understand the domain model of field-based work. Any field-based work can be broadly subdivided into three components.

1.  **Service delivery organisation** - The organisation, with providers and the geographical area where they work.
2.  **Services (or schema of data to be collected)** - The actual set of services provided by the above organisation to the people (or data to be collected about something in a said geographical area).
3.  **Service encounter** - Each service is broken down into a discrete set of type of encounters that providers of the organisation have with the people.

Now lets further explore each one of the above one by one and how Avni models it into the software system.

# 1.  The architecture of the service delivery organisation
Avni allows for modelling of the work geography of the organisation and mapping of service providers to their geographical units. In avni, one can set up the complete geographical area into multiple levels and locations at the same level.

Lets first identify the key domain concepts with their names. A service delivery organisation consists of the following:
  * An **organisation** (e.g. NGO, or government department, university), the entity that provides the service or collects some data.
  * In order to provide this service or collect data, this organisation employs, hires or engages service providers. They can be called field workers, frontline worker, health worker, etc - we will simply call them **provider or user**.
  * The service provided by the organisation via the providers is received by *beneficiaries, citizens, patients, students, children* so on. In the case where the work is data collection, the provider may be additionally or only collecting data for non-living objects like *water body, school, health centre*, etc. Since Avni is a generic platform, let's call of them by a rather imaginative name **subject**.
  * In most Avni use cases, the subjects may be spread across a geographical area such that one provider cannot service (or collect data from) all subjects. Hence each provider is assigned a specific area that is called **catchment** in Avni. A catchment could be a block, a cluster of slums, etc.
  * Each catchment may have one or more geographical units which are called **location**s in Avni. A location could a village, slum, subcenter area so on.

Each user **must** to be associated with at least one catchment.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/4343bff-Screenshot_2019-11-15_at_5.17.05_PM.png",
        "Screenshot 2019-11-15 at 5.17.05 PM.png",
        1918,
        952,
        "#d1e3e6"
      ]
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/514028d-Screenshot_2020-11-16_at_11.50.38_AM.png",
        "Screenshot 2020-11-16 at 11.50.38 AM.png",
        2372,
        752,
        "#d3dcdd"
      ],
      "caption": "An example of service delivery organisation"
    }
  ]
}
[/block]
In Avni system, the organisation, provider, catchment and location are setup via web application by the implementer, IT or program administrator. When a subject is created/registered in the system, they are assigned a location. This is usually done by the field user using their mobile device

# 2.  Modelling the services provided into Avni
Avni allows for modelling of the services provided (or data collected) via a three-level configurable data structure. Avni allows for setting up subjects, enrolment of subjects in programs, and defining encounters for enrolments/subjects. There can be multiple programs per subject type and multiple encounter types per program.
  * An organisation may have one or more **subject types** to which they provide service (or collect data for).
  * To each subject type, the organisation may be providing one or more service types (or have the purpose of data collection). In Avni, each service type is called a **program**.
  * Each service type may involve one or more types of interactions which are called **encounter type**s. Avni also allows one to avoid the service type completely and define encounter types directly for the subject types - allowing for modelling of interactions which are not required to be grouped under services.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/b63d3c9-Screenshot_2019-11-15_at_5.26.15_PM.png",
        "Screenshot 2019-11-15 at 5.26.15 PM.png",
        2084,
        948,
        "#cde0e4"
      ]
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/93a551a-Screenshot_2019-11-15_at_5.27.48_PM.png",
        "Screenshot 2019-11-15 at 5.27.48 PM.png",
        1942,
        830,
        "#cfe2e6"
      ]
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/3ca82d4-Screenshot_2020-09-23_at_6.00.45_PM.png",
        "Screenshot 2020-09-23 at 6.00.45 PM.png",
        1906,
        754,
        "#cde1e6"
      ]
    }
  ]
}
[/block]
# 3. Groups and relationships
Avni allows for creating groups of subjects and more specifically a special type of group called household or family whereby another subject types (created to represent people) can be members of the household. These members can also be linked to each other via relationships. Do note though that in Avni we have modelled group and households via attributes on subject types. The subjects of such types can be linked as child elements of the parent subject. Please the diagrams below. Avni application behaves differently for groups and households.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/a5fd36e-Screenshot_2020-04-28_at_11.20.04_AM.png",
        "Screenshot 2020-04-28 at 11.20.04 AM.png",
        2374,
        882,
        "#d2e4e7"
      ],
      "caption": "Group also can behave like subjects also, along with being a group of subjects."
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/740185f-Screenshot_2020-04-28_at_11.16.09_AM.png",
        "Screenshot 2020-04-28 at 11.16.09 AM.png",
        2750,
        984,
        "#d9e0d9"
      ],
      "caption": "Household is a special type of group, which has persons as members. The persons can be related to each other via human relationship types."
    }
  ]
}
[/block]
# 4.  Design of a service encounter
Service encounter is an encapsulation of a type of interaction between the service provider and the beneficiary - as explained above. Each service encounter consists of the following:
  * observation made by the service provider (field workers)
  * answer is given by the beneficiary for a question asked by the provider
  * information/suggestion/recommendation made by provider
  * computed or preset information provided by the avni app to the provider
  * photos/videos taken by the provider

Avni allows you to arrange these sequentially and including based on conditions set by you. It also allows to schedule next service encounters based on a rule set by you. This is modelled using form, rules and content. Each service encounter can be defined in this way.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/d7f0b31-Screenshot_2019-11-15_at_5.30.31_PM.png",
        "Screenshot 2019-11-15 at 5.30.31 PM.png",
        2040,
        1178,
        "#cbdcdd"
      ],
      "caption": "Anatomy of an encounter type (or a subject registration form)"
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/5fdb3eb-Screenshot_2019-11-15_at_1.53.16_PM.png",
        "Screenshot 2019-11-15 at 1.53.16 PM.png",
        1814,
        1006,
        "#e2e4e6"
      ],
      "caption": "Example of a few form element groups.",
      "border": true
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/2c87d92-Screenshot_2019-11-15_at_1.55.34_PM.png",
        "Screenshot 2019-11-15 at 1.55.34 PM.png",
        2180,
        1442,
        "#f3f4f5"
      ],
      "caption": "Example of form elements within a form element group.",
      "border": true
    }
  ]
}
[/block]