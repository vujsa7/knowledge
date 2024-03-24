# Introduction
---
ACID is an acronym that stands for
- Atomicity
- Consistency
- Isolation
- Durability
It's a set of properties that describe how transaction should be like.

## Atomicity

Describes that every transaction should be like one unit. Either everything succeeds or everything fails. There's no in between state where some operation is complete and some is not.

## Consistency

Describes that transactions can only change data in allowed ways. All constraints, cascades, triggers must be respected. It also means that every future transaction sees the effect of previous transactions. It also means that databases must be consistent between different servers. What if we have 5 databases and one replica goes down, then the other ones must pick up the slack. [[SQL DBs|SQL]] databases provide ACID guarantees, [[NoSQL DBs|NoSQL]] provides [[NoSQL#BASE guarantees|Base guarantees]].

## Isolation

Says that we can have a multi threaded databases but transactions that are run in parallel must provide the same result as if they were run sequentially. This means that transactions are isolated from one another until they are completed.

## Durability

Once the transactions are committed, changes made by the transactions are permanent and will not be lost, even in the case of system failure.

