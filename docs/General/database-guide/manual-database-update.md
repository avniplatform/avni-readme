---
title: Manual Database Update
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
> 🚧 Manually updating database to fix visit schedules is not recommended. See section below for why?

Updating production database directly must be the last resort. Using support API, bulk uploads are better option. But if you are unlucky enough that you have to do database update directly then...

- For everyone's sanity the SQL should be present in <https://github.com/avniproject/data-fixes> repository, so that we can checkout only one repo for all implementations to review.

- Ensure that the database updates SQL is checked in another environment and end to end testing has been done after the update.
  - For getting a dump of a specific organisation please follow the tunnel_, dump_, restore\* and create-local-db-impl-user targets from here - <https://github.com/avniproject/avni-server/blob/master/makefiles/externalDB.mk>

- Ensure that code review has been done for the SQLs.

- For transaction data last modified date time should be modified. Increment like the following (any data reported by database like number of rows updated can be put into comment when the queries are run).

- ```sql
  update individual set 
  	last_modified_date_time = current_timestamp + (random() * 5000 * (interval '1 millisecond'))
  where foo = bar;
  ```
  <br />

- If the number of rows updated exceed 10000 records then we should ideally go for downtime.

- In transaction tables a manual update history is maintained for troubleshooting. This column should not be updated directly rather use the  
  _append_manual_update_history_  
   function to update. This function maintain the previous history records and appends the new value, maintaining date of update.

- ```sql
  update program_enrolment 
  	set manual_update_history = append_manual_update_history(program_enrolment.manual_update_history, 'avni-server#676')
   where foo = bar;
  ```
  <br />

- Use data-fixes Github project for SQL scripts.

- If ETL is enabled for the org for which the updates are being run, disable it before executing the updates and re-enable it after the updates are committed to avoid issues with the ETL tables not being updated.

- In the SQL script follow the following pattern. 

- Avoid using specific ids in these SQLs as they may lead to errors - as the records may get updated between writing and final run. Better to include select in the update and other queries.

- **When running in production:**

  - **always run sql script within manual transaction**
  - **verify counts**
  - **only if changed count is as expected, do the commit**

- ```sql
  set role org;

  begin transaction; -- so that you can rollback if something is goes wrong

  -- Before queries
  select count(*) from individual where observations->>'dfds' > 2; -- example. enter the data that you see here in test environment in comment

  -- DML statements
  -- insert/update etc

  -- After queries
  select count(*) from individual where observations->>'dfds' > 2; -- example. enter the data that you see here in test environment in comment


  -- rollback;
  commit;
  ```

  <br />

- If your SQL script is likely to update a lot of rows in the database and the users may also be updating the data at the same time then the data update should be done in production by bringing down the server first. Note that this approach is not valid for API based update. How to handle the concurrent update, when updating via API - has to be figured out.
  - For smaller number of rows but with concurrency `Select for update` or higher transaction isolation level can be used.

## Fixing visit schedule

Visit/encounter schedules work like state machine. e.g. cancelling one encounter schedules another encounter; performing enrolment is schedules another encounter. This behaviour is unlike other data behaviour in Avni - requiring us to look at how we should manage this data distinctly from other transaction and non-transaction data. For example - updating an observation value mostly has no impact on another data point. Sometimes updating weight requires updating the calculated value as well - but scope is often simple to understand and limited. But cancelling a visit from backend means also determining what else should happen as a side-effect of that change.

There are major risks in "fixing" visit schedules by fixing the data, as it is quite complex to do it right and quite complex to test and verify. It is difficult to know when and whether you are done. **Hence this is not prudent strategy.**

### How should we solve for this?

The core idea is:

- Empower users to manage the wrongly scheduled visits and be able to do the right visits.
- Improve scheduling and encounter eligibility rules to cover new scenarios that are encountered. If required write unit tests to verify.
- Provide offline reports if required to let users figure the cohort of subjects that have problematic state.
- Fixing visit schedules via data fix is not a cost effective approach as well.