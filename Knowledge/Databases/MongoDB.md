# Introduction
---
MongoDB is a [[NoSQL DBs]] database that falls into type of [[Document DBs]]. MongoDB **stores data in JSON-like documents**, meaning fields can vary from document to document and data structure can be changed over time. It's great to use with dynamic scripting languages as it's easy to map from document model to objects inside our application.
## Terminology
| Non relational databases | Relational databases |
| ------------------------ | -------------------- |
| Collection               | Table                |
| Document                 | Row                  |
## How to start?
---
One way to start MongoDB is to download MongoDB official image and run inside the terminal:

``` bash
 docker run --name playground-mongo -dit -p 27017:27017 --rm mongo:4.4.1
```

Once the image is pulled from repository, we can connect to Mongo database by running command below:

``` bash
docker exec -it playground-mongo mongo
```

Other ways are:
- From cloud (costly)
- Installing MongoDB driver locally

# Querying
---
To show a list of all databases inside Mongo, run command:

```js
show dbs
```
## Creating a database

To **create** a new database run:

```js
use cinema
```

This command creates a new database called cinema and it uses that database. Now every time we run `db`, it will refer to the database we're using. To see the list of all possible commands you can run:

```js
db.help()
```

This command can be used to switch the database you are using if you have more than one database.
## Creating a collection

You don't need to do that manually, it will be done when we're doing an insert. How nice! But if we need some specific configuration to a collection we can use:

```js
db.createCollection(name, options);
```

## Inserts

To insert a new document into a collection we can run:

```js
db.anime.insertOne({ name: "Monster", genre: "Thriller", numberOfEpisodes: 70 })
```

This command will create a new collection called anime and insert one document inside of it.

## Retrieval

To get document from a collection we can run:

```js
db.anime.find({ genre: "Thriller" })
```

>[!tip]+ Fun Fact
> MongoDB is capable of executing [geospatial queries](https://www.mongodb.com/docs/manual/geospatial-queries/).
> If you need to find the closest distance between two points on a 2d map, you can easily do it with MongoDB.

## Operators

You can use [query and logical operators](https://www.mongodb.com/docs/manual/reference/operator/query/#query-and-projection-operators) to form complex queries, for example:

```js
db.anime.count({ 
	genre: "Thriller", 
	$or: [
		{ numberOfEpisodes: { $gte: 50 } }, 
		{ numberOfEpisodes: { $lt: 10 }}
	] 
})
```

## Projections

Always use projections to retrieve only the data you need. For example if we only want to fetch the names of the anime that satisfy a query, we can run:

```js
db.anime.find({ genre: "Action"}, { name: 1, _id: false })
```

This will project the retrieved object and only return name inside the object. We can set `_id` to `false` to exclude returning automatically generated id.
## Updating

To update a document we can run something like this:

```js
db.anime.updateOne(
	{ name: "Monster" },
	{ $set: { rating: 9 }}
)
```

We can also omit `$set` command, use `$replace` and provide the entire object to replace everything. With combination with update one, there is also an `upsert` command that will insert new object if it's not found, and if it's found it will update it. It look something like this:

```js
db.anime.updateOne(
	{ name: "One Piece" },
	{ $set: { name: "One Piece", genre: "Action", numberOfEpisodes: 1000 }},
	{ upsert: true }
)
```

## Deleting

To delete document you can use `deleteOne` and `findOneAndDelete` to return deleted element. Let's see how the command would look like:

```js
db.anime.findOneAndDelete({ name: "One Piece" })
```

>[!danger]+ Caution
>Avoid using `deleteMany` because mistakes can happen and you can accidentally delete stuff from your database.

# Indexing
---
MongoDB as well as every other database supports [[Indexing]]. To see how the query will get executed before you run it run:

```js
db.anime.findOne({ name: "Monster" }).explain("executionStats")
```

This will give you information about the strategy that MongoDB will use to perform your query. Look for `winningPlan` and inside it look for `stage`. If the stage is `COLSPAN` it means that it will perform **full-table scan**.

To create an index, we can run:

```js
db.anime.createIndex({ name: 1 })
```

Now if we run `explain` command once more, we will find that `stage` will be equal to `FETCH`, which is much much better. When we create an index we can also add another parameter: `{ unique: true }` to make sure that those fields must be unique.

# Full-Text Search
---
When we need to search text by multiple fields (columns in relational databases), we can do that by creating text search index. We can create it by running:

```js
db.anime.createIndex({ name: 1, genre: 1 });
```

This command will create index for fields `name` and `genre` and we will be able to perform full text search like this:

```js
db.anime.find({ $text: { $search: "monster thriller" } })
```

And if we want to sort the results by displaying the ones who match with search query the most we can modify the query like this:

```js
db.anime.find({ $text: { $search: "monster thriller" } }).sort({ score: { $meta: "textScore" }})
```

# Aggregation
---
> *This is my favourite thing about MongoDB.*
> 
> \- Brian Holt, Microsoft

Aggregation is a fun feature from MongoDB that allows you to derive various insights from your data. To sum it up, you can slice your data and put it inside buckets based on different conditions and then use that information further in your pipeline to derive results.

>[!example]+ 
>Let's say that we want to group animes into different categories based on number of episodes and then get the count of long animes.

We could do it using aggregation by running this:

```javascript
db.anime.aggregate([
  {
    $bucket: {
      groupBy: "$numberOfEpisodes",
      boundaries: [0, 10, 30],
      default: "very long",
      output: {
        count: { $sum: 1 },
      },
    },
  },
])
```

This will place all animes into categories based on number of episodes. It will place all anime that have 0 - 9 episodes in first bucket (0 bucket), 10 - 29 in second bucket (10 bucket), 30+ to very long bucket. It will then output how many animes are in the each bucket.

We can combine aggregations, for example we can first find all thrillers and then perform grouping by `numberOfEpisodes` like this:

```js
db.anime.aggregate([
{
    $match: {
      genre: "Thriller",
    },
    $bucket: {
      groupBy: "$numberOfEpisodes",
      boundaries: [0, 10, 30],
      default: "very long",
      output: {
        count: { $sum: 1 },
      },
    },
},
])
```

# MongoDB Ops
---
## Primaries, Secondaries, and Replica Sets

A replica set is a set of servers running *mongod* process and all of them have the same set of data. They exist so that if one of the servers goes down, there are other servers available to step up and continue running without downtime as well as making sure that if a server blows up that you're not losing data.

The primary server accepts reads and writes. There is always one primary server per replica set. The primary server is usually under most load.

Secondary servers accept only reads. All writes must go through the primary. However if you're just doing a read operation you can freely use the secondary. It does end up storing all the information from the primary eventually but it's key to note the MongoDB has [[Database consistency#What is eventual consistency?|eventual consistency]]. That means the secondaries may have a lag time between when you write to the primary and when it updates the secondary. You can set write priorities in MongoDB so you can make your app pause until it can guarantee that all secondaries have received the write.

## Arbiters and Elections

When a primary server goes down, your remaining MongoDB servers hold an election. Through an algorithm the secondaries will decide on a new primary and resume from there.

Sometimes you only have one primary and one secondary server. If that happens election may get deadlocked. In this case you need arbiter. It's the server that just exists to vote, it does not store the data.

## Sharding

Sometimes if you have too much data, it may not be wise to keep all your data on a set of servers due to limitations such as RAM or CPU. Then you need to shard your data. Sharding is a type of database partitioning that separates large databases into smaller, faster, more easily managed parts. This adds a non-trivial amount of complexity to your app.

## Cloud version of MongoDB

As a developer, it's much easier to just use a managed cloud service like [MongoDB Atlas](https://www.mongodb.com/cloud/atlas), [Microsoft Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-introduction), or [Amazon Web Services DocumentDB](https://aws.amazon.com/documentdb/). These services will manage literally everything for you so you can just focus on writing your app. Only use servers and replicas if you have a managed team of DevOps who can do that. And even then, it's up to them to make that decision.
