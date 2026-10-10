# 8. Non-Relational Databases

[← 7. Sharding and Partitioning](../basics/07-sharding-and-partitioning.md) · [🏠 Index](../README.md) · [9. Picking the Right Database →](../basics/09-picking-the-right-database.md)

---

It is a very broad generalization of databases that are non-relational (MySQL, PostgreSQL, etc. are relational).
**But this does not mean all non-relational databases are similar.**

### What makes non-relational databases interesting?
Most non-relational databases **shard out-of-the-box!!** → Horizontal Scalability.

We talk about 3 most important types of NoSQL databases:

### Document DBs — MongoDB, Elasticsearch
- Mostly JSON based
- Supports complex queries → *almost like relational (SQL) databases*
- Partial updates to documents possible
- Closest to relational database

```json
{
  "user_id": "...",
  "total_posts": 270
}
```
↳ can do `total_posts += 1` **without re-writing the entire document**.

- Use case: in-app notification service, catalog service - ecommerce platform if seller want to add data and what products we sell not in broad case (because can store unstructured data) 

### Key Value Stores — Redis, DynamoDB, Aerospike
- Extremely simple databases
- Limited functionalities (`GET(k)`, `PUT(k, v)`, `DEL(k)`)
- Meant for key-based access pattern (most of the use cases)
- Does not support complex queries (aggregations)
- Can be heavily sharded and partitioned
- Use case: profile data, order data, auth data, messages, etc.

\* You can use relational databases and document DBs as KV stores.

### Graph Databases — Neo4j, Neptune, DGraph
\* What if our graph data structure had a database?
- It stores data that are represented as **nodes, edges, and relations**
  - e.g. `A —FOLLOWS→ B`, `Arpit —BOUGHT→ iPad`
- Great for running complex graph algorithms
- Powerful to model Social Networks, Recommendations & Fraud Detection

### Exercise
On your local machine, spin up MongoDB, Redis and Neo4j and play around with them.

---

[← 7. Sharding and Partitioning](../basics/07-sharding-and-partitioning.md) · [🏠 Index](../README.md) · [9. Picking the Right Database →](../basics/09-picking-the-right-database.md)
