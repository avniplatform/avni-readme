---
title: Repeatable question group
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
A repeatable question group is an extension of the question group form element. A Question group is like any other data type in Avni. The only difference is it allows implementers to group similar fields together and show those questions like a group. Now there are cases where you want to repeat the same set of questions(group) multiple times. This can be easily done by just marking the question group as repeatable.

## Steps to configure repeatable Question group

1. Create a form element having a question group concept.
2. This will allow you to add multiple questions inside the question group.
3. Once all the questions are added, mark it repeatable and finally save the form.

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/ae26aab-Repeaable-question-group.png",
        "Repeaable-question-group.png",
        1495
      ],
      "align": "center",
      "caption": "Notice how the question group is marked repeatable."
    }
  ]
}
[/block]


[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/61bee14-repeatable-question.gif",
        "repeatable-question.gif",
        585
      ],
      "align": "center",
      "caption": "Repeatable questions in mobile app"
    }
  ]
}
[/block]


### Limitations

At this time, the following elements that are part of the forms are not yet supported. 

- Nested Groups
- Encounter form element
- Id form element
- Subject form element with the "Show all members" option (Regular subject form elements are supported)

  - To get this working within a Question-Group/ Repeatable-Question-Group, for a Non "Group" Subject Type, please select the **"Search Option"** in the Subject FormElement while configuring the Form inside **App Designer**

  [block:image]{"images":[{"image":["https://files.readme.io/c5c15ae-Screenshot_2024-06-10_at_2.35.04_PM.png","",""],"align":"center"}]}[/block]