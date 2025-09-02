---
title: Sync Gotchas
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
1. When the strategy is direct assignment, the last modified date time kept in entity sync status is that of transaction entity like (Subject, Program Enrolment etc) but the date time against which the check is done on the server to find new entities is of User Subject Assignment. This can cause behaviour which may seem puzzling.
   1. Avni client will keep downloading same entities over and over - as the assignment is always done after the creation on individual
2. If user has reset sync set due to location changes to one's catchment and the user ignores it and gets a sync error. After this if the user reports a ticket one will not be able to figure whether user ignored the reset sync prompt.
3. If last modified date time of an entity is in future (due to wrong manual update) then that entity will not be synced. A dependent entity may have last modified date time before "now" - will sync and cause sync error.
4. Sync requires that a program is not used in multiple subject types, encounter type is not used in multiple subject types or programs. This is not enforced by the platform yet. When such mapping exists the sync will fail. The failure happens because when finding the subject type for program enrolment sync it can find a subject type which has different sync strategy like location, sync attributes, etc than one applied for subject sync. You can run the following SQL to find such anomalies. **If these programs are using sync strategy which varies between different subject types then there could be sync problem.**

   ```sql
   -- program used in multiple subject types
   select organisation.name, program.name, form.form_type, count(*)
   from form_mapping
            join subject_type on form_mapping.subject_type_id = subject_type.id
            join program on form_mapping.entity_id = program.id
            join form on form_mapping.form_id = form.id
            join organisation on form.organisation_id = organisation.id
   where form_mapping.is_voided = false
     and observations_type_entity_id is null
   group by 1, 2, 3
   having count(*) > 1;

   -- general encounter types used in multiple subject types
   select organisation.name, encounter_type.name, form.form_type, count(*)
   from form_mapping
            join subject_type on form_mapping.subject_type_id = subject_type.id
            join program on form_mapping.entity_id = program.id
            join form on form_mapping.form_id = form.id
            join encounter_type on encounter_type.id = form_mapping.observations_type_entity_id
            join organisation on encounter_type.organisation_id = organisation.id
   where form_mapping.is_voided = false
     and observations_type_entity_id is not null
     and encounter_type.is_voided = false
    and entity_id is null
   group by 1, 2, 3
   having count(*) > 1;

   -- encounter types used in multiple programs
   select organisation.name, encounter_type.name, form.form_type, count(*)
   from form_mapping
            join subject_type on form_mapping.subject_type_id = subject_type.id
            join program on form_mapping.entity_id = program.id
            join form on form_mapping.form_id = form.id
            join encounter_type on encounter_type.id = form_mapping.observations_type_entity_id
            join organisation on encounter_type.organisation_id = organisation.id
   where form_mapping.is_voided = false
     and observations_type_entity_id is not null
     and encounter_type.is_voided = false
   group by 1, 2, 3
   having count(*) > 1;
   ```
