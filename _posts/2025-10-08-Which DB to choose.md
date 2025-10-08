---
title: Which DB to choose?
author: rajneesh304
date: 2025-10-08
tags:
  - system-design
  - DB
category:
  - Tech
description: In this blog, we are going to discuss which DB should one choose in which scenario.
---
# Introduction
There are multiple options to choose a data base from and a lot of factors which can impact our choice. The choice can be based on type of database, type of index, type of storage etc. 
In this blog, we are going to discuss major types of databases and their usages.

# Indexes
two main types: 
- LSM Tree + SS Table
- B/B+ tree

## LSM Tree + SS Table
- LSM Tree is a balanced binary search tree in memory. Once the tree gets too big, it flushes to sorted table on disk. 
- We can Binary search the SS table for a key.
- If there are too many SS tables, they can be merged together (old values get discarded)

*Pro:* fast write to memory
*Con:* May have to search many ss tables to get a particular key

## B Tree
- It is a binary search tree which is implemented completely on a disk.
Pro: Faster reads, know exactly where keys are located
Con: Slow write to disk instead of memory

## Replication
Types: 
1. Single Leader replication: Writes go to one db, reads can go to any db
2. Multi leader replication: writes can go to a small subset of leader dbs. reads can come from any db. 
3. Leaderless replication: Writes go to all dbs. Reads come from all db. 


# Databases
## SQL Databases
- Relational/Normalized data
- Have Transactional (ACID) Guarantees
- Typically use B trees

## Mongo DB
- Document data model
- B Trees and Transactions supported
- Use it when you need SQL like guarantees on data that is more flexible via document model

## Apache Cassandra
- Wide column datastore, has a shard key and a sort key
	- allows for flexible schema and ease of partitioning
- Multileader / leaderless replication
	- super fast writes, albeit uses last write wins for conflict resolution
	- may clobber existing writes if they were not the winner of LWW
- Index based off of LSM tree and SS table
	- fast writes
- Great for applications with high write volume, consistency is not as important, all writes and reads go to the same shard(no transactions)
- example: chat application

## RIAK
- Key value store
- Cassandra + CRDTs(Conflict free replicated data type)

## Apache Hbase
- Column oriented storage/good for Analytics
- Data is stored on Hadoop
- Single Leader Replication

## Memcached and Redis
- in memory key value storage
- index not required. Uses in memory hashmap

