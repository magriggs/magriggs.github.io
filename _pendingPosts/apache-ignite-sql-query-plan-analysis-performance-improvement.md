---
layout: post
title: Apache Ignite SQL Query Plan Analysis
---

A guide to analysis SQL query plans and improving SQL query performance in Apache Ignite

## Introduction

Apache Ignite contains an in-memory database, based on the H2 database, that can be queried using SQL.
When the SQL query is first received by Ignite, it calculates a “query plan” of how best to execute it.
Tuning these SQL query plans is an important step in achieving maximum performance from Apache Ignite.
This post will explain in detail how to generate query plans from Apache Ignite,
how to read and interpret those query plans,
and how to tune your Apache Ignite configuration and SQL query to achieve maximum performance.

## Generating a Query Plan

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

