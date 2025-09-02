---
title: Avni Development Process
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
The Development process is slightly different between Core Developers and External Contributors. 

Core Developers are those who work on Avni full-time. They work as a team towards goals stated on the [roadmap](https://github.com/orgs/avniproject/projects/2/views/7) and have full commit access to the repositories. They also work towards predefined deadlines. 

External Contributors usually work on items that they are interested in fixing. There are no timeline expectations for external contributors, but there are code quality requirements. The process for both these groups are mentioned below. 

### Core Developers

- Cards are maintained on the [Project](https://github.com/orgs/avniproject/projects/2) wall
- Developers pick up work from the "Ready" lane for the current release (or past releases if there are any bugs)
- For non-bugs, an estimate is required on the card. This helps us understand a crude average team velocity and helps us plan releases better
- Ensure that commits go to the release branch marked on the card. See [Release Naming and Branching](branching-strategy)section to see more details on the branching strategy
- Follow the Coding Standards and the [Commit Guidelines](commit-guidelines)
- Cards are moved to the "In Progress" lane and assigned to self when development is underway. 
- Once development is complete, ensure CI works, deploy to the correct environment (if not auto deployed) and move the card to "Code Review Ready"
- Current release deployments happen on the staging environment, while minor releases or patch releases are deployed on prerelease before release to production
- Code Review - Any developer who is free picks up cards in "Code Review Ready". Comments that need to be fixed are mentioned on the card and the card moved to "Code Review w/comments". Comments that do not require stopping of card movement are mentioned in the commit as comments. If everything is fine, they are moved to "QA Ready". Any developer can pick up cards to review. The only condition is that they should not have worked on the card
- QAs pick up cards from the QA Ready lane to "In QA". Failed cards go to "QA Failed" from where developers can pick up and address issues. Otherwise they are moved to "Done"
- Around once a month, when the priorities of the release are complete, code is branched out and a release candidate is built. This goes to the "prerelease environment" 
- A bug-bash is conducted where everyone is invited and can try out the new release
- Conducting the bug bash is a signal for the development and implementation teams that the release is complete, and only release regressions will be taken care for this release
- Avni implementers look at the release and test out a few implementations to certify it ready to deploy to the Avni cloud.
- Any bugs found during the bug bash are addressed before the release is finally performed on production. Release process is mentioned [here](doc:pre-release-testing). 

### External Contributors

- Ensure there is an issue in the right repository that describes your issue. If there is none, then create one

![](https://files.readme.io/9acb2d0-image.png)

- Try to avoid picking up cards that are marked for an active release. Core Contributors already have this as their focus, we do not want to duplicate work. You can look at the [roadmap](https://github.com/orgs/avniproject/projects/2/views/7) to identify the current active release(s). The project release will be marked on the card
- Clone the repository that you want to work on
- Follow the General Coding and [Commit guidelines](commit-guidelines)
- Raise a pull request to the main branch once you are done with changes. This will be either the `master` or `main` branch depending on the repository
- A core contributor will review changes and merge them to the codebase. If there are any review comments, work on them and raise the pull request again once you are done
- Once the merge is complete, the issue is marked for the current release and released. If you wish this to be backported to an earlier release, mention this either in the PR or on the issue. Backporting happens to only one major release backwards from the current release at this point.