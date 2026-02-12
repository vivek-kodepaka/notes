# DBMS Concurrency, Locks, MVCC, Isolation & Deadlocks

### Interview Preparation Notes (Detailed)

------------------------------------------------------------------------

# 1. Concurrency in DBMS

Concurrency means multiple transactions executing at the same time while
maintaining consistency.

## Why Concurrency Matters

-   High throughput systems
-   Multi‑user applications
-   Microservices with shared databases

## Problems Without Concurrency Control

1.  Lost Update
2.  Dirty Read
3.  Non‑repeatable Read
4.  Phantom Read

------------------------------------------------------------------------

# 2. Locking in Databases

Locks prevent data corruption when multiple transactions modify the same
data.

## Types of Locks

### Shared Lock (Read Lock)

-   Multiple reads allowed
-   Writes blocked

### Exclusive Lock (Write Lock)

-   Only one writer allowed
-   Reads and writes blocked

------------------------------------------------------------------------

# 3. How Locking Works (Timeline)

Example:

T1: UPDATE account SET balance=900 WHERE id=1;

T2: UPDATE account SET balance=800 WHERE id=1;

Flow: 1. T1 acquires exclusive lock 2. T2 waits 3. T1 commits 4. T2
proceeds

------------------------------------------------------------------------

# 4. Deadlocks

A deadlock occurs when two or more transactions wait for each other
indefinitely.

## Example Timeline

T1: Lock Row A → waiting for Row B

T2: Lock Row B → waiting for Row A

Circular wait → deadlock

------------------------------------------------------------------------

# 5. When Deadlocks Occur in Real Systems

Common causes:

1.  Updating tables in different order
2.  Long transactions holding locks
3.  Batch updates or large deletes
4.  Missing indexes causing full table scans
5.  Foreign key cascades
6.  Triggers updating additional tables
7.  High‑contention rows (inventory, counters)
8.  Multiple services updating same records

------------------------------------------------------------------------

# 6. How Databases Detect Deadlocks

Databases maintain a lock dependency graph. If a cycle is detected: -
One transaction is rolled back - Other continues

Error Example: Deadlock found when trying to get lock; try restarting
transaction

------------------------------------------------------------------------

# 7. How to Resolve / Prevent Deadlocks

Best practices:

-   Access tables in consistent order
-   Keep transactions short
-   Use proper indexes
-   Avoid large batch updates in one transaction
-   Retry transactions on deadlock
-   Reduce transaction scope

------------------------------------------------------------------------

# 8. MVCC (Multi-Version Concurrency Control)

MVCC allows: - Readers to read old versions - Writers to create new
versions

## Key Points

-   Only modified rows get new versions
-   Snapshot is logical, not a full copy
-   Improves read performance

------------------------------------------------------------------------

# 9. MVCC Timeline Example

Initial: balance = 1000

T1 starts transaction and reads → sees 1000

T2 updates balance to 900 and commits

T1 reads again: Repeatable Read → still sees 1000 Read Committed → sees
900

------------------------------------------------------------------------

# 10. Isolation Levels

Isolation controls visibility of changes.

  Isolation Level    Dirty Read   Non-repeatable Read   Phantom
  ------------------ ------------ --------------------- ----------
  Read Uncommitted   Yes          Yes                   Yes
  Read Committed     No           Yes                   Yes
  Repeatable Read    No           No                    Possible
  Serializable       No           No                    No

------------------------------------------------------------------------

# 11. How Isolation and MVCC Work Together

MVCC: Stores row versions

Isolation: Decides which version is visible

Example: Repeatable Read → fixed snapshot Read Committed → latest
committed version

------------------------------------------------------------------------

# 12. Optimistic Locking

Used when conflicts are rare.

## How It Works

-   Version column added
-   Update checks version
-   Conflict detected if version changed

SQL Pattern: UPDATE orders SET status='DONE', version=version+1 WHERE
id=1 AND version=2;

------------------------------------------------------------------------

# 13. Retry Pattern (Safe Approach)

Correct retry: 1. Reload entity 2. Re-check business rule 3. Attempt
update again

Never retry with stale data.

------------------------------------------------------------------------

# 14. Pessimistic Locking

Used when conflicts are likely.

Example: SELECT \* FROM orders WHERE id=1 FOR UPDATE;

Row locked until commit.

------------------------------------------------------------------------

# 15. Optimistic vs Pessimistic Comparison

  Feature             Optimistic        Pessimistic
  ------------------- ----------------- -------------------
  Locking             No initial lock   Locks immediately
  Conflict Handling   Retry             Wait
  Throughput          High              Lower
  Deadlock Risk       Low               Higher

------------------------------------------------------------------------

# 16. Testing MVCC in MySQL (Hands‑On Steps)

1.  Open two sessions
2.  Start transaction in session 1
3.  Read a row
4.  Update same row in session 2
5.  Session 1 still sees old value

------------------------------------------------------------------------

# 17. What Happens When Two Transactions Update Same Row

1.  First transaction acquires lock
2.  Second waits
3.  First commits
4.  Second proceeds

------------------------------------------------------------------------

# 18. Production Design Tips

-   Keep transactions short
-   Use idempotent APIs
-   Add retry with backoff
-   Monitor slow queries
-   Use connection pools
-   Avoid large transactions

------------------------------------------------------------------------

# 19. Deadlock Troubleshooting in Production

Steps: 1. Capture deadlock logs 2. Identify queries involved 3. Check
execution order 4. Add indexes if needed 5. Reduce transaction size 6.
Standardize table access order

------------------------------------------------------------------------

# 20. Common Interview Questions

1.  What is MVCC?
2.  Difference between optimistic and pessimistic locking?
3.  How deadlocks occur and how to prevent them?
4.  What happens when two transactions update the same row?
5.  Difference between Read Committed and Repeatable Read?

------------------------------------------------------------------------

End of Notes
