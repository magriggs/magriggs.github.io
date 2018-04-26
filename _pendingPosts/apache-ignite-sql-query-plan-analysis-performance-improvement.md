---
layout: post
title: Apache Ignite SQL Query Plan Analysis
---

A guide to analysis SQL query plans and improving SQL query performance in Apache Ignite

### Introduction

Apache Ignite contains an in-memory database, based on the H2 database, that can be queried using SQL.
When the SQL query is first received by Ignite, it calculates a “query plan” of how best to execute it.
Tuning these SQL query plans is an important step in achieving maximum performance from Apache Ignite.
This post will explain in detail how to generate query plans from Apache Ignite,
how to read and interpret those query plans,
and how to tune your Apache Ignite configuration and SQL query to achieve maximum performance.

### Generating a Query Plan

Asking Apache Ignite to generate a SQL query plan is very simple.  Given a simple query:

```SQL
SELECT * 
FROM "CustomerCache".CUSTOMER
WHERE CITY='Callander'
``` 
then to generate the query plan we simply prefix the query with `EXPLAIN PLAN`:

```SQL
EXPLAIN PLAN SELECT *
FROM "CustomerCache".CUSTOMER
WHERE CITY='Callander'
``` 

When we execute this query, we get back the query plan that shows how Ignite (and H2) 
intends to execute your query.  An example query plan for the above query might be:

```SQL
SELECT
    C__Z0.NAME AS __C0_0,
    C__Z0.ADDRESS AS __C0_1,
    C__Z0.CITY AS __C0_2,
    C__Z0.CUSTOMERID AS __C0_3
FROM "CustomerCache".CUSTOMER C__Z0
    /* "CustomerCache".CUSTOMER.__SCAN_ */
WHERE C__Z0.CITY = 'Callander'
SELECT
    __C0_0 AS NAME,
    __C0_1 AS ADDRESS,
    __C0_2 AS CITY,
    __C0_3 AS CUSTOMERID
FROM PUBLIC.__T0
    /* "CustomerCache"."merge_scan" */
```

### Reading and Interpreting a Query Plan
Let's look at this piece-by-piece to understand.  The first section

```SQL
SELECT
    C__Z0.NAME AS __C0_0,
    C__Z0.ADDRESS AS __C0_1,
    C__Z0.CITY AS __C0_2,
    C__Z0.CUSTOMERID AS __C0_3
```
is simple to interpret: our `SELECT *` is translated in to a `SELECT` of all of the columns in the 
`FROM` table.  The columnsn are all gives alias, `__C0_0 to __CO_3`.  The next section

```SQL
 FROM "CustomerCache".CUSTOMER C__Z0
     /* "CustomerCache".CUSTOMER.__SCAN_ */
```
