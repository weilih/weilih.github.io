---
title: "Silent Logger"
date: 2017-07-28T18:52:00+08:00
comments: true
---

:warning: ***requires PostgreSQL 9.6+.***

More often than not, we write module/function to track database record changes for administrative purposes. Of course, as a web developer, we tends to do it on application level.
But let's try to do it on database layer this time. :sunglasses:

## Setup
First, we create the tables. _(NOTE: json or jsonb is fine.)_

- ***products***: table that we going to track/log.
- ***activity_logs***: logger table that tracks/logs record modifications.

{% gist b8a0f52320142ef865c2b10f8042ce91 00-create-table.sql %}

## Create Function & Trigger
Here is where the magic happens. By using function `row_to_json`, we insert the whole record row into 'activity' field in 'json/jsonb' format.

{% gist b8a0f52320142ef865c2b10f8042ce91 01-create-function-trigger.sql %}

```cmd
test=# select * from activity_logs order by id desc;
 id | table_name | table_key | action |                                                              activity                                                              |        inserted_at         
----+------------+-----------+--------+------------------------------------------------------------------------------------------------------------------------------------+----------------------------
  3 | products   |         1 | UPDATE | {"id": 1, "name": "iPad", "price": 700.0, "updated_at": "2017-07-28T17:59:45.241048", "inserted_at": "2017-07-28T17:59:36.175178"} | 2017-07-28 17:59:45.241048
  2 | products   |         1 | UPDATE | {"id": 1, "name": "iPad", "price": 800.0, "updated_at": "2017-07-28T17:59:44.755292", "inserted_at": "2017-07-28T17:59:36.175178"} | 2017-07-28 17:59:44.755292
  1 | products   |         1 | INSERT | {"id": 1, "name": "iPad", "price": 900.0, "updated_at": "2017-07-28T17:59:36.175178", "inserted_at": "2017-07-28T17:59:36.175178"} | 2017-07-28 17:59:36.175178
(3 rows)
```

### Window Function
A handy VIEW to show current & previous activity in single-row for comparison.

{% gist b8a0f52320142ef865c2b10f8042ce91 03-window-function.sql %}

## Working with JSON/JSONB
By converting ROW to JSON type, we could store any table record into 'activity' field. Then, to reverse it from JSON to ROW, we could use function `json_populate_record`. With that, we could somehow perform Event-Sourcing _**manually**_ to reverse accidental mistake from the history. :slightly_smiling_face:

{% gist b8a0f52320142ef865c2b10f8042ce91 04-working-with-json.sql %}


### References
- [PostgreSQL: JSON Functions & Operators ](https://www.postgresql.org/docs/9.6/static/functions-json.html)
- [PostgresQL Tutorial: Window Function](http://www.postgresqltutorial.com/postgresql-window-function/)
- [PostgreSQL: built-in Window Functions](https://www.postgresql.org/docs/9.6/static/functions-window.html)
