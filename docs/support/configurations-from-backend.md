---
title: Configurations from backend
excerpt: Use full queries that can be configured only via backend.
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
# How to make the "Total" default report card always appear.

Run the below script

```pgsql
set role <org_name>;

update organisation_config
set settings = settings || '{"hideTotalForProgram": false}' ::jsonb,
    last_modified_date_time = now()
where organisation_id = <org_id>;
```
