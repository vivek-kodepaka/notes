# Database Interview Questions (7 Years Experience) — With Short Answers

---

## 1) Core Database Concepts

### 1. What is a database? Why do we need it?
A database persists and manages data with concurrency, querying, security, and recovery.

### 2. What is the relational model?
Data stored as relations (tables) containing tuples (rows). Relationships managed using keys.

### 3. Difference between DBMS and RDBMS?
- DBMS: general database system
- RDBMS: supports relational model, tables, SQL, constraints, joins

### 4. What are primary key and foreign key?
- PK: uniquely identifies row
- FK: references PK of another table for relationship integrity

### 5. What is normalization? Why do it?
Process of reducing redundancy & anomalies.

### 6. When do you denormalize?
For performance (read-heavy queries, reporting), when joins become expensive.

---

## 2) SQL + Querying

### 7. Difference between WHERE and HAVING?
- WHERE filters rows before grouping
- HAVING filters groups after GROUP BY

### 8. INNER JOIN vs LEFT JOIN?
- INNER JOIN: only matches
- LEFT JOIN: left rows always, right side may be null

### 9. What is EXISTS vs IN?
- EXISTS checks existence and can be faster for correlated subqueries
- IN can be slower with large sets

### 10. How do you find duplicate records?
```sql
SELECT email, COUNT(*)
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

### 11. What is window function?
Functions like `ROW_NUMBER()`, `RANK()` over partitions without grouping away rows.

---

## 3) Indexing (Most Important)

### 12. What is an index?
Data structure to speed up lookups (commonly B-Tree).

### 13. Pros/cons of indexes?
Pros: faster reads  
Cons: slower writes, extra storage

### 14. When index is NOT used?
- function on indexed column (`LOWER(email)`)
- leading wildcard: `LIKE '%abc'`
- poor selectivity column
- type mismatch / implicit conversion

### 15. Composite index rule?
Left-most prefix: (a,b) helps for a and a+b, not b alone.

### 16. What is covering index?
Index contains all columns needed by query → avoids table lookup.

---

## 4) Transactions + ACID

### 17. Explain ACID.
Atomicity, Consistency, Isolation, Durability.

### 18. What is a transaction?
Sequence of operations treated as one unit of work.

### 19. What is rollback?
Undo changes if failure.

### 20. Isolation levels?
READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE.

### 21. Dirty read vs phantom read?
Dirty: read uncommitted data  
Phantom: new rows appear on re-run of range query

---

## 5) Locks + Concurrency

### 22. Pessimistic vs optimistic locking?
- Pessimistic: lock row (SELECT FOR UPDATE)
- Optimistic: version check (@Version in JPA)

### 23. What is deadlock?
Two transactions wait for each other’s locks; DB aborts one.

### 24. How to reduce deadlocks?
- consistent locking order
- shorter transactions
- proper indexing

---

## 6) Schema Design + Constraints

### 25. Difference between UNIQUE and PRIMARY KEY?
PK: unique + not null + one per table  
UNIQUE: unique constraint; multiple possible; can allow nulls (DB-specific behavior)

### 26. Why foreign keys matter?
Prevents orphan rows and enforces consistency.

### 27. What is a junction table?
Table used for many-to-many relationship.

---

## 7) Performance & Optimization

### 28. How do you debug slow queries?
Use `EXPLAIN / EXPLAIN ANALYZE`, check indexes, remove full scans.

### 29. What is N+1 problem in JPA?
One query for parent + N queries for children due to lazy loading.

### 30. How to fix N+1?
Fetch join, EntityGraph, batch size.

### 31. OFFSET pagination issues?
Large OFFSET becomes slow. Use keyset pagination.

---

## 8) Replication / Scaling

### 32. What is read replica?
Copy of primary DB used for read scaling.

### 33. Sync vs async replication?
Sync: safer but slower  
Async: faster but may lose last writes on crash

### 34. Sharding vs partitioning?
- Partitioning: split inside same DB instance
- Sharding: split across multiple DB instances

---

## 9) NoSQL

### 35. Why NoSQL?
Scalability, flexible schema, high throughput.

### 36. Document DB vs relational?
Document: nested JSON, fewer joins  
Relational: normalized, strong constraints

### 37. CAP theorem?
In distributed systems: cannot fully guarantee Consistency + Availability + Partition tolerance.

---

## 10) Migration Tools / DevOps

### 38. Why schema migrations?
To version and consistently apply schema changes across environments.

### 39. Flyway vs Liquibase?
- Flyway: SQL-first, simple
- Liquibase: supports YAML/XML/JSON, advanced rollbacks

### 40. Why not use Hibernate ddl-auto in prod?
Uncontrolled changes, risk of data loss, no versioning.

### 41. Zero-downtime migrations strategy?
Expand → migrate → contract.

---

## 11) Real Project Scenarios

### 42. You need fast search by email. What do you do?
Add unique index on email + ensure queries match type/case.

### 43. Your table has 50M rows, queries slow. Fix?
Partition by date, add proper indexes, tune queries, avoid large OFFSET, archive old data.

### 44. Payments system needs consistency. Which DB?
RDBMS with ACID, strong constraints and transactions.

### 45. Cache vs DB?
Cache for speed, DB for source of truth.

---

✅ End of Interview Questions
