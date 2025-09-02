---
title: Understanding WhatsApp Integration Tables
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
## Description of reflective fields

**Message Rule**

- entityType - see [EntityType](https://github.com/avniproject/avni-server/blob/master/avni-server-api/src/main/java/org/avni/messaging/domain/EntityType.java) enum
- entityTypeId (Avni ID of the entity type like SubjectType, Program, etc)

(From reporting/querying standpoint, these fields provide information about meta entity)

**Message Receiver**

- receiverType (User, Subject, Group)
- receiverId (Avni ID for User and Subject. never populated for Group)
- externalId (for User and Subject this field gets populated when the message is first sent to the external system. for Group this field is always present)

**Message Request (Queue)**

- entityId (Subject ID, Program Enrolment ID, Program Encounter ID, Encounter ID)

### To understand the status of automatic messages

These messages are triggered when an entity like Individual, Enrolment, or Encounter, is saved.

```sql
select o.name, mr.receiver_type, mr.receiver_id, mrq.scheduled_date_time, mrq.delivered_date_time, mrq.delivery_status
from message_request_queue mrq
         join message_receiver mr on mrq.message_receiver_id = mr.id
         join organisation o on mr.organisation_id = o.id
where mrq.is_voided = false
  and mr.is_voided = false and o.is_voided = false
order by o.name, scheduled_date_time desc;
```



### To understand the status of manually triggered messages

```sql
select o.name,
       mr.receiver_type,
       case
           when mr.receiver_id is null then 'External id is used'
           else mr.receiver_id::text
           end
           as receiver,
       mr.external_id, mrq.scheduled_date_time, mrq.delivered_date_time, mrq.delivery_status, mbm.parameters
    from message_request_queue mrq
             join manual_broadcast_message mbm on mbm.id = mrq.manual_broadcast_message_id
             join message_receiver mr on mrq.message_receiver_id = mr.id
             join organisation o on mrq.organisation_id = o.id
    where mrq.is_voided = false
      and mbm.is_voided = false and o.is_voided = false;
```