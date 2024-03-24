# Introduction
---
PostgreSQL is a relational database most commonly used by web developers. It's [[ACID]] compliant database that can run on any system.

## How to start?
---
One way to start PostgreSQL is to download official image and run inside the terminal:

``` bash
docker run --name playground-postgres -e POSTGRES_PASSWORD=mysecretpassword -p 5432:5432 -d --rm postgres:13.0
```

Once the image is pulled from repository, we can connect to Postgres database by running command below:

``` bash
docker exec -it -u postgres playground-postgres psql
```

Other ways of starting PostgreSQL are:
- From cloud (costly)
- Installing PostgreSQL driver locally

By default we're connected to postgres database by default. First order of business is to create new database.

# Querying
---
Querying is done via psql CLI and there is built in support for running SQL queries as well as special admin commands.
## Creating a database
---
To create a new database:

```sql
CREATE DATABASE message_boards;
```

Then connect to the database by running:

```sql
-- connect to the message_boards database
\c message_boards;
```

We're using psql CLI to connect to the database. There are more commands from CLI that give us admin commands that we can use:

```sql
-- see all databases
\l

-- see all tables in this database, probably won't see anything
\d

-- see all available commands
\?

-- see available queries
\h

-- run a shell command
\! ls && echo "hi from shell!"
```

Use `--` to add a comment.
## Creating a table
---
To create a table we need to use `CREATE` DDL command. Difference between SQL and NoSQL is that in SQL we need to define a **schema** (typically when creating a table).

```sql
CREATE TABLE users (
  user_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  username VARCHAR ( 25 ) UNIQUE NOT NULL,
  email VARCHAR ( 50 ) UNIQUE NOT NULL,
  full_name VARCHAR ( 100 ) NOT NULL,
  last_login TIMESTAMP,
  created_on TIMESTAMP NOT NULL
);
```

Couple of explanations:
- `GENERATED ALWAYS AS IDENTITY` means from 0 to n (incrementing). We also had to specify `PRIMARY KEY` - in MongoDB, we rely on the `_id` field to be that key field. PostgreSQL doesn't do that for you by default.
- `NOT NULL` does not mean it can't be empty string. They could still be empty strings but you'd have to intentionally specify that.

There are more commands that can be used to maniHere is the [list of all types](https://www.postgresql.org/docs/9.5/datatype.html#DATATYPE-TABLE) in PostgreSQL.

We can also use more DDL commands. Such as `ALTER`
- [****DROP****](https://www.geeksforgeeks.org/sql-drop-truncate/): This command is used to delete objects from the database.
- [****ALTER****](https://www.geeksforgeeks.org/sql-alter-add-drop-modify/)****:**** This is used to alter the structure of the database.
- [****TRUNCATE****](https://www.geeksforgeeks.org/sql-drop-truncate/)****:**** This is used to remove all records from a table, including all spaces allocated for the records are removed.
- [****COMMENT****](https://www.geeksforgeeks.org/sql-comments/): This is used to add comments to the data dictionary.
- [****RENAME****](https://www.geeksforgeeks.org/sql-alter-rename/)****:**** This is used to rename an object existing in the database.

> [!danger]+ Caution
> Running ALTER table is a very expensive command! It can cause downtime. Be very cautious when using on production environment. Not only ALTER table command, but also creating an index will also be expensive.
## Inserting
---
To insert a row to users table we can run:

```sql
INSERT INTO users (username, email, full_name, created_on) VALUES ('vujsa7', 'aleksa@vujisic.com', 'Aleksa Vujisic', NOW());
```

If the operation is successful we'll get `INSERT 0 1`. 0 is code for success and 1 represents OID (object identifier), which is something PostgreSQL used for system tables.
## Retrieval
---
To get all rows with all columns from users table, we can run:

```sql
SELECT * FROM users;
```

The user we entered should be displayed. 

>[!tip] Useful file
> Use this [[sample-postgres-data.sql|sql file]] and execute it inside the database to easily create all tables and data inside tables.
## Limit and offset (pagination)
---
Let's select fewer users:

```sql
SELECT * FROM users LIMIT 5;
```

This will scope down to how many records you get to just 5. We can also add `OFFSET` to fetch next section of data.
## Projection
---
Let's just some of the columns now, not all of them.

```sql
SELECT username, user_id FROM users LIMIT 15;
```

In general it's a good idea to only select the columns you need. Some of these databases can have 50+ columns.
## Where
---
Fetching Let's find specific records:

```sql
SELECT username, email, user_id FROM users WHERE user_id=150;
SELECT username, email, user_id FROM users WHERE last_login IS NULL LIMIT 10;
SELECT username, email, user_id, created_on FROM users WHERE last_login IS NULL AND created_on < NOW() - interval '6 months'  LIMIT 10;
```
## Ordering
---
What if wanted to find the oldest users? Let's use a sort.

```sql
SELECT user_id, email, created_on FROM users ORDER BY created_on LIMIT 5;
```

Use `DESC` to get newest users.
## Counting
---
To get total amount of users from a table:

```sql
SELECT COUNT(*) FROM users;
```

Use `DISTINCT` in combination with a column to find out how many unique not null values there are.
## Updating
---
Let's say user_id 1 logged in. We'd need to go into their record and update their last_login. Here's how we'd do that.

```sql
UPDATE users SET last_login = NOW() WHERE user_id = 1 RETURNING *;
```

`RETURNING *` is optional and it specifies whether to return the updated records or not.

## Deleting
---
This works as you would expect based on what we've done before.

```sql
DELETE FROM users WHERE user_id = 1000;
```

# Complex Queries
---
Usually in the real world data is related to one another. How do we represent that record from one table has specific kind of relationship with data from another table? **Using foreign keys!**
## Foreign Keys
---
Let's jot down all of our schemas for our users, boards, and comments. We've already done this by executing the sql commands from the file above, and we can see that by getting information about specific table with  `\d table_name`.

```sql
CREATE TABLE users (
  user_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  username VARCHAR ( 25 ) UNIQUE NOT NULL,
  email VARCHAR ( 50 ) UNIQUE NOT NULL,
  full_name VARCHAR ( 100 ) NOT NULL,
  last_login TIMESTAMP,
  created_on TIMESTAMP NOT NULL
);

CREATE TABLE boards (
  board_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  board_name VARCHAR ( 50 ) UNIQUE NOT NULL,
  board_description TEXT NOT NULL
);

CREATE TABLE comments (
  comment_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  user_id INT REFERENCES users(user_id) ON DELETE CASCADE,
  board_id INT REFERENCES boards(board_id) ON DELETE CASCADE,
  comment TEXT NOT NULL,
  time TIMESTAMP
);
```

`ON DELETE CASCADE` means that when a user / board is deleted, comments left by that user / board will be deleted as well. We can specify also:
- `ON DELETE SET NULL` -> We still save the comment
- `ON DELETE NO ACTION` -> This is the default and it will not allow deleting the user if it's referencing data from another table.
## JOINs
---
So a user goes to our app and leaves a comment on a specific board. What if we want to get all comments by users for a specific board and display it on UI? We could do something like:

```sql
SELECT comment_id, user_id, LEFT(comment, 20) AS preview FROM comments WHERE board_id = 39;
```

- Two new things here. The `LEFT` function will return the first X characters of a string (as you can guess, RIGHT returns the last X characters). We're doing this because this hard to read otherwise in the command line.
- The `AS` keyword lets you rename how the string is projected. If we don't use AS here, the string will be returned under they key `left` which doesn't make sense.

We'll get something back like this:

```text
 comment_id | user_id |       preview
------------+---------+----------------------
         63 |     858 | Maecenas tristique,
        358 |     876 | Mauris enim leo, rho
        429 |     789 | Maecenas ut massa qu
        463 |     925 | Phasellus sit amet e
        485 |     112 | Maecenas tristique,
        540 |     588 | Nullam porttitor lac
        545 |     587 | Praesent id massa id
        972 |     998 | Aenean lectus. Pelle
(8 rows)
```

But in reality getting comment_id and user_id doesn't mean that much on UI. We need to return username perhaps but that doesn't live in the comments table, that exists in the users table. So how do we connect those together? The answer is using joins.

```sql
SELECT
  comment_id, comments.user_id, username, time, LEFT(comment, 20) AS preview
FROM
  comments
INNER JOIN
  users
ON
  comments.user_id = users.user_id
WHERE
  board_id = 39;
```

Magic! The big key here is the `INNER JOIN` which allows us to match up all the keys from one table to another. We do that in `ON` clause where we say user_ids match is where you can _join_ together those records into one record.

>[!tip] NATURAL JOIN
>If user_id column has the same name inside users and comments table then we can use NATURAL JOIN to join them together without writing the ON clause.

We would do something like this:

```sql
SELECT
  comment_id, comments.user_id, username, time, LEFT(comment, 20) AS preview
FROM
  comments
NATURAL INNER JOIN
  users
WHERE
  board_id = 39;
```

Let's talk about `INNER` for a second. There are multiple kinds of JOINs. INNER is a good one start with. It says "find where user_ids match. If you find a record where the user_id exists in one but not in the other, omit it in the results." This isn't a particularly useful distinction for us right now because all user_ids will exist in users and we're assured of that due to the foreign key restraints we used. However if a comment had a user_id that didn't exist, it would omit that comment in the results.

![[SQL JOINS.png]]

A `LEFT JOIN` would say "if a comment has a user_id that doesn't exist, include it anyway." A `RIGHT JOIN` wouldn't make much sense here but it would include users even if they didn't have a comment on that board.

We can also an `OUTER JOIN` which would be everything that _doesn't match_. In our database, that would be nothing because we're guaranteed everything has match due to our constraints.

You can also do a `FULL OUTER JOIN` which says just include everything. If it doesn't have a match from either side, include it. If it does have a match, include it.

Another rarely useful join is the `CROSS JOIN`. This gives the *Cartesian product* of the two tables which can be enormous. A Cartesian product would be every row matched with every other row in the other table. If you have A, B, and C in one table with D and E in the other, your CROSS JOIN would be AD, AE, BD, BE, CD, an CE. If you do a cross join between two tables with 1,000 rows each, you'd get 1,000,000 records back.

Tables can also be self-joined. Imagine you have a table of employees and one of the fields is direct_reports which contains employee_ids of employees that report the original employee. You could do a SELF JOIN to get the information for the reports.

**95% of the things you really need is covered by INNER and LEFT joins.**
## Group by
---
This is useful in making reports. Let's say that we want to get the top 10 written boards. We need to group all the comments by board id and then count them.

```sql
SELECT
  boards.board_name, COUNT(*) AS comment_count
FROM
  comments
INNER JOIN
  boards
ON
  boards.board_id = comments.board_id
GROUP BY
  boards.board_name
ORDER BY
  comment_count DESC
LIMIT 10;
```

# JSON in PostgreSQL
---
Sometimes you have data that just doesn't have a nice schema to it. If you tried to fit it into a table database like PostgreSQL, you would end having very generic field names that would have to be interprepted by code or you'd end up with multiple tables to be able describe different schemas. This is one place where document based databases like MongoDB really shine; their schemaless database works really well in these situations.

However PostgreSQL has a magic superpower here: the **JSONB** data type. This allows you to put JSONB objects into a column and then you can use SQL to query those objects.

For example: you want to add a new feature that allows users to do rich content embeds in your message board. For starters they'll be able to embed polls, images, and videos but you can imagine growing that in the future so they can embed tweets, documents, and other things we haven't dreamed up yet.

This would be possible to model with a normal schema but it'd come out pretty ugly and hard to understand, and it's impossible to anticipate all our future growth plans now. This is where the `JSONB` data type is going to really shine. These are the queries we ran to create them.

```sql
CREATE TABLE rich_content (
  content_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  comment_id INT REFERENCES comments(comment_id) ON DELETE CASCADE,
  content JSONB NOT NULL
);

INSERT INTO rich_content
  (comment_id, content)
VALUES
  (63, '{ "type": "poll", "question": "What is your favorite color?", "options": ["blue", "red", "green", "yellow"] }'),
  (358, '{ "type": "video", "url": "https://youtu.be/dQw4w9WgXcQ", "dimensions": { "height": 1080, "width": 1920 }}'),
  (358, '{ "type": "poll", "question": "Is this your favorite video?", "options": ["yes", "no", "oh you"] }'),
  (410, '{ "type": "image", "url": "https://btholt.github.io/complete-intro-to-linux-and-the-cli/WORDMARK-Small.png", "dimensions": { "height": 400, "width": 1084 }}'),
  (485, '{ "type": "image", "url": "https://btholt.github.io/complete-intro-to-linux-and-the-cli/HEADER.png", "dimensions": { "height": 237 , "width": 3301 }}');
```

- The `JSONB` data type allows us to insert JSON objects to be queried later.
- PostgreSQL won't let you insert invalid JSON so it does validate it for you.
- Notice you can have as much nesting as you want. Any valid JSON is valid here.

So let's do some querying! We're going to use two new symbols, `->` and `->>`. 
- The `->` means "give me back the JSON object". The return type will be a JSON object, even if it's just a string. It's basically a black box to PostgreSQL and it treats all JSON the same, whether it's an array, object, or just a string.
- The `->>` means "give me this back as a string."

Find the all the different types of rich content.

```sql
SELECT content -> 'type' FROM rich_content;
```

We'll get something like this:

```md
## ?column?

"poll"
"video"
"poll"
"image"
"image"
```

It repeats poll and image twice because there's two of those. What if we just wanted the distinct options and no repeats? We could use `SELECT DISTINCT`. SELECT DISTINCT will deduplicate your results for you. We could try this (this will throw an error):

```sql
SELECT DISTINCT content -> 'type' FROM rich_content;
```

PostgreSQL doesn't actually know what data type it's going to get back from JSON so it refuses to do any sort of comparisons with the results. We have to tell PostgreSQL "this is definitely going to be text".

```sql
SELECT DISTINCT CAST(content -> 'type' AS TEXT) FROM rich_content;
```

However this is a ton easier if you just `->>`:

```sql
SELECT DISTINCT content ->> 'type' FROM rich_content;
```

That's the key difference between `->` and `->>`.

What if we wanted to only query for polls?

```sql
SELECT content ->> 'type' AS content_type, comment_id FROM rich_content WHERE content ->> 'type' = 'poll';
```

Unfortunately due to the execution order (WHERE happens before SELECT) you can't reference content_type and have to give it the full expression.

Okay, last one. What if we wanted to find all the widths and heights?

```sql
SELECT
  content -> 'dimensions' ->> 'height' AS height,
  content -> 'dimensions' ->> 'width' AS width,
  comment_id
FROM
  rich_content;
```

You can use the `->` and `->>` multiple times to look at nested values. This will give you back the ones that don't have heights and widths too. To filter those out just do:

```sql
SELECT
  content -> 'dimensions' ->> 'height' AS height,
  content -> 'dimensions' ->> 'width' AS width,
  comment_id
FROM
  rich_content
WHERE
  content -> 'dimensions' IS NOT NULL;
```

# Indexing
---
PostgresSQL supports [[Indexing|indexing]]. Before executing a query we can ask PostgreSQL to explain how it's going to perform the query:

```sql
EXPLAIN SELECT comment_id, user_id, time, LEFT(comment, 20) FROM comments WHERE board_id = 39 ORDER BY time DESC LIMIT 40;
```

This part should break your heart: `Seq Scan on comments`. This means it's looking at every comment in the table to find the answer. This is a place we'd need an index to prevent this. Let's make an index on board_ids to speed this up.

```sql
CREATE INDEX ON comments (board_id);
EXPLAIN SELECT comment_id, user_id, time, LEFT(comment, 20) FROM comments WHERE board_id = 39 ORDER BY time DESC LIMIT 40; -- run again
```

> [!danger]+ Caution
> Creating an index on production db can be pretty expensive, it can cause downtime.

If you're looking the EXPLAIN again, you'll see it does a `Bitmap Heap Scan` instead of a Seq Scan. Much better!

Let's do one more; all users should have a unique username. Let's ensure that with a unique index.

```sql
CREATE UNIQUE INDEX username_idx ON users (username);
INSERT INTO users (username, email, full_name, created_on) VALUES ('aaizikovj', 'lol@example.com', 'Brian Holt', NOW()); -- this will fail
```

- The `username_idx` is just a name for the index. You can call it whatever you want.
- Try inserting a duplicate username. The query will fail.
- A pleasant byproduct is that this field is now indexed so you can easily search it.

PostgreSQL has [more types of indexes](https://www.postgresql.org/docs/13.0/indexes.html). To sum them up, there are these types of indexes:
B-tree, Hash, GiST, SP-GiST, GIN, BRIN, and the extension bloom.

```bash
docker run -d -p 8080:8080 \
  -e HASURA_GRAPHQL_DATABASE_URL=postgres://username:password@hostname:port/dbname \
  -e HASURA_GRAPHQL_ENABLE_CONSOLE=true \
  hasura/graphql-engine:latest
```

# PostgreSQL Ops
---
Being a database administrator is hard, so this section is just an overview. [Take a look at the documentation](https://www.postgresql.org/docs/13/admin.html). 
## Primary & Secondary / Master & Slave
---
PostgreSQL has a replication strategy that allows you to write to a primary server (and read from it) as well as reading from the secondaries.
## User Accounts
---
When creating accounts for your servers to connect to PostgreSQL in production, make multiple accounts. Each account should only have access to what it needs and nothing else. Do you have one service that only works with users and a different one that only works with comments? Give them scoped access to tables and databases. Never use the superuser (like we just did) in production.