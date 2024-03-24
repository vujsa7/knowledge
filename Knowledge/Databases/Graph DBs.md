# Introduction
---
Graph databases, as the name suggest, are wonderful for defining data that has complex relationships with other pieces of data in your database. So if your application has data that is driven by relationships (*for example social media application*), graph databases are a great choice for you.

Popular choices for graph databases:
- [[Neo4j]]
- ArrangoDB
- Dgraph (has direct support with [[GraphQL]])
- TigerGraph
- OrientDB
- Amazon Neptune
- GraphDB
- DataStax
- CosmosDB
and more...
# Terminology
---
### Nodes / Entities
A node in a graph database represents a thing, an entity. In movies database for example, it would either be a movie or a person. These nodes will have certain labels to denote those. Label defines what type of node this is. In movie database labels would probably be Actor and Director.

### Relationships / Edges
Nodes can have relationships between each other. Companies EMPLOYED people or CONTRACTED with them, people MANAGED other people or WERE_PEERS with each other. Many of these relationships have a direction liked MANAGED, other are more bidirectional like WERE_PEERS. In Neo4j every relationship has a direction but sometimes you can just ignore it like in the case of WERE_PEERS. Other times you need two relationships to between nodes to describe adequately the graph. A good example is if you were describing who loved who in a play: if Taylor loved Sue, it wouldn't mean that Sue also loved Taylor. You'd need a relationship in both directions.

Neo4j calls these connections relationships but know that lots of graphs will call these edges.

Nodes can have a relationship with themselves. If Taylor loves herself and we want to express that, she could have a self-referential relationship of love.