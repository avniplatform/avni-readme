---
title: Reporting issues
excerpt: How to report a new issue
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
All work in Avni happens through Github issues, so if you discover a problem in Avni, please add an issue in the relevant repository. 

### Points to note when raising issues

1. If you know the repository the bug needs to be raised, please raise in that repository. Here is a list of repositories and the link to the issues section

| Application                               | Issue Link                                                                              |
| :---------------------------------------- | :-------------------------------------------------------------------------------------- |
| Avni Server                               | [github.com/avniproject/avni-server/issues](github.com/avniproject/avni-server/issues)  |
| ETL                                       | [github.com/avniproject/avni-etl/issues](github.com/avniproject/avni-server/issues)     |
| Media Viewer                              | [github.com/avniproject/avni-media/issues](github.com/avniproject/avni-server/issues)   |
| Web Application (other than Media Viewer) | [github.com/avniproject/avni-webapp/issues](github.com/avniproject/avni-server/issues)  |
| Android field app                         | [github.com/avniproject/avni-client/issues](github.com/avniproject/avni-server/issues)  |
| Multiple/not sure                         | [github.com/avniproject/avni-product/issues](github.com/avniproject/avni-server/issues) |



2. If the issue affects an end-user of the application, mark the label "user-reported". 
3. If you believe the issue is a bug, then add the label "bug"
4. When reporting a product issue, try to help the developer understand how the issue can be replicated with minimum effort. For example, create a new organisation where you can isolate the issue to its bare minimum. Alternatively, provide steps to reproduce the issue in a clear manner. This helps save a lot of developer time, and cuts down the number of communication hops to understand the issue. 



Once issues are reported, they get triaged and assigned a release. The list of releases and their expected go-live dates are available on the [Roadmap](https://github.com/orgs/avniproject/projects/2/views/7). The [Avni Development Process]\(Avni Development Process)provides a peek into how issues get sorted and move through to be fixed in the product.