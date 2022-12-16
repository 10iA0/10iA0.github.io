---
title: Introduction of Spark
date: 2019-06-02 18:30:00
tags: ["Spark"]
series: ["Spark"]
featured: false
---
<!--more-->

## Big Data
What we mean when we say Big Data? Big data is an overloaded therm with no single definition, but most data scientists and engineers have a good sense of what we mean when we say. Roughly, we know we are working with big data when, instead of using a single machiine, it is easier to use a distributed system of multiple computers.

## Spark
Spark is currently one of the most popular tools for big data analytics. You might have heard of other tools such as Hadoop. Hadoop is a slightly older technology although still in use by some companies. 

## Hadoop
Hadoop is an ecosystem of tools for big data storage and data analysis. The major difference between Spark and Hadoop is how they use memory. Hadoop writes intermediate results to disk whereas Spark tries to keep data in memory whenever possible. This makes Spark faster for many use cases.

Here is a list of some terms associated with Hadoop.

- **Hadoop MapReduce** - a system for processing and analyzing large data sets in parallel.

> Oftentimes when someone is talking about Hadoop in general terms, they are actually talking about Hadoop MapReduce. However, Hadoop is more than just MapReduce. 

- **Hadoop YARN** - a resource manager that schedules jobs across a cluster. The manager keeps track of what computer resources are available and then assigns those resources to specific tasks.

- **Hadoop Distributed File System (HDFS)** - a big data storage system that splits data into chunks and stores the chunks across a cluster of computers.

As Hadoop matured, other tools were developed to make Hadoop easier to work with. 

- **Apache Hive** - another SQL-like interface that runs on top of Hadoop MapReduce


> How is Spark related to Hadoop? The Hadoop ecosystem includes a distributed file storage system called HDFS (Hadoop Distributed File System). Spark, on the other hand, does not include a file storage system. You can use Spark on top of HDFS but you do not have to. 


> PySpark is an interface for Apache Spark in Python. It not only allows you to write Spark applications using Python APIs, but also provides the PySpark shell for interactively analyzing your data in a distributed environment. PySpark supports most of Spark’s features such as Spark SQL, DataFrame, Streaming, MLlib (Machine Learning) and Spark Core.