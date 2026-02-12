# DBMS Notes -- Concurrency, Locks, MVCC, Isolation, and Deadlocks

## 1. Concurrency in DBMS

Concurrency means multiple transactions accessing or modifying the
database at the same time.

**Why concurrency is needed** - Many users access systems
simultaneously - Improves throughput and performance

**Problems without concurrency control** - Lost updates - Dirty reads -
Non-repeatable reads - Phantom reads

------------------------------------------------------------------------

## 2. Locks in DBMS

Locks prevent multiple transactions from corrupting data.

### Types of Locks

**Shared Lock (Read Lock)** - Multiple transactions can read - No
transaction can write

**Exclusive Lock (Write Lock)** - Only one transaction can modify -
Others must wait

### When Locks Occur

-   UPDATE → exclusive lock
-   DELETE → exclusive lock
-   SELECT FOR UPDATE → exclusive lock
-   Plain SELECT (in MVCC systems) → usually no lock

------------------------------------------------------------------------

## 3. Locking Example

Transaction T1: UPDATE account SET balance=900 WHERE id=1;

Transaction T2: UPDATE account SET balance=800 WHERE id=1;

T2 waits until T1 commits or rolls back.

------------------------------------------------------------------------

## 4. Deadlocks in DBMS

A deadlock occurs when two or more transactions wait for each other
indefinitely.

### Example

Transaction T1: - Locks Row A - Tries to lock Row B

Transaction T2: - Locks Row B - Tries to lock Row A

Both wait forever → Deadlock

------------------------------------------------------------------------

## 5. When Deadlocks Commonly Occur

1.  Transactions update rows in different order
2.  Long-running transactions
3.  Batch updates on overlapping datasets
4.  Missing indexes causing large row scans
5.  Multiple services updating same tables
6.  Foreign key cascades and triggers
7.  High concurrency on inventory or payment tables

------------------------------------------------------------------------

## 6. How Databases Handle Deadlocks

-   Detect circular wait
-   Abort one transaction
-   Rollback occurs
-   Application should retry

------------------------------------------------------------------------

## 7. How to Prevent Deadlocks

**Best Practices** - Access tables in same order - Keep transactions
short - Use proper indexing - Avoid user interaction inside
transactions - Update rows in small batches - Use optimistic locking
when possible

------------------------------------------------------------------------

## 8. MVCC (Multi-Version Concurrency Control)

MVCC allows multiple versions of rows so readers don't block writers.

**Key Points** - Only modified rows get new versions - Old versions
stored internally (undo logs or tuple versions) - Reads see snapshot
based on transaction start

------------------------------------------------------------------------

## 9. Snapshot Meaning

Snapshot does NOT copy entire table. It stores visibility rules and
transaction IDs.

------------------------------------------------------------------------

## 10. Isolation Levels

Isolation level determines what data a transaction can see.

  Isolation Level    Dirty Read   Non-repeatable Read   Phantom
  ------------------ ------------ --------------------- ----------
  Read Uncommitted   Yes          Yes                   Yes
  Read Committed     No           Yes                   Yes
  Repeatable Read    No           No                    Possible
  Serializable       No           No                    No

------------------------------------------------------------------------

## 11. How Isolation Works with MVCC

-   MVCC stores versions
-   Isolation decides which version is visible

Example: - Read Committed → latest committed version - Repeatable Read →
snapshot at transaction start

------------------------------------------------------------------------

## 12. Optimistic Locking

Assumes conflicts are rare.

**How it works** - Version column added - Update checks version - If
mismatch → retry

### Example SQL Pattern

UPDATE orders SET status='DONE', version=version+1 WHERE id=1 AND
version=2;

If rows affected = 0 → conflict

### JPA Example

@Version private Integer version;

Hibernate increments version automatically.

------------------------------------------------------------------------

## 13. Retry Logic in Optimistic Locking

Retry must: 1. Re-read latest data 2. Re-validate business rules 3.
Attempt update again

Never retry using stale data.

------------------------------------------------------------------------

## 14. Pessimistic Locking

Assumes conflicts are likely.

### Example

SELECT \* FROM orders WHERE id=1 FOR UPDATE;

Row locked until commit.

### JPA Example

@Lock(LockModeType.PESSIMISTIC_WRITE)

------------------------------------------------------------------------

## 15. Optimistic vs Pessimistic Locking

  Feature             Optimistic        Pessimistic
  ------------------- ----------------- -------------------
  Locking             No initial lock   Locks immediately
  Conflict Handling   Retry             Wait
  Performance         Higher            Lower
  Deadlock Risk       Low               Higher

------------------------------------------------------------------------

## 16. Testing MVCC in MySQL

Steps: 1. Open two sessions 2. Start transaction in session 1 3. Read
row 4. Update same row in session 2 and commit 5. Session 1 still sees
old value (Repeatable Read)

------------------------------------------------------------------------

## 17. When Writes Block Writes

Two UPDATE statements on same row: - First acquires lock - Second waits

MVCC does not remove write locks.

------------------------------------------------------------------------

## 18. Real-World Best Practices

-   Use short transactions
-   Prefer optimistic locking in microservices
-   Use pessimistic locking in financial operations
-   Always validate business state before updates
-   Add retry with backoff for optimistic locking

------------------------------------------------------------------------

## 19. Interview Summary Points

**Deadlock definition** Deadlock occurs when transactions hold locks and
wait for each other in a circular dependency.

**MVCC definition** MVCC allows multiple row versions so reads don't
block writes.

**Isolation definition** Isolation level controls visibility of data
between transactions.

**Optimistic locking** Version-based conflict detection without locking.

**Pessimistic locking** Database locks row to prevent concurrent
updates.

------------------------------------------------------------------------

End of Notes
