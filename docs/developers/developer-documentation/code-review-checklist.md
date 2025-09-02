---
title: Code Review Checklist
excerpt: ''
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Code Review Checklist(For the Reviewer and Developer both)

This a code review checklist. The reviewer should go through this. But a developer creating the PR should also go through this to avoid obvious things and save the reviewer's time.

### Code Formatting

Reviewer mostly need not worry about code styling since we do automatic code formatting using Prettier. We also use ESLint which shows warnings for code that violate ESLint rules. Make sure that there are no open ESLint warnings.

### Functionality

Does this code implement the functionality correctly? Do you see any missed out cases?  
You can validate the behavior by merging it on your machine. When it's hard to understand the feature by yourself, have the developer give a demo to you.

### PR Size

 Is the pull request too big to review quickly? We prefer small PRs because of multiple reasons like early issue detection, ease of review, etc. Talk to the developer to make sure that they raise small PRs. The way to raise small PRs for them is to not wait for the full feature to complete. If the feature is not complete and they must hide incomplete functionality they can use feature toggles to hide the feature from users. 

### Readability

- Can you as a reviewer clearly understand what the code is doing by yourself?
- Naming
  - Name the system entities by what they are + the intent
    - E.g. name a ProgramEnrolment as programEnrolment or exitedProgramEnrolment
  - Name the array or list as a plural
    - E.g. Array or a list of program enrolments should be called programEnrolments
      - An array of encounters should just be called encounters
  - Name the primitive variable by the role they play
    - E.g. counter, states, state, visitStatus

### Maintainability

Does the code keep maintainability in mind?  
Is the code too complex? If you find it too complex it might be hard for other developers too.  
Few things to consider:

- Loose coupling
- Abstraction
- Polymorphism

### Reusability

Is code easily reusable if it's adding some functionality that looks like something common? Is there any duplication that can be extracted into a class, function, or component? Also, check if code is doing over-abstraction and making code hard to understand. Consider the Rule of Three \[[1](https://en.wikipedia.org/wiki/Rule_of_three_%28computer_programming%29)] \[[2](https://softwareengineering.stackexchange.com/questions/197363/reasoning-to-wait-until-third-time-in-the-rule-of-three)].

### Reliability

The code should be handling all scenarios to protect the user from a bad experience.  
Is it handling all the edge scenarios? Is it handling scenarios when API can return an error?

### Framework specific best practices

We use React and Redux so see that the code follows specific best practices:

- Usage of local state vs redux. 
  - Use the local state when you don't need to use the state in other components. See [Redux vs Local state](https://redux.js.org/faq/organizing-state#do-i-have-to-put-all-my-state-into-redux-should-i-ever-use-reacts-setstate).
- Breaking down react components
  - See [Thinking in React](https://reactjs.org/docs/thinking-in-react.html#step-1-break-the-ui-into-a-component-hierarchy)
- Naming redux actions
  - Name it so it conveys the intention of the action
    - E.g. SAVE_FORM, VALIDATE_FORM, EXIT_ENROLMENT

### Zoom out

- Are there problems at the architecture level? Is it inefficient, or brittle, or poorly architected? It might feel like it's too late at the code review stage as developers may have worked on it for many days. But it's important to not merge such PRs. It raises a point of small PRs so things can be caught early on.  
  Maybe there are larger issues like lack of training/skills, so it's better to flag off these kinds of things with appropriate people to reduce overall project risk early on.