---
title: When to use Translations
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
Since the issue of change in concept name has come up a few times - in terms of what impact it would have on rules. Also, we use uuid for concept as the name can change - but this reduces readbility of the rule.\
Here is a mental model to think about this.

Concept name, form element name should be considered programming keywords - representing an idea.\
e.g. Mother's name\
When we name a concept/element in app designer we are defining a name for it in the programming realm not in the user realm.

How this idea is presented to user is using an English translation or another language translation.\
What does English translation represent?\
It represents a mapping from the idea to a view for the user.

So two realms - programming and user.\
What is the benefit of this?

As long the the core idea doesn't change, there is no need to change the name of the concept/element.\
e.g. if the customer says we want to call it - Name of mother - then it is a change only in the user's realm not programming realm.

So we can simply change/add a translation for it based on the user/customer's preference. The translation feature offers a decoupling between programming and user realm.

Following from this, there is no need to use UUID of the concept in the rule. Why? Because once the concept/element name is defined, there is no need to change it based on what user/customer wants it to be named.

Concept/element should be renamed only if there is a semantic change in the idea behind it - this happens very rarely.

If there a typo in the name, then you can change it, but remember there is cost to it - which may or may not be worth paying - depending on how deep you have been in the cycle of the project.\
Avni has been designed such that almost everything can be shown to the user in their own language.\
But what also follows from this is - everything is also defined separately in the programmer and user realms.
