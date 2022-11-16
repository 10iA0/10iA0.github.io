---
title: SQL in 10 Minutes
date: 2017-07-25 20:22:00
tags: ["SQL"]
series: ["SQL"]
featured: false
---

Here are key points in DML language.

<!--more-->

## Drop Tables
- `if exists` is used to drop existed tables. 

For example,

``` SQL
drop table if exists transaction_fact;
```
## Create Tables
- `if not exists` is used to create tables that were not created at the time of being; 
- `primary key`, `not null` are used to condition a column value unique and non-duplicated;
- `references` is used to set up a foreign key refering to a primary key in another table.

For example,

``` SQL
create table if not exists transaction_fact
    (
        trans_id serial primary key,
        cust_id integer not null references customer_dim(cust_dim),
        prod_id integer not null references product_dim(prod_id),
        vend_id integer not null references vendor_dim(vend_id),
        `date` date not null references data_dim(`date`),
        `time` time not null references time_dim(`time`),
        quantity integer not null,
        single_price decimal not null,
        total_price decimal not null
    );
```

- `check` is used to check if the value in column is satisfied with the thresould.

For example,
```SQL
create table if not exists date
    (
        `date` date primary key,
        year integer not null check (year > 0),
        month integer not null check (month > 0),
        week integer not null check (week > 0),
        day integer not null check (day > 0),
        weekday integer not null check (weekday > 0),
        is_weekend varchar not null 
    );
```

## Insert Records
```SQL
insert into date
    (
        date,
        year,
        month,
        week,
        day,
        weekday,
        is_weekend
    )
    values (%s, %s, %s, %s, %s, %s, %s)
    on conflict (date)
    do nothing;
```