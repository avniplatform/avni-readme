---
title: Performance expectations
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
In the table below different performance items have been listed with the rough expectations of how long they should take. If during your testing you see response times not inline with the following table, please get it verified by the platform team or technical leads in your team, if indeed the response time is OK.

### Implementation

[block:parameters]
{
  "data": {
    "h-0": "Performance Item",
    "h-1": "General Expectation",
    "h-2": "Raise red flag",
    "0-0": "**SuperSet/Metabase Dashboard**  \n(with or without filters)",
    "0-1": "\\< 10 seconds",
    "0-2": "> 20 seconds",
    "1-0": "**SuperSet/Metabase Line list download**",
    "1-1": "\\< 60 seconds",
    "1-2": "> 3 minutes",
    "2-0": "**Offline mobile dashboard**  \n(with or without filter values,  \nfor any catchment size; on any device)",
    "2-1": "\\<= 2 seconds",
    "2-2": "> 5 seconds",
    "3-0": "**Summary and Recommendations  \nScreen** (mobile app; on any device)",
    "3-1": "\\<= 2 seconds",
    "3-2": "> 5 seconds"
  },
  "cols": 3,
  "rows": 4,
  "align": [
    "left",
    "left",
    "left"
  ]
}
[/block]


### Platform

These are platform issues, but may have been caused by some specific configuration of the organisation, hence may not be a known issue. So please feel free to report them.

[block:parameters]
{
  "data": {
    "h-0": "Performance Item",
    "h-1": "General Expectation",
    "h-2": "Raise red flag",
    "0-0": "**Incremental sync**  \n(on wifi network; on any device)",
    "0-1": "\\< 20 seconds",
    "0-2": "> 1 minute",
    "1-0": "**Subject search** (mobile app; on any device)",
    "1-1": "\\<= 3 seconds",
    "1-2": "> 5 seconds",
    "2-0": "**DEA subject search** (after release 10)  \nwith/without filters",
    "2-1": "\\<= 5 seconds",
    "2-2": "> 10 seconds",
    "3-0": "**All admin / app designer screens**  \n(Except CSV, bundle uploads)",
    "3-1": "\\<= 5 seconds",
    "3-2": "> 10 seconds"
  },
  "cols": 3,
  "rows": 4,
  "align": [
    "left",
    "left",
    "left"
  ]
}
[/block]