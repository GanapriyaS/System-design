# 18. Data Redundancy and Recovery

[← 17. Circuit Breakers](../basics/17-circuit-breakers.md) · [🏠 Index](../README.md) · [19. Leader Election for Auto-Recovery →](../basics/19-leader-election-for-auto-recovery.md)

---

API servers are **"stateless"** but databases are **"stateful"**.
- API servers going down is fine because a new one will be spun up almost instantly. API server gets request and it does not matter which one handles it.
- Databases going down is catastrophic; almost always an outage!! **Worst:** Disk crash leading to loss of data!

A good system always takes care of such catastrophic situations.

\* The only way to protect ourselves against loss of data is to **create multiple copies of it → Data Redundancy**.

Redundancy can be implemented at row/document level, table level or DB level. Redundant data can be stored on a different table, different DB or different region.

### Backup and Restore
- Daily backup of data (incremental)
- Weekly complete backup
- Storing one copy across region (Disaster recovery)
- When something goes wrong, just restore the last backup
- Almost always the easiest thing to do

### Continuous Redundancy
Setup replica of the database and writes go to both DBs (sync/async):
1. API server writing to both databases
2. API writes to one and is copied to other asynchronously

\* If the main database goes down, replica (sync with main db always) can take its place (almost instantly).
*Note: this replica may just be a stand-by and not serve any production traffic.*

### Exercise
1. Setup replication b/w two MySQL servers
2. See how you can backup a MySQL DB
3. See how you can restore the database

---

[← 17. Circuit Breakers](../basics/17-circuit-breakers.md) · [🏠 Index](../README.md) · [19. Leader Election for Auto-Recovery →](../basics/19-leader-election-for-auto-recovery.md)
