# 11. Populating and Scaling Caches

[← 10. Understand Caching](../basics/10-understand-caching.md) · [🏠 Index](../README.md) · [12. Caching at Different Levels →](../basics/12-caching-at-different-levels.md)

---

Cache sits between the API server and the database. There are two ways to populate the cache.

### Lazy population (most popular)
```
Read: first go to the cache
  if data exists → return data
  else:
      go to database / do heavy operation
      persist in the cache
      return data
```
\* Whenever we set something in cache, we set an **expiry**.

**Example: Caching Blogs** — fetching a blog from DB is expensive (multiple joins). Hence, when someone accesses it we fetch from DB and cache it on Redis. Subsequent requests are served from cache.

### Eager population
1. **Writes go to both database and cache in the same request call.**
   - Example: *live cricket score* — thousands of people are watching cricket score; you will be serving it from cache. So, why not update cache & DB at once and save the cache miss. (Commentator updates the score → writes to MySQL + Redis.) which means why to wait for key to get expire and fetch new value so user can get live updated score.
2. **Proactively push data to cache because you anticipate the need.**
   - Example: when a celebrity tweets/posts something — when an account with 100,000 followers posts something, proactively push it to the cache. We will anyway need it and we save a cache miss.
   Like if user open youtube see recommendations even old or new trending proactively pushed to cache. (asynchronous way)

### Scaling Cache
Cache is just like a database, hence scaling technique for a cache like Redis is similar to a regular database.
- **Vertical scaling** — make your cache bigger to handle more data/load.
- **Horizontal scaling – Replica (scaling reads)** — same data replicated across multiple nodes so that reads could scale. (API → Master / Replica)
- **Horizontal scaling – Sharding (scaling writes)** — data partitioned across multiple shards, so that writes could scale.
  - \* each shard can have a replica
  - \* shards are mutually exclusive
  (API → shards)

---

[← 10. Understand Caching](../basics/10-understand-caching.md) · [🏠 Index](../README.md) · [12. Caching at Different Levels →](../basics/12-caching-at-different-levels.md)
