---
layout: post
title: "Audit and Log Database DML Changes in PostgreSQL With Cyan Audit"
date: 2016-12-27T19:46:55+05:00
comments: true
categories: postgreSQL
image: 001.png
---

## Introduction

The database serves an important role. The security of your database systems is also becoming increasingly important, and an effective audit of your database can reduce the potential security risk.

> Information about the periodic changes (DML — Insert, Update, Delete) of the intensity and structure of database workloads plays an important role in reducing the potential security risk, strengthening the security of database, and making it possible to trace the source when looking for problems and evidence of wrongdoing, such as fraud or records being modified without authorization.

In this article, I am going to share an interesting Postgres extension that does the DML logging job simply and seriously. Although there are many others way of doing that, in this article, I&nbsp;am going to use the Cyan Audit Postgres Extension.

## Cyan Audit

This extension is responsible for detecting all database changes (INSERTs, UPDATEs, and DELETEs) and recording them in a separate 'audit' schema within the same Postgres database so that they can be reviewed using online inquiry screens.

By default, logging is TRUE for all current database tables and their columns, but it can be set to FALSE to disable logging on table and column levels.

This extension also provides a means to revert back to the correct values.

## Let's Get Started

I have created the database `stackDB` with some tables on it.

![Stack Db with Default Public Schema]({{ site.raw_static_url }}/audit-and-log-postgresql/001.png)

I will use the customer table in this article for demonstration. For now, this table contains the record below:  


```sql
stackdb=# Select * from customer order by customer_id limit 5;
+-------------+----------+------------+-----------+-------------------------------------+------------+------------+-------------+-------------------------+--------+
| customer_id | store_id | first_name | last_name |                email                | address_id | activebool | create_date |       last_update       | active |
+-------------+----------+------------+-----------+-------------------------------------+------------+------------+-------------+-------------------------+--------+
|           1 |        1 | Mary       | Smith     | mary.smith@sakilacustomer.org       |          5 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1 |
|           2 |        1 | Patricia   | Johnson   | patricia.johnson@sakilacustomer.org |          6 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1 |
|           3 |        1 | Linda      | Williams  | linda.williams@sakilacustomer.org   |          7 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1 |
|           4 |        2 | Barbara    | Jones     | barbara.jones@sakilacustomer.org    |          8 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1 |
|           5 |        1 | Elizabeth  | Brown     | elizabeth.brown@sakilacustomer.org  |          9 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1 |
+-------------+----------+------------+-----------+-------------------------------------+------------+------------+-------------+-------------------------+--------+
```


### Install and Enable the Cyan Audit Extenstion With stackDb Database

Download Cyan Audit from [this link][2]&nbsp;and unzip it.


```shell
$ cd -&gt; cyanaudit-1.0.2
$ make install
```

If running above command gives the error "`How To Resolve Pgxs.Mk: No Such File Or Directory Make`" then run the following commands:



```shell
$ sudo apt-get install postgresql-server-dev-all
$ sudo apt-get install postgresql-common
```

Now that you're logged into to your Postgres database using Shell, run the following commands to enable the&nbsp;extension with your stackDB database.



```sql
postgres=# connect stackdb;
stackdb=# create extension cyanaudit ;
CREATE EXTENSION
stackdb=# select cyanaudit.fn_create_event_trigger();
+-------------------------+
| fn_create_event_trigger |
+-------------------------+
| |
+-------------------------+
(1 row)

stackdb=# select cyanaudit.fn_update_audit_fields('public');
+------------------------+
| fn_update_audit_fields |
+------------------------+
| |
+------------------------+
(1 row)
```



Now you have enabled the Cyan Audit Extension with your default schema in your stackDB database, refresh the database. Now you will have two schemas in your database, as seen below:

![Stack Db with Default Public Schema]({{ site.raw_static_url }}/audit-and-log-postgresql/002.png)

Now, all the logging details of your tables under the public schema will be stored in tables under the "cyanaudit" schema. If you explore the cyanaudit schema, you will have four tables, three views, and 31 functions&nbsp;that do the job for you. You don't need to worry about these — you just need to know about&nbsp;`1 table, 1 view, and 1 function`&nbsp;to do your job.

Now, let's play with the customer table by updating, deleting, and inserting snew record in this table. First, update the records of users with ID 1 in the&nbsp;customer table.


```sql
stackdb=# Select * from customer order by customer_id limit 5;
+-------------+----------+------------+-----------+-------------------------------------+------------+------------+-------------+-------------------------+--------+
| customer_id | store_id | first_name | last_name |                email                | address_id | activebool | create_date |       last_update       | active |
+-------------+----------+------------+-----------+-------------------------------------+------------+------------+-------------+-------------------------+--------+
|           1 |        1 | Mary       | Smith     | mary.smith@sakilacustomer.org       |          5 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1 |

stackdb=# update customer set address_id = 6 , activebool = false where customer_id = 1;
UPDATE 1

stackdb=# Select * from customer order by customer_id limit 5;
+-------------+----------+------------+-----------+-------------------------------------+------------+------------+-------------+----------------------------+--------+
| customer_id | store_id | first_name | last_name |                email                | address_id | activebool | create_date |        last_update         | active |
+-------------+----------+------------+-----------+-------------------------------------+------------+------------+-------------+----------------------------+--------+
|           1 |        1 | Mary       | Smith     | mary.smith@sakilacustomer.org       |          6 | f          | 2006-02-14  | 2016-09-14 22:08:19.330765 |      1 |
```


I have updated the address_id = 6 and&nbsp;activebool = false of customer 1. Remember the old value. Now, let's ask Cyan Audit to tell us the old value of these two columns of customer 1.

All the logged details are stored in the&nbsp;`tbl_audit_event`&nbsp;table.

![Stack Db with Default Public Schema]({{ raw_static_url }}/audit-and-log-postgresql/003.png)

As you can clearly see, the columns are there to store the logged data:&nbsp;`audit_filed`&nbsp;has the ID of the column in cyanaudit,&nbsp;`pk_vals`&nbsp;has the primary key of the record,&nbsp;`recorded`&nbsp;represents the timestamp at which the operation was performed,&nbsp;`uid`&nbsp;holds the user ID of Postgres,&nbsp;`row_op`&nbsp; holds the type of operation like update, delete or insert, `txid`&nbsp;hold logged transaction ID — helpful in reverting the transaction, audit_transaction_type —&nbsp;`old_value`&nbsp;is old value of the record, and&nbsp;`new_value`&nbsp;is new value of the record.

To get our logged data, Cyan Audit provides us with a view built in view&nbsp;`vw_audit_log`. We need to pass the view to get log data by passing the table name and column name.


```sql
stackdb=# select txid, pk_vals, recorded, op,old_value,new_value from vw_audit_log where table_name = 'public.customer' and pk_vals = '{1}';
+------+---------+----------------------------+----+-------------------------+----------------------------+
| txid | pk_vals |          recorded          | op |        old_value        |         new_value          |
+------+---------+----------------------------+----+-------------------------+----------------------------+
| 2875 | {1}     | 2016-09-14 22:08:19.333788 | U  | true                    | false                      |
| 2875 | {1}     | 2016-09-14 22:08:19.333788 | U  | 5                       | 6                          |
| 2875 | {1}     | 2016-09-14 22:08:19.333788 | U  | 2013-05-26 14:49:45.738 | 2016-09-14 22:08:19.330765 |
+------+---------+----------------------------+----+-------------------------+----------------------------+
```

Now the logs tell us clearly when the update query was performed, what the old value was, and what the new value is.

Now, what if we want to revert this transaction? Not a problem. Cyan Audit has a built-in function,&nbsp;`fn_undo_transaction()`&nbsp;, to do this. What we need to do is pass the transaction ID of the log to this function, and the record will be reverted back in the customer table. Let's try:



```sql
stackdb=# select fn_undo_transaction('2875');
+----------------------------------------------------------------------------------------------------------------------------------+
|                                                       fn_undo_transaction                                                        |
+----------------------------------------------------------------------------------------------------------------------------------+
| UPDATE public.customer SET last_update = '2013-05-26 14:49:45.738',activebool = 'true',address_id = '5' WHERE customer_id = '1'; |
+----------------------------------------------------------------------------------------------------------------------------------+
(1 row)
stackdb=# Select * from customer order by customer_id limit 5;
+-------------+----------+------------+-----------+-------------------------------------+------------+------------+-------------+----------------------------+--------+
| customer_id | store_id | first_name | last_name |                email                | address_id | activebool | create_date |        last_update         | active |
+-------------+----------+------------+-----------+-------------------------------------+------------+------------+-------------+----------------------------+--------+
|           1 |        1 | Mary       | Smith     | mary.smith@sakilacustomer.org       |          5 | t          | 2006-02-14  | 2016-09-14 22:31:09.315708 |      1 |
```


Now if we explore our vw_audit_log, we will see it also records the undo transaction log, too:


```sql
stackdb=# select * from vw_audit_log where table_name = 'public.customer' and pk_vals = '{1}';
+----------------------------+-----+------------+------+------------------+-----------------+-------------+---------+----+----------------------------+----------------------------+
|          recorded          | uid | user_email | txid |   description    |   table_name    | column_name | pk_vals | op |         old_value          |         new_value          |
+----------------------------+-----+------------+------+------------------+-----------------+-------------+---------+----+----------------------------+----------------------------+
| 2016-09-14 22:31:09.318275 |   0 |            | 2877 | Undo transaction | public.customer | activebool  | {1}     | U  | false                      | true                       |
| 2016-09-14 22:31:09.318275 |   0 |            | 2877 | Undo transaction | public.customer | address_id  | {1}     | U  | 6                          | 5                          |
| 2016-09-14 22:31:09.318275 |   0 |            | 2877 | Undo transaction | public.customer | last_update | {1}     | U  | 2016-09-14 22:08:19.330765 | 2016-09-14 22:31:09.315708 |
| 2016-09-14 22:08:19.333788 |   0 |            | 2875 |                  | public.customer | activebool  | {1}     | U  | true                       | false                      |
| 2016-09-14 22:08:19.333788 |   0 |            | 2875 |                  | public.customer | address_id  | {1}     | U  | 5                          | 6                          |
| 2016-09-14 22:08:19.333788 |   0 |            | 2875 |                  | public.customer | last_update | {1}     | U  | 2013-05-26 14:49:45.738    | 2016-09-14 22:08:19.330765 |
+----------------------------+-----+------------+------+------------------+-----------------+-------------+---------+----+----------------------------+----------------------------+
```


It also records the Delete and Insert operations on the customer table.

## Set Logging ON and OFF on Specific Columns

By default, logging is TRUE for all current database tables and their columns, but it can be set to FALSE to disable logging on the table and column levels.

You will notice that there is a table under the&nbsp;cyanaudit schema in our database named tb_audit_field, which contains the table and column name on which&nbsp;`logging`&nbsp; is set&nbsp;`on`&nbsp;or&nbsp;`off`.

Let's explore this table, too:

```sql
stackdb=# select * from tb_audit_field where table_name = 'customer';
+-------------+--------------+------------+-------------+---------+----------+
| audit_field | table_schema | table_name | column_name | enabled | loggable |
+-------------+--------------+------------+-------------+---------+----------+
|           2 | public       | customer   | store_id    | t       | t        |
|           9 | public       | customer   | email       | t       | t        |
|          18 | public       | customer   | last_update | t       | t        |
|          25 | public       | customer   | customer_id | t       | t        |
|          45 | public       | customer   | first_name  | t       | t        |
|          49 | public       | customer   | activebool  | t       | t        |
|          54 | public       | customer   | active      | t       | t        |
|          62 | public       | customer   | last_name   | t       | t        |
|          66 | public       | customer   | address_id  | t       | t        |
|          68 | public       | customer   | create_date | t       | t        |
+-------------+--------------+------------+-------------+---------+----------+
(10 rows)
```

Now I will turn off the logging for the&nbsp;`create_date`&nbsp;column of the&nbsp;`customer`&nbsp;table. From now on, any operation on this column will not be logged by Cyan Audit.&nbsp;

```sql
stackdb=# update tb_audit_field set enabled = false where audit_field = 68;
UPDATE 1
stackdb=# select * from tb_audit_field where table_name = 'customer';
+-------------+--------------+------------+-------------+---------+----------+
| audit_field | table_schema | table_name | column_name | enabled | loggable |
+-------------+--------------+------------+-------------+---------+----------+
|           2 | public       | customer   | store_id    | t       | t        |
|           9 | public       | customer   | email       | t       | t        |
|          18 | public       | customer   | last_update | t       | t        |
|          25 | public       | customer   | customer_id | t       | t        |
|          45 | public       | customer   | first_name  | t       | t        |
|          49 | public       | customer   | activebool  | t       | t        |
|          54 | public       | customer   | active      | t       | t        |
|          62 | public       | customer   | last_name   | t       | t        |
|          66 | public       | customer   | address_id  | t       | t        |
|          68 | public       | customer   | create_date | f       | t        |
+-------------+--------------+------------+-------------+---------+----------+
(10 rows)
```

This way, we can turn off the non-critical columns or tables in our database.

I also want to talk about querying this log from your web application or via exporting to some human readable form. And most of this logging is used for reporting purposes. Most of the languages or frameworks today provide facilities to connect with multiple databases or multiple connections within the&nbsp;same database. In our case, it's one database:

```yml
doctrine:
    dbal:
        default_connection: default
        connections:
            default:
                driver:   pdo_pgsql
                host:     '%database_host%'
                port:     '%database_port%'
                dbname:   '%database_name%'
                user:     '%database_user%'
                password: '%database_password%'
                charset:  UTF8
                options:
                   search_path: public
            cyanaudit:
                driver:   pdo_pgsql
                host:     '%database_host2%'
                port:     '%database_port2%'
                dbname:   '%database_name2%'
                user:     '%database_user2%'
                password: '%database_password2%'
                charset:  UTF8
                options:
                   search_path: cyanaudit

    orm:
        default_entity_manager: default
        entity_managers:
            default:
                connection: default
            cyanaudit:
                connection: cyanaudit
```

Or in Django:

``` python
DATABASES = {

    'default': {
            'ENGINE': 'django.db.backends.postgresql_psycopg2',
            'OPTIONS': {
                'options': '-c search_path=public'
            },
            'NAME': 'multi_schema_db',
            'USER': 'appuser',
            'PASSWORD': 'secret',
    },

    'cyan_audit': {
            'ENGINE': 'django.db.backends.postgresql_psycopg2',
            'OPTIONS': {
                'options': '-c search_path=cyan_audit'
            },
            'NAME': 'multi_schema_db',
            'USER': 'appuser',
            'PASSWORD': 'secret',
    },
}
```

## Conclusion

In this DevOps world, developers are increasingly being asked (or sometimes pushed) to manage the database as well. So, with these small, free, open-source Postgres extensions, the chances of survival are high.

Let me know your thoughts in the comments section.&nbsp;I would love to hear your feedback!

[1]: https://dzone.com/storage/temp/3053461-screenshot-from-2016-09-14-20-56-01.png "Stack Db with Default Public Schema"
[2]: http://pgxn.org/dist/cyanaudit
[3]: https://dzone.com/storage/temp/3053555-screenshot-from-2016-08-26-23-35-07.png
[4]: https://dzone.com/storage/temp/3053582-screenshot-from-2016-09-14-22-16-35.png
