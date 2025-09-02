---
title: Custom Query API
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
Avni allows you to save the implementation-related queries in the `custom_query` table. 

### Points to note:

* Right now, there is no UI, and the implementer needs to insert the query manually into the DB.
* The query gets passed following additional parameters used to set Organisation context, which can be made use of, as per implementation team's requirement in the custom query. 
  * ORG\_ID =>"org\_id"
  * ORG\_DB\_USER => "org\_db\_user"
  * ORG\_SCHEMA\_NAME => "org\_schema\_name"
* Please note that the custom query is executed within the current organisation's db\_user's role context.. therefore, data access would be limited to only that within its organisation.

### Sample insert statement

```sql Adding new custom query
insert into custom_query (uuid, name, query, organisation_id, is_voided, version, created_by_id,
                          last_modified_by_id, created_date_time, last_modified_date_time)

values (uuid_generate_v4(),
        'Individual based on gender',
        'select concat(first_name, '' '', last_name) as "Full name",
               g.name as "Gender"
        from individual
                 join gender g on individual.gender_id = g.id
                 join organisation o on o.id = i.organisation_id
        where g.name = :gender
        and o.id = :org_id
        and registration_date = cast(:date as date)
        limit 100;',
        21,
        false,
        0,
        1,
        1,
        now(),
        now());
```

Once the query is saved in the DB one can fire the post request to `/executeQuery` endpoint with the request body

```json Sample request body
{
    "name": "Individual based on gender",
   "queryParams": {
       "gender": "Male",
       "date": "2020-01-01"
   }
}
```

Below is the output response

```json Response
{
    "headers": [
        "Full name",
        "Gender"
    ],
    "data": [
        [
            "Hetram  Kewat Kewat",
            "Male"
        ],
        [
            "Reesham Kumar Panika",
            "Male"
        ],
        [
            "Ambika Prasad Panika",
            "Male"
        ],
        [
            "Shiv Prasad Panika",
            "Male"
        ],
        [
            "Aman Kumar Panika",
            "Male"
        ],
        [
            "Chandan Prasad Panika",
            "Male"
        ],
        [
            "Suraj Kumar Panika",
            "Male"
        ],
        [
            "Birendra Kumar Panika",
            "Male"
        ],
        [
            "Naresh Prasad Panika",
            "Male"
        ]
    ],
    "total": 9
}
```

### Notes

1. Right now casts need to be handled in the query itself, for example in the above query casting of date param.
