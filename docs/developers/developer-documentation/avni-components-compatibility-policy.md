---
title: Component compatibility policy
excerpt: Compatibility between the different components of Avni
deprecated: false
hidden: false
metadata:
  title: ''
  description: Internal compatibility between the different Avni components
  robots: index
next:
  description: ''
---
Avni is a standalone application and is not meant to be used as a library within another piece of software. Therefore, the forwards and backwards compatibility requirements are slightly lower than a software library. However there are deployment considerations that necessitate some level of backwards compatibility. 

This document provides the reasons, and the actual compatibility policy that Avni adheres to. 

### What are the ideas behind backwards compatibility?

The field-app is hosted on Google Play, and therefore we expect users to stay on older versions (sometimes upto 6 month old versions). The server, therefore has to be able to service these apps correctly. 

avni-webapp, however, is completely within our control and therefore does not need any kind of forwards or backwards compatibility. Every released version of avni-webapp is guaranteed to work only with the same released version of avni-server. 

The hosted version of Avni has reports written since 2017, and modifying these reports are extremely hard. Therefore we ensure that there are no changes that will break backwards compatibility at the database level. 

All other applications such as avni-etl, avni-integration, avni-media etc are relatively standalone, and will be released along with a major release if there are compatibility problems. If not, then the last available release of these components will be compatible with any newer release of Avni. 

### How is backwards compatibility achieved in Avni?

All APIs in avni-server that are used by the Android client are append-only. Newer attributes and endpoints can be added to the API, but older ones will be kept intact for at least 6 months. POST requests can take in additional fields, but will always have defaults. If default values are not possible, then a new endpoint is created instead. 

External APIs are handled similar to the Android client. 

Web APIs (those that are used by the web applications of Avni) are compatible to the same released version of avni-server. There are no backward or forward compatibility requirements on these. 

The Avni database will never break a select query that has ever been written before. This is achieved by append-only columns/tables and usage of triggers for older tables/columns so that these are preserved.
