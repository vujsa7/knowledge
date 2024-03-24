# What is Hasura?
---
[Hasura](https://hasura.io/) is a tool too cool to not talk about. It's a server you will put inside your cluster of servers that allows you to treat your database like it was a **GraphQL data source**. If you don't know GraphQL, it's a really nice way to write queries against any data source.

# How to start?
---
To start it, we can use official docker image. We just need to specify which database we want to connect to. Here I am using a [[PostgreSQL]] database from another docker container.

```bash
docker run -d -p 8080:8080 \
  -e HASURA_GRAPHQL_DATABASE_URL=postgresql://postgres:mysecretpassword@<host_of_postgresql_db>:5432/<database_name> \
  -e HASURA_GRAPHQL_ENABLE_CONSOLE=true --name=my-hasura --rm \
  hasura/graphql-engine:latest
```

>[!tip] Find host of docker container
>To find host ip address of a running docker container we can do with:
>`docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container`

Once you're running, head to [http://localhost:8080](http://localhost:8080/) to play around with it. Specifically you'll want to click the data tab at the top and all tables to it then head back to the GraphiQL tab to run some queries.

We can query our database easily like this:

```graphql
{
  boards(where: { board_id: { _eq: 39 } }) {
    board_id
    board_name
    board_description
  }
  comments(where: { board_id: { _eq: 39 } }) {
    board_id
    comment
    comment_id
    time
    user_id
  }
}
```

# When to use Hasura?
---
There is a great [article](https://dev.to/omaiboroda/guide-to-side-effects-in-hasura-5a1g#:~:text=Hasura%20heavily%20relies%20on%20the,with%20Postgres%20functions%20and%20triggers.&text=Pros%3A%20powerful%20flexibility%2C%20database%2D,3rd%2Dparty%20API%20is%20impossible.) listing all pros and cons and how you would tackle some issues that come with using Hasura.

**When to use it:**
- You want to build something fast (out of the box functionality)
- You are great with SQL
- You are firm believer that you don't need an ORM
- You are learning GraphQL and just want to use it to learn
- Good for vizualizations

# Alternatives
---
- [WunderGraph](https://wundergraph.com/)