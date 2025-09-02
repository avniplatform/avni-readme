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

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th style={{ textAlign: "left" }}>
        Performance Item
      </th>

      <th style={{ textAlign: "left" }}>
        General Expectation
      </th>

      <th style={{ textAlign: "left" }}>
        Raise red flag
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td style={{ textAlign: "left" }}>
        **SuperSet/Metabase Dashboard**
        (with or without filters)
      </td>

      <td style={{ textAlign: "left" }}>
        \< 10 seconds
      </td>

      <td style={{ textAlign: "left" }}>
        > 20 seconds
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        **SuperSet/Metabase Line list download**
      </td>

      <td style={{ textAlign: "left" }}>
        \< 60 seconds
      </td>

      <td style={{ textAlign: "left" }}>
        > 3 minutes
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        **Offline mobile dashboard**\
        (with or without filter values,\
        for any catchment size; on any device)
      </td>

      <td style={{ textAlign: "left" }}>
        \<= 2 seconds
      </td>

      <td style={{ textAlign: "left" }}>
        > 5 seconds
      </td>
    </tr>

    <tr>
      <td style={{ textAlign: "left" }}>
        * \*Summary and Recommendations\
          Screen\*\* (mobile app; on any device)
      </td>

      <td style={{ textAlign: "left" }}>
        \<= 2 seconds
      </td>

      <td style={{ textAlign: "left" }}>
        > 5 seconds
      </td>
    </tr>
  </tbody>
</Table>

### Platform

These are platform issues, but may have been caused by some specific configuration of the organisation, hence may not be a known issue. So please feel free to report them.

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        Performance Item
      </th>

      <th>
        General Expectation
      </th>

      <th>
        Raise red flag
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        **Incremental sync**
        (on wifi network; on any device)
      </td>

      <td>
        \< 20 seconds
      </td>

      <td>
        > 1 minute
      </td>
    </tr>

    <tr>
      <td>
        * \*Subject search\*\* (mobile app; on any device)
      </td>

      <td>
        \<= 3 seconds
      </td>

      <td>
        > 5 seconds
      </td>
    </tr>

    <tr>
      <td>
        * \*DEA subject search\*\* (after release 10)\
          with/without filters
      </td>

      <td>
        \<= 5 seconds
      </td>

      <td>
        > 10 seconds
      </td>
    </tr>

    <tr>
      <td>
        **All admin / app designer screens**\
        (Except CSV, bundle uploads)
      </td>

      <td>
        \<= 5 seconds
      </td>

      <td>
        > 10 seconds
      </td>
    </tr>
  </tbody>
</Table>
