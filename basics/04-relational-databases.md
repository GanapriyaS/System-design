# 4. Relational Databases

[← 3. Knowing You Have Built a Good System](../basics/03-knowing-you-have-built-a-good-system.md) · [🏠 Index](../README.md) · [5. Database Isolation Levels →](../basics/05-database-isolation-levels.md)

---

Databases are the most critical component of any system. They make or break a system.

Data is stored & represented in **rows** and **columns**.

### History of relational databases
Everything "revolutionary" (computers, internet, blockchain) starts with **Financial Applications!**

Computers first did "accounting" → ledgers (excel and spreadsheets) → Rows & Columns. Databases were developed to support accounting. Hence, key properties were:
1. Data consistency
2. Data durability
3. Data integrity
4. Constraints
5. Everything in one place

Because of these reasons, relational databases provide **"Transactions"!**

**ACID** → **A**tomicity, **C**onsistency, **I**solation, **D**urability

### Atomicity
All statements within a transaction take effect, or none.

e.g. *publish a post and increase total posts count*
```sql
START TRANSACTION;
  INSERT INTO posts VALUES (....);
  UPDATE stats SET total_posts = total_posts + 1
    WHERE user_id = 100;
COMMIT;
```

### Consistency
Data will never go incorrect, no matter what — **constraints, cascades, triggers**.

- Money of one person should not transfer to another person.
- Triggers can be like update post count when transaction have insert post but usually business logic in transaction is preferred than triggers as it database related topic.

e.g. Foreign key checks do not allow you to **delete** parent if child exists (*can be tuned).

You have the necessary tools to ensure that your data never goes inconsistent:
`total_posts` = total entries in `posts` table for user!

### Durability
When a transaction commits, the changes outlive outage if disk is there.

### Isolation
When multiple transactions are executing parallelly, the **isolation level** determines how much changes of one transaction are visible to the other.

> Txn1 and Txn2 run in parallel — should changes done at a line in Txn1 be visible to Txn2 **before** Txn1 commits?

### Remember
You pick relational databases for **relations** and **ACID**.

### Exercise
1. Setup a SQL database (MySQL or PostgreSQL)
2. Create a schema for a social network — users, posts, profile, photos, following, etc. (define relationships)
3. Insert data in (users & profile) in one transaction

---

[← 3. Knowing You Have Built a Good System](../basics/03-knowing-you-have-built-a-good-system.md) · [🏠 Index](../README.md) · [5. Database Isolation Levels →](../basics/05-database-isolation-levels.md)
