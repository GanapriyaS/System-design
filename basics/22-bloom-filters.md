# 22. Bloom Filters

[← 21. Blob Storage and S3](../basics/21-blob-storage-and-s3.md) · [🏠 Index](../README.md) · [23. Consistent Hashing →](../basics/23-consistent-hashing.md)

---

Bloom Filters are **approximate data structures** (also probabilistic data structure) that say with **100% certainty that an element does not belong to a set**.

**For example:** Instagram wants to recommend reels, but it does not want to recommend something you saw already.

**Naive way:** keep track of everything that a user saw in a set:
`u1 → <p1, p4, p7, p1024, p1056>` (user → posts he/she saw)

People watch 100s of reels every single day. Over time the set of all posts watched by a user will be huge!! So what?
To check the existence of a key in set, we have to load the entire set in memory and then check → **super expensive and time consuming**.

So, can we do better?

**Key Insight:** Once something is "watched", you cannot take it back ↳ once a post/reel is watched by you, we do not take it out of the set!! This means, storing actual data is not worth it. This is the concept over which the Bloom Filters are set up.

### Bloom Filters
Filter ≈ bit array (e.g. we take an 8-bit array, indices 0–7).
Bit array not byte array.

Words I want to put in:
- `apple → f(apple) % 8 → 2`
- `ball → f(ball) % 8 → 6`
- `cat → f(cat) % 8 → 2`

Bit array after insertion: positions **2** and **6** are set to 1.

Lookups:
- `dog → f(dog) % 8 → 5` → `filter[5] = 0` → **dog not present!!**
- `elephant → f(elephant) % 8 → 6` → `filter[6] = 1` → **elephant may be present**

Thus we see, when Bloom Filter says **No → we can be sure**. But when it says **yes → we still need to check**.

### Space Efficiency
Bloom Filters take significantly less space to hold the information (because it does not store keys) and are very efficient in checking existence (just an array lookup).

### False Positivity Rate
As the number of keys we put in Bloom Filter increases, the false positivity rate increases ↳ says key is present but in reality it is not.

For example, as word increases in 8-bit all will become 1 and always say it is present.

1kb - 1024bytes - 1024 * 8 bits - 8196 bits

Hence, when #keys increases:
1. we have to re-create bloom filter with larger size and populate the keys again as hash function changed.
2. estimate the max keys and provision a large one to start with

### Practical Bloom Filter
We do not have to re-implement Bloom Filter. There are libraries in every single language. **Redis** has it as one of its core features (nowadays, mostly people go for this) but in redis mention size as 1KB is enough mostly.

### Practical Application
Use it whenever:
- you insert but not remove data
- you need a NO with 100% certainty
- having false positive is okay

e.g. Medium recommendation, feed generation, web crawler, Tinder feed.

There is simple formula for calculating bloom filter array size, words insert so on.

### Exercise
1. Setup Redis locally
2. Read Redis's documentation on Bloom Filter
3. Write small code to play around with it (understand how to use it)
4. Try to get one false positive result
abloom (repo)

---

[← 21. Blob Storage and S3](../basics/21-blob-storage-and-s3.md) · [🏠 Index](../README.md) · [23. Consistent Hashing →](../basics/23-consistent-hashing.md)
