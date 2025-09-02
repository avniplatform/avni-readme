---
title: Contribution guide
excerpt: A guide to anyone who contributes to the Avni codebase
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
These are some principles we try to follow within the Avni codebase. Some of it is just strong beliefs, and some are good practices. Either way, it will be good to follow them for a hassle-free contribution experience. 

### About the code you write
- Styling is best done by the machine. Some repositories have pre-commit hooks that take care of styling, but if there is none, keep a sane style. Don't fuss over it. 
- Keep your code [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). 
- Reuse patterns, conventions and abstractions already available in the codebase as much as possible. 
- Write unit tests for non-trivial business logic. 
- Avoid view tests. They are brittle and don't help much anyway. 

PS: Remember that you might find counter-examples for these principles within the Avni code. We are humans too. If you are up to it, fix them. 

### Raising pull requests
- Ensure you create an issue in the relevant repository in Github. 
- Fork the relevant repository, create a branch and work on it. 
- Keep smaller commits, it makes review easier
- If you are taking too long to work on an issue, keep rebasing from upstream to avoid nasty merge conflicts. Pull requests that cannot be automatically merged might not be picked up. 


### What happens when you submit a pull request
When a pull request is submitted, one of the core developers will pick it up and review them. Reviews must ensure that
- Code does not break the system
- Code is DRY, and contains relevant unit/integration tests
- All tests pass


Once the code is merged, the core developer will move the card status to the appropriate status for testing. Status of your cards will be available either on Github, or on Zenhub (all core developers right now use Zenhub to track issues). 

A QA will pick up the issue for testing. If it does not work, a bug card is raised, which will then be resolved.


### Who are these core developers? 

Core developers have direct commit access to the avni codebase. 

If you work on Avni for some time, when it becomes hard for core developers to keep up with the volume of work that you produce, and we like your code, you will automatically be added as a core developer. 

### What if I have a question? 

We are at [Gitter](https://gitter.im/OpenCHS/openchs) if you want to chat. Email at avni-project@googlegroups.com.