# 3. Knowing You Have Built a Good System

[← 2. How to Approach System Design?](../basics/02-how-to-approach-system-design.md) · [🏠 Index](../README.md) · [4. Relational Databases →](../basics/04-relational-databases.md)

---

Every system is "**infinitely**" buildable, and hence **knowing when to stop the evolution** is important. Here are some pointers that will help you:

1. **You broke your system into components** (e.g. Feed → Webserver, Generator, Aggregator, Database).
2. **Every component has a clear (exclusive) set of responsibilities**
   - *Feed Webserver* → serves feed over HTTP
   - *Feed Generator* → pulls data from multiple services (posts, friends, recommendations) and puts them in DB ↳ *candidate feed items*
   - *Feed Aggregator* → combines candidate items fetched by generator, filters out redundant, ranks and creates a final consumable feed
3. **For each component, you've slight technical details figured out**
   1. Database and Caching
   2. Scaling & Fault Tolerance
   3. Async processing (Delegation)
   4. Communication
4. **Each component (in isolation) is**
   - **scalable** → horizontally scalable
   - **fault tolerant** → plan for recovery in case of a failure (mostly data*) ↳ to a stable state
   - **available** → component functions even when some component "fails"

This is precisely how we would tackle every single system — **structured and detailed**.

---

[← 2. How to Approach System Design?](../basics/02-how-to-approach-system-design.md) · [🏠 Index](../README.md) · [4. Relational Databases →](../basics/04-relational-databases.md)
