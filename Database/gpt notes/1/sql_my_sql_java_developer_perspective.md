# SQL & MySQL – 10+ Years Java Developer Perspective

> **Audience**: Senior Java Developers (10+ years)
>
> **Mindset**: Production-first, performance-aware, failure-tolerant
>
> **Focus**: How experienced Java engineers *think* about SQL/MySQL in real systems

---

## 1. Database Is a Concurrency System (Not Just Storage)

A senior Java developer views a database as:
- A **concurrent shared state machine**
- With **locking, isolation, and contention**
- Backed by **durability guarantees**

Mental mapping:

| Java | MySQL |
|----|------|
| synchronized | row locks |
| ReentrantLock | explicit locks |
| volatile | MVCC visibility |
| Atomicity | transactions |

---

## 2. Schema Design Beats ORM Tricks

After years in production:
- No ORM optimization can fix a bad schema
- Indexing strategy matters more than Java tuning

Golden rule:
```
Bad schema → permanent pain
Good schema → average code still works
```

---

## 3. Primary Key Choice Is a Long-Term Decision

A senior asks:
- Insert pattern?
- Future sharding?
- Secondary index size?
- Write amplification?

Guidelines:
- Prefer **monotonic primary keys** (AUTO_INCREMENT / time-based)
- Avoid random UUIDs in OLTP
- If UUID required → use time-ordered UUIDs

Why:
- Clustered index defines physical row order
- Random PKs cause page splits & fragmentation

---

## 4. Indexes Are a Write-Time Tax

Every index:
- Speeds reads
- Slows writes
- Increases lock duration
- Increases redo/undo log pressure

Senior mindset:
> “Every index must justify its existence.”

---

## 5. SELECT * Is a Code Smell

Why seniors avoid it:
- Breaks covering indexes
- Pulls unused data
- Wastes network & memory

Preferred:
```sql
SELECT required_columns_only
```

---

## 6. Transactions Must Be Small

Junior pattern:
```java
@Transactional
public void processOrder() {
   // large method
}
```

Senior pattern:
- Short-lived transactions
- No network calls inside
- No user waits
- No loops with DB calls

Rule of thumb:
```
Transaction duration < milliseconds
```

---

## 7. Isolation Levels Are Business Decisions

Senior engineers know:
- MySQL default = REPEATABLE READ
- Phantom prevention via next-key locks
- Higher isolation → lower concurrency

Isolation is chosen based on:
- Business correctness
- SLA
- Contention patterns

---

## 8. Locks Explain “Random Slowness”

Common production issues:
- API slow only sometimes
- Insert hangs randomly

Root causes:
- Gap locks
- Missing indexes
- Long-running transactions
- Lock order inversion

Senior debugging starts with:
```sql
SHOW ENGINE INNODB STATUS
EXPLAIN ANALYZE
```

---

## 9. Pagination Separates Juniors from Seniors

Junior approach:
```sql
LIMIT 100000, 20
```

Senior approach:
```sql
WHERE id > last_seen_id
LIMIT 20
```

Why:
- Offset pagination = O(N)
- Keyset pagination = O(1)

---

## 10. Scaling Is Gradual, Not Magical

Senior scaling order:
1. Fix queries & indexes
2. Vertical scaling
3. Read replicas
4. Caching
5. Sharding (last resort)

Truth:
> “Sharding is an application problem, not a database feature.”

---

## 11. ORM Is a Tool, Not a Strategy

Hibernate/JPA:
- Excellent for CRUD
- Dangerous for batch jobs
- Poor for reporting queries

Senior approach:
- ORM for simple flows
- Native SQL for hot paths
- Explicit indexes always

---

## 12. Database Failures Are Expected

A senior designs assuming:
- Replication lag
- Deadlocks
- Failovers
- Partial outages

So systems include:
- Retries
- Idempotency
- Read-after-write handling

---

## 13. “Database Is Slow” Usually Means “Query Is Bad”

Experience shows:
- 90% → bad query / missing index
- 9% → schema issue
- 1% → actual DB limit

Senior workflow:
1. EXPLAIN ANALYZE
2. Index fix
3. Query rewrite
4. Only then Java tuning

---

## 14. Backups You Didn’t Test Don’t Exist

Senior rules:
- Restore must be tested
- Know RPO & RTO
- Validate point-in-time recovery

If restore wasn’t tested:
> Backup does not exist

---

## 15. Interview-Grade Summary (10+ Years)

> “I focus on schema correctness, predictable access patterns, proper indexing, short transactions, and incremental scaling using replicas before considering sharding.”

---

## 16. Senior Mental Model

```
Correctness → Schema
Performance → Indexes + Queries
Scalability → Replication + Design
Reliability → Backups + Transactions
```

---

**Next Topics (Optional)**
- MongoDB from Java POV
- Cosmos DB (SQL & Mongo API)
- MySQL vs NoSQL decision framework
- Real production failure case studies

