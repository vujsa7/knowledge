# Introduction
---
Document databases are type of [[NoSQL DBs]] databases that store information inside documents. Such databases are:
- [[MongoDB]]
- Cosmos DB
- CouchDB
- Amazon Document Db
- ArangoDB
and more...

# Terminology
---

| Non relational databases | Relational databases |
| ------------------------ | -------------------- |
| Document                 | Row                  |
| Collection               | Table                |
## What is a document?

Document is just **one record** inside a [collection]. Documents can be stored in different formats such as JSON, XML, BSON, etc. If we think in terms of relational databases, document is just one row inside a table, representing one object.

## What is a collection?

Collection is a group of documents (objects). If we think in terms of relational databases, collection would be a table.
# When to use document dbs?
---
## 👍 Pros:
1. Documents are schemaless, you're pretty much free to do whatever you want
2. Easy to get up and running
3. Good with dynamic scripting languages (JavaScript and Python) because mapping from document to objects is very easy
4. Simplicity of using queries
5. Faster write speeds (tradeoff is data inconsistency)
6. Built-in versioning
---
## 👎 Cons:
1. Documents are schemaless, mistakes can easily happen as there are no strict rules
2. Relationships are represented with nesting, leading to some duplication
3. Harder to represent many to many relationships, joining is so expensive
4. Offer [[Database consistency#What is eventual consistency?|eventual consistency]] with a period of inconsistency
5. Atomicity weaknesses: change involving two collections will require you to run two separate queries (per collection)

