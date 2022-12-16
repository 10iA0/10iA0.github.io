---
title: Introduction to Data Warehouse
date: 2019-05-01 18:30:00
tags: ["Data Warehouse"]
series: ["Data Warehouse"]
featured: false
---

<!--more-->

## A business perspective on a Data Warehouse
Let's me introduce a Data Warehouse in a business context. For example, You are in charge of a retailer’s data infrastructure. There are some business activities you will meet in the field of retailer:

- Customers should be able to find goods & make orders
- Inventory Staff should be able to stock, retrieve, and re-order goods
- Delivery Staff should be able to pick up & deliver goods
- HR should be able to assess the performance of sales staff
- Marketing should be able to see the effect of different sales channels
- Management should be able to monitor sales growth

The problems you have now are: Can I build a database to support these activities? Are all of the above questions of the same nature? 

Let's take a closer look at these problems. In fact, you have 2 processes on daily work: **Operational Processes** and **Analytical Processes**. The first three of activities above are on operational processes. The others are on analytical processes. Obviousely, the operational databases are good for activities on operational processes. It's not only excellent for operations but also no redundancy and high integrity. However, It's really a bad idea applying operational databases on activites on analytical processes. It's too slow and hard to understand for analytics as the SQLs used in operational databases usually have too many joins.

So, how to solve these problems?

We can define 2 processing modes and create a system for them to c0-exist. The 2 processing are: **OLTP**(Online transactional processing) and **OLAP**(Online analytical processing), and the sysytem is **Data Warehouse**.

>The data warehouse is an abstract collection of technical sets that provides supports of decision-making on administrative departments and data exhibition on operation applications. By doing analyses based on data on data warehosue, the business processes, costs and product qualities will be refined and developed.

## A technical perspective on a Data Warehouse
After the introduction of data warehouse in business perspective, we should introduce the data warehouse in technical perspective. We prefer to use the architecture issued by Kimball, and sometimes use the architecture issued by hybrid Kimball and Inmon. For example, the services of data warehouse in Alibaba Cloud are based on the Hybrid Kimball Bus & Inmon CIF.

> A Data Warehouse is a copy of transaction data specifically structured for query and analysis. --Kimball

> A Data Warehouse is a subject-oriented, integrated, nonvolatile, and time-variant collection of data in support of management's decisions. --Inmon

How to use the data warehouse to connect the 2 processing modes? Assume there is a bridge that transfers data (raw resources) from OLTP to OLAP. This bridge is roughly constructed by 2 components: ETL and Dimensinoal Models. 

On one hand, the ETL is a combination of the first letter in 3 words: Extract, transfer and load. We **extract** the data from the sources used for operations, **transform** the data and **load** it into dimensinoal models. 

``` text
Extracting
- Get the data from its source
- Possibly delete old state

Transforming
- Integrate many sources together
- Possibly clean inconsistencies, duplication, missing values, etc.
- Possibly produce diagnostic metadata

Loading
- Structure and load the data into the dimensional data model
```

On the other hand, the dimensional model is designed to make it easy for businees users to work with the data, and improve analytical queries performance.

## Dimensional modeling
There are 2 schemas in the design of dimensional modelings: star schema and snowflake schema. The star schema is preferred to use in dimensional modeling, whihch is not like 3NF used for tables in operational database. Dimensional modelling is easy to understand and fast in analytical query. In star schema, a fact table joins with dimension tables, **which is only good for OLAP not OLTP**. However, in 3NF, analytical queries contain lost of expenive joins, which is hard to explain to business users.

```text
Fact tables
- Record business events, e.g. what (record an order, a phone call, a book review)
- Fact tables columns record events recorded in quantifiable metrics like quantity of an item, duration of a call, a book rating

Dimension tables
- Record the context of the business events, e.g. who, what, where, why, etc.
- Dimension tables columns contain attributes like the sotre at which an item is purchansed, or the customer who made the call, etc.
```

## Data Warehouse architecture

## OLAP cubes
An OLAP cube is an aggregation of a fact metric on a number of dimensions, which is easy to communicate to business users. There are some common OLAP operations: **Rollup, drill-down, slice, dice**. The OLAP cubes should store the finest grain of data (atomic data), in case we need to frill-down to the lowest level.

```text
Slice: Reduce N dimensions to N-1 dimensions by restricting one dimension to a single value.

Dice: Compute a sub-cube by restricting some of the values of the dimensions
```

## Data Warehouse technology
There are 2 technologies used in data storage: Columnar Storage and Row Storage. Columnar storage is good for OLAP, and row storage is good for OLTP.
