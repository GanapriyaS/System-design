# 2. Designing a Rate Limiter

[← 1. Designing E-commerce Product Listing](../case_studies/01-designing-e-commerce-product-listing.md) · [🏠 Index](../README.md) · [3. Designing a Notification Service →](../case_studies/03-designing-a-notification-service.md)

---

> **Motive:** understanding that more boxes ≠ better system. Understanding trade-offs making scaling decisions.

Systems break down under tremendous load, and we need to ensure that doesn't happen.

Design a rate limiter that:
- limits the number of requests in a given period
- allows developers to configure threshold at a granular level
- does not add a massive additional overhead

### Rate Limiter
- First line of defence (write in middleware)
- Any incoming request is first consulted against rate limiter
  - if we are under limits, **go through**
  - otherwise, **reject with error (429)**

### Where does it fit?
- **Option A:** at the Proxy — proxy consults the rate limiter before forwarding to the service (\* request does not even hit the service)
- **Option B:** behind the LB — the service itself consults the rate limiter

### Dissecting Rate Limiter
Rate limiter needs to track #requests in a given period. Hence we need a database to hold the count, but which one?
1. for every incoming request, we would update the DB
2. for every incoming request, we would read from DB (aggregate)
3. we need ability to "clock" the time (expiry)

Say, we rate limit per user:
1. we store #requests in current period (`user_id → count`)
2. once the period is over we reset the counter

Resetting the counter ≈ expiring the key ↳ **KV store + Expiration → Redis** (also gives us fast in-mem writes + periodic persistence) -> fixed window algorithm ( allow to handle 'n' request in that window).

### Checking and updating (pick one)
1. We expose a service (HTTP endpoint) that talks to rate limiter database
2. We let the services directly talk to rate limiter DB
   - ↳ saves one network hop
   - ↳ but exposes critical internal details

We can make Rate Limiter having its own Load Balancer and a bunch of API servers (LB of rate limiter → backend servers of R.L. → Redis).
\* But this adds **substantial network hops**. Nothing wrong in this, just be aware of the trade-off.

To reduce the overhead of checking the Rate Limiter, we let proxy/service directly manipulate the rate limiter database (**saving 2 hops**). Rate limiter is added as a **library** holding all the business logic (embedded in proxy and service), talking directly to Redis.

### Scaling Rate Limiter
Given the services are directly talking to the Rate Limiter Database, **scaling rate limiter = scaling the database**.
1. Should we scale vertically? **Yes**
2. Should we add read replicas? **No!** traffic is not read heavy
3. Should we shard the database? **Yes**
   - ↳ any function in backend service
     - will extract `user_id` from token
     - check rate limiter DB
     - if good, then proceed
     - otherwise, block

*Note: the access is key based* — `user_id → count` (4+4 B), `ip → count` (16+4 B), `token → count` (16+4 B).

Hence we can easily **shard the database** to handle more load (library knows shards S1, S2, S3).

20 B × 100M users = 2000 MB ≈ 2 GB → **Compute is the concern, not data storage because of this we are sharding as many compute updates writes **

### Admin Console
It is better to have a small Admin Console (Frontend/Backend) that is used by the developers to reset counters and debug when things go wrong.
The service will also provide observability on infra and rate limiting DB like #keys, #requests blocked, CPU/mem load, etc. (very low throughput service).

### Exercise
Read, understand and then implement: **leaky bucket, fixed window and sliding window** algorithms using Redis.

---

[← 1. Designing E-commerce Product Listing](../case_studies/01-designing-e-commerce-product-listing.md) · [🏠 Index](../README.md) · [3. Designing a Notification Service →](../case_studies/03-designing-a-notification-service.md)
