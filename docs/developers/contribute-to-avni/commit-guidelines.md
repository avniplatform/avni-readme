---
title: Commit Guidelines
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
- Every commit needs an issue on Github
- There are times where an issue is not very important (minor dev only fixes such as Makefile changes etc). These will be marked as issue number 0. 
- Commit format is `#<issueNumber> | <commitMessage>`. If the issue is in a different repository for some reason, then it is marked appropriately with a message like this - `avniproject/<repo>#issueNumber | <commitMessage>`. eg: `#123 | Fix missing semicolon`, `avniproject/avni-client#123 | Fix missing semicolon`
- Make the first line of the commit concise. If you need to add more context, add a newline and put details there
- Have atomic commits that fix one issue at a time. If there is a lot of formatting changes, then make that a separate commit so it is easy to identify the core change