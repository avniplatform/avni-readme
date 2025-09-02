---
title: Timed questions
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
Questions can be timed in Avni. If you want the user to fill some set of questions after a particular time then you can mark the page as a timed page and specify the time when the questions on the page should be visible. You also set the stay time which forces user to fill all those questions in the mentioned time frame.

## Steps to configure timed questions

Any page can be marked as timed. It is important that you specify the start time and stay time for the timed pages. The start time indicates that the page should start at the provided time and the stay time will keep the question visible till the specified time. Once the stay time is over screen automatically moves to the next page.

<Image title="timed_page.png" alt={1809} src="https://files.readme.io/cf86f77-timed_page.png">
  Example of the timed page in the form
</Image>

There are some assumptions that must be followed to make timed questions work properly.

1. Questions inside the timed page should not be mandatory.
2. If any page is marked as timed then it should not have any visibility rule to hide the entire page. The visibility rule might get ignored.
3. Timer only runs for the first time when filling up the form. Once users have filled in all the timed questions they can go back and edit the entries. Also, the edit flow does not show any timer for the timed questions.
4. If multiple pages are timed and are placed one after the other then the same timer is used for all the pages. Only when there is at least one non-timed page in between two timed page app asks the user to start the timer again.
