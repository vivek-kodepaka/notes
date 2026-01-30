# SQL Queries & Internal Implementation (10+ Years Experience)

This guide covers **ALL major SQL query types** and explains **how databases execute them internally**. It is written at a **senior / architect level** and aligns with **real interview expectations**.

---

## 1. SELECT (Read Queries)
### 1.1 Basic SELECT
```sql
SELECT id, name FROM users WHERE age > 30;
```
**Internals:**
- Parser builds AST
- Optimizer chooses index scan or full table scan
- Executor fetches rows


## How an Index References a Table

```
INDEX (age)                 TABLE (users)
--------------------        -----------------------
age | row_pointer   -----> row_id | id | name | age
--------------------        -----------------------
```

### Explanation
- The **index** stores only:
    - Indexed column value (`age`)
    - A **row pointer** (ROWID / primary key / page+offset)
- The **table** stores the full row data
- Index does **NOT** duplicate the table
- Index allows fast lookup by jumping directly to rows



---

### 1.2 WHERE Clause
```sql
SELECT * FROM orders WHERE status = 'PAID';
```
**Internals:**
- Predicate pushed down
- Index seek if index exists
- Row filtering happens before projection

---

### 1.3 ORDER BY
```sql
SELECT * FROM users ORDER BY created_at DESC;
```
**Internals:**
- Uses index order if available
- Else performs external sort (memory + disk)

---

### 1.4 LIMIT / OFFSET
```sql
SELECT * FROM users ORDER BY id LIMIT 10 OFFSET 1000;
```
**Internals:**
- OFFSET rows still scanned
- LIMIT stops row emission early
- Keyset pagination preferred

---

## 2. INSERT (Write Queries)
```sql
INSERT INTO users (id, name) VALUES (1, 'A');
```
**Internals:**
- WAL (Write Ahead Log)
- Row written to data page
- Index entries updated

---

### 2.1 Bulk Insert
```sql
INSERT INTO users SELECT * FROM temp_users;
```
**Internals:**
- Batch execution
- Reduced WAL flushes
- Faster than row-by-row

---

## 3. UPDATE
```sql
UPDATE accounts SET balance = balance - 100 WHERE id = 10;
```
**Internals:**
- Row-level lock acquired
- Old version stored (MVCC)
- New version written

---

## 4. DELETE
```sql
DELETE FROM sessions WHERE expired = true;
```
**Internals:**
- Logical delete (MVCC)
- Space reclaimed later by vacuum / purge

---

## 5. JOINS (CRITICAL)

### 5.1 INNER JOIN
```sql
SELECT * FROM orders o JOIN users u ON o.user_id = u.id;
```
**Internals:**
- Nested Loop Join
- Hash Join
- Merge Join

---

### 5.2 LEFT JOIN
```sql
SELECT * FROM users u LEFT JOIN orders o ON u.id = o.user_id;
```
**Internals:**
- Preserves left table rows
- NULL padding for missing matches

---

## 6. GROUP BY & Aggregation
```sql
SELECT status, COUNT(*) FROM orders GROUP BY status;
```
**Internals:**
- Hash aggregation
- Sort-based aggregation

---

## 7. HAVING
```sql
SELECT status FROM orders GROUP BY status HAVING COUNT(*) > 10;
```
**Internals:**
- Aggregation first
- HAVING applied after GROUP BY

---

## 8. SUBQUERIES

### 8.1 Correlated Subquery
```sql
SELECT * FROM orders o WHERE amount > (
  SELECT AVG(amount) FROM orders WHERE user_id = o.user_id
);
```
**Internals:**
- Executed per row (slow)
- Often rewritten as JOIN by optimizer

---

## 9. EXISTS vs IN
```sql
SELECT * FROM users u WHERE EXISTS (
  SELECT 1 FROM orders o WHERE o.user_id = u.id
);
```
**Internals:**
- EXISTS short-circuits
- IN materializes set

---

## 10. INDEXES (VERY IMPORTANT)

### 10.1 B-Tree Index
```sql
CREATE INDEX idx_users_age ON users(age);
```
**Internals:**
- Balanced tree
- O(log N) lookup

---

### 10.2 Composite Index
```sql
CREATE INDEX idx_users_age_city ON users(age, city);
```
**Internals:**
- Leftmost prefix rule

---

## 11. TRANSACTIONS
```sql
BEGIN;
UPDATE accounts SET balance = balance - 100;
COMMIT;
```
**Internals:**
- WAL logging
- Atomic commit
- Rollback via undo logs

---

## 12. ISOLATION LEVELS
| Level | Phenomena |
|-----|---------|
| Read Uncommitted | Dirty reads |
| Read Committed | Non-repeatable reads |
| Repeatable Read | Phantom reads |
| Serializable | Full isolation |

---

## 13. LOCKING

### Row-level locks
```sql
SELECT * FROM orders WHERE id = 10 FOR UPDATE;
```
**Internals:**
- Prevents concurrent updates
- Used in money transfer systems

---

## 14. MVCC (MULTI VERSION CONCURRENCY CONTROL)
**How it works:**
- Readers see snapshot
- Writers create new row version
- No read locks

---

## 15. EXPLAIN / QUERY PLAN
```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE age > 30;
```
**Internals:**
- Shows cost-based decisions
- Actual vs estimated rows

---

## 16. PERFORMANCE ANTI-PATTERNS
❌ SELECT *
❌ OFFSET pagination
❌ Functions on indexed columns
❌ Missing indexes on JOIN keys

---

## 17. SCALING CONCEPTS
- Read replicas
- Sharding
- Partitioning
- Caching (Redis)

---

## 18. INTERVIEW GOLD QUESTIONS
- How index selectivity affects plans
- Why COUNT(*) is fast
- Difference between logical and physical delete
- How deadlocks occur
- How databases ensure durability

---

## FINAL SENIOR MENTAL MODEL
```
SQL = declarative
Optimizer = decision maker
Indexes = performance
Transactions = correctness
```

---

If you want next:
- PostgreSQL vs MySQL internals
- Real EXPLAIN plan walkthroughs
- SQL tuning checklist
- Database interview Q&A (10+ yrs)

