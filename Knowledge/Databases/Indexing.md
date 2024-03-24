> *If your queries are expensive, your application will look cheap.*
> 
> \- Aleksa Vujisic, SmartCat
# Introduction
---
Indexes are a powerful tool used in the background of a database to speed up querying.

For example, someone wants to filter and get all dogs from your `Animals` table. Your database will try to use the best strategy possible to extract that data. The only way to do that is to go through all the rows and check if each and every animal is a dog. 

This is the **worst way** to execute this query. The [[Time complexity]] is *O(n)*. The solution is to create a database index for `type` column.

# How does indexing work under the hood?
---
When an index gets created, memory is allocated to store the **index data structure.** This data structure contains sorted references (pointers) to the actual rows in the table.

Typically, indexes are stored as **B-trees** or **hash functions**. B-trees are good for finding specific element because data is sorted inside the B-tree making it easy to find the row we're looking for. Each non-leaf node contains information about the range of values for which its child nodes are responsible and pointers to those child nodes. In the case of leaf nodes, pointers directly lead to the actual rows in the table. B-trees have a root node that serves as the starting point of a search. The root node guides the search by directing it to the appropriate branch based on the comparison of the search key with the keys in the root node.

The time complexity of a search is now reduces to *O(log(n))* by using indexes.
## What is stored inside each node?

- An internal node in a B-tree contains key values and pointers to child nodes. Each key in the internal node represents a boundary or separator that determines which branch to follow in the search process. The pointer associated with each key points to the child node whose range falls between the previous key (or negative infinity for the first key) and the current key.
- A leaf node in a B-tree contains key values and pointers to the actual data or records in the table. The keys in the leaf node are the actual values from the indexed column(s) sorted in ascending order. The pointers associated with each key point directly to the corresponding rows in the table.

## Example

Let's say we're searching for number 85. We start with the root node. Keys are -15, 10, 25. It's clear that we must move to the right by following a pointer inside the 25 key value pair. Now in the next intermediate node where keys are 25, 56 and 115. It's clear that we need to follow the value (pointer) of 56. We're a the leaf node. We find that there is a key for number 85 and by following value we will be accessing the table row where 85 is located. 

The search is now done. We found number 85 in only 2 steps by traversing the B-tree. If we were to do it without an index, the search would be done by checking all 23 rows in the table.

![[B-Tree.png]]

## How many key-value pairs can be stored in each node?

The number of key-value pairs that can be stored in each node of a B-tree is determined by the order or degree of the B-tree. The order of a B-tree is defined as the maximum number of children (subtrees) that each internal node can have.

>[!example]+
>If a B-tree has order of 5, each internal node can contain maximum 5 children.
# How are indexes created?
---
Creating an index is very simple. All you need to do is decide what you want to index and then just run the command.

For example, command for a creating an index in a PostgreSQL would look like this:

```sql
CREATE INDEX index_name ON table_name (column_name);
```

> [!warning]+ 
> Creating an index can be a very expensive operation. Do not do it on a production server because this operation may cause severe downtime.

# When to use indexes?
---
If we constantly read the same data using specific columns to filter data, indexes can be used to improve performance. Multiple columns can be used as an index.

>[!danger]+
>Indexes can become expensive for data that is updated frequently. 

If the data is often updated, keep in mind that the indexes must be updated as well. This operation can actually slow down your write queries. Analyse your queries thoroughly to determine when to use indexes.

# Types of indexes
---
## Clustered index

Clustered index is an index that determines how the data is ordered inside the table. That's the main idea behind clustered indexes. Depending on the database, implementation of clustered index may vary.

MySQL for example uses primary key as the default for clustered index. Meaning that the data would be stored in the order of the primary key. 

When new data is added inside the table, it may not be in the order that the clustered index defined, which decreases **clustering factor**. Clustering factor tells us how the order in the table is aligned to the index. To maintain the performance of a clustered index, the table may need to be re-clustered periodically.

Changing clustered index is not a straightforward operation as it would cause data to be physically reorder inside the disk.
## Non-clustered indexes

Non clustered indexes are the ones that do not dictate the physical order of the data rows on disk.


