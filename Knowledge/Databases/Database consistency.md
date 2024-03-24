When talking about distributed database systems (replicas), we must talk about database consistency. Lets assume a simple setup with one master db and two read replicas. Now when someone writes into db, those changes are copied down to replicas. So that if master goes down, we can restore master from one of the replicas.

**So the question is:** after you have written onto the master, should you first wait to copy those changes to replicas and then respond or should you reply back and copy into replicas in the background?

To answer that question we must know that these two options have names:
- **Strong consistency** *(option 1)*
- **Eventual consistency** *(option 2)*
# What is eventual consistency?
--- 
Eventual consistency is a guarantee that when an update happens to the data inside distributed database, the change will be reflected in all nodes eventually. [[NoSQL DBs]], such as document dbs, provide eventual consistency. Meaning, the data across all nodes may not initially be consistent but they will eventually be consistent over a period of time. 

# What is strong consistency?
---
Strong consistency is a guarantee that when an update happens to the data inside distributed database, the change will be reflected in all nodes. This guarantee comes with the tradeoff of speed. Data must be blocked across multiple nodes but consistency is preserved. [[SQL DBs]] offer strong consistency.

![[Database Replicas & Blocking.png]]


