# 23. Consistent Hashing

[← 22. Bloom Filters](../basics/22-bloom-filters.md) · [🏠 Index](../README.md) · [24. Introduction to Big Data Processing →](../basics/24-introduction-to-big-data-processing.md)

---

One of the most amazing and popular algorithms out there, and the only problem it solves is **Data Ownership**.
We first understand Consistent Hashing and then look into practical implementation.

### Hash Based Ownership
Say, we have a load balancer, and when a request comes in, it uses hash of access token to decide which backend server to forward it to.

```
server = fn(r.id) % 3        // r.id → access token in the request
SHA256(u1) = x
server = x % 3 = i           // SHA-256 or SHA-128 are popular choices
```
(Users u1, u2 → Load Balancer → B.Server 0 / 1 / 2)

> **Note:** Hashing logic is NOT a 'service', but just simple code running in the load balancer's code ↳ more of a function.

If one of the servers is taken down, then the routing function changes to `fn(r.id) % 2` — now the requests will be evenly distributed between remaining two servers. So, **no hiccups!!**

### Load Balancers are stateless
Because the API servers are stateless, which means every server is **equally** capable of handling requests, it does not matter if a request that was handled by server 1 now starts going to server 0.

This is precisely why we see **'Hash Based Routing'** as one of the most common ways of routing for stateless backends, e.g. Load Balancer + API servers.

### Hash Based 'routing' (ownership) for distributed storage
Instead of stateless API requests, say we are having a **stateful** distributed storage:
1. nodes store the data
2. proxy forwards the request to a node
3. end user/client talks to proxy

Picking which node "owns" the data depends on the **Hash Based Ownership** (`fn(k) % 3`).

Say we want to store 6 keys k1…k6:

| key | `fn(k) % 3` | | key | `fn(k) % 3` |
|---|---|---|---|---|
| k1 | 2 | | k4 | 2 |
| k2 | 0 | | k5 | 1 |
| k3 | 1 | | k6 | 0 |

→ node0: k2, k6 · node1: k3, k5 · node2: k1, k4

*Okay to do it when workload is stateless like API load balancer.*

**Challenge:** if a storage node is removed or added, the proxy cannot just forward the request to any arbitrary node because it won't have the data.

### Repartitioning
When the number of nodes changes, the proxy will change the routing function and it would now become `fn(k) % 2`. Now, all the keys would need to be re-evaluated and moved to the correct node ↳ **involves a lot of data transfer**.

| key | `% 3` | `% 2` |
|---|---|---|
| k1 | 2 | 0 |
| k2 | 0 | 1 |
| k3 | 1 | 1 |
| k4 | 2 | 0 |
| k5 | 1 | 1 |
| k6 | 0 | 0 |

→ node0: k1, k4, k6 · node1: k2, k3, k5

**Can we minimize the data movement?** This is where consistent hashing comes in.

- **What consistent hashing is?** An algorithm that helps in determining **data ownership** (who owns this data).
- **What consistent hashing is not?** It will not do data transfer for us; it is not a "service" in itself.

### The Hash Ring
Hash function (SHA-128) → range of `[0, 2^128)`. Given hash functions are cyclic, we can visualize it as a **ring of integers** (simple visualization: `[0 to 15]`).

**Every node occupies one slot in the ring**; the slot is calculated by passing the node's IP to the hash function.
- node0 → slot 3
- node1 → slot 12
- node2 → slot 8

The ring can be modelled as a simple array and be part of the proxy.

**Here's how the ownership is determined:** hash the key, then walk to the **node to the right** (clockwise):
- `k1 → hash → 0 → node to right → node 0` (slot 3)
- `k2 → hash → 10 → node to right → node 1` (slot 12)

Consistent Hashing ring will only tell you the node that **"should"** own it.

### Scaling up
When we add a new node to the "ring", say node3 hashes to slot 1. The keys that hashed between slot 12 and slot 1 will now be "owned" by node3 instead of node0. Other keys continue to remain at the respective nodes ↳ **Minimal Data Movement**.

Operationally, you just have to:
1. snapshot node 0
2. create node 3
3. delete unwanted keys


Slots are numbered clockwise, starting from 0 at the top.

```
                              ◆ 0 (k1)
                     15 ○           ○ 1 ← node3
                14 ○                     ○ 2 

            13 ○                             ● 3  ← node0


   node1 → 12 ●                               ○ 4


            11 ○                             ○ 5

           k2 → 10 ◆                     ○ 6
                      9 ○           ○ 7
                              ● 8  ← node2
```

**Legend:** `●` node  ·  `◆` key  ·  `○` empty slot

## Node Slot Mapping

| Node  | Slot |
|-------|------|
| node0 | 3    |
| node2 | 8    |
| node1 | 12   |

## Key Ownership

| Key | Hash → Position | Walk clockwise | Owner           |
|-----|-----------------|----------------|-----------------|
| k1  | 0               | 0 → 1 → 2 → 3  | **node0** (slot 3)  |
| k2  | 10              | 10 → 11 → 12   | **node1** (slot 12) |

## How It Works

1. Hash each node (IP) to get its slot in the ring.
2. Hash each key to get its position in the ring.
3. Walk clockwise to the next node.
4. That node is the owner of the key.

> **Key Takeaway**
>
> The consistent hashing ring only tells you which node should own the key.
> It does not store the actual key or data.


### Scaling Down
Say we scale down and remove node 0. All the keys that were owned by node 0 will now be owned by node 2 (next in the ring) → **Minimal Data Transfer**.

Operationally:
1. copy everything from node 0 to the next node
2. remove node 0 from the ring
3. delete node 0

### Exercise
1. Understand consistent hashing
2. Read the blog https://arpitbhayani.me/blogs/consistent-hashing
3. Implement it in your favourite programming language — the implementation just contains **2 arrays + Binary Search**

---

[← 22. Bloom Filters](../basics/22-bloom-filters.md) · [🏠 Index](../README.md) · [24. Introduction to Big Data Processing →](../basics/24-introduction-to-big-data-processing.md)
