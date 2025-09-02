---
title: Integration Service Building Blocks
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
Avni Integration Service follows [domain driven design building blocks](https://medium.com/@mazraara/the-building-blocks-of-domain-driven-design-ddd-af745a53a9eb). Here the responsibility of these building blocks is elaborated.

### Repository

Each integration uses three data sources typically integrating system, Avni and Integration Database. The first two are accessed via REST/HTTP API. But for all these three data access use Repository classes to pull/push data.

### Entity (and Factory, Value Object)

The data coming in-out of repositories are entities (like usual). The responsibility of Value objects and Factories are  as usual.

### Service

Services provide business and data transformation logic. They can use **Mapper**.

## Integration Service has two other building blocks that are not common

### Worker

Workers are like controllers that orchestrate one or more services to process each integration flow. Typically there is one worker method for one integration process.

### Job

These handle the responsibility of scheduling of the [Integration process](doc:integration-process). They invoke workers for the processing.