# SQL Interview Revision Notes (Service-Based Companies)

## 1. SQL Execution Order

1.  FROM
2.  JOIN
3.  WHERE
4.  GROUP BY
5.  HAVING
6.  SELECT
7.  DISTINCT
8.  ORDER BY
9.  LIMIT

------------------------------------------------------------------------

## 2. WHERE vs HAVING

  WHERE                            HAVING
  -------------------------------- ----------------------------------
  Filters rows before grouping     Filters groups after aggregation
  Cannot use aggregate functions   Used with aggregate functions

Example:

``` sql
SELECT dept_id, COUNT(*)
FROM employees
GROUP BY dept_id
HAVING COUNT(*) > 1;
```

------------------------------------------------------------------------

## 3. JOIN Types

### INNER JOIN

Returns only matching rows from both tables.

### LEFT JOIN

Returns all rows from left table and matching rows from right table.

Important: Using WHERE condition on right table after LEFT JOIN may
behave like INNER JOIN.

------------------------------------------------------------------------

## 4. GROUP BY & Aggregation

-   COUNT(\*) → Counts all rows
-   COUNT(column) → Counts non-null values
-   SUM(), AVG(), MIN(), MAX()

Without GROUP BY: Entire table becomes one group.

------------------------------------------------------------------------

## 5. Window Functions

### RANK()

Skips rank numbers when duplicates exist.

### DENSE_RANK()

Does not skip numbers. Best for Nth highest distinct value.

### ROW_NUMBER()

Assigns unique sequence number. Not suitable for distinct ranking.

------------------------------------------------------------------------

## 6. Find Duplicate Records

``` sql
SELECT email
FROM employees
GROUP BY email
HAVING COUNT(*) > 1;
```

To fetch full records: Use subquery or JOIN.

------------------------------------------------------------------------

## 7. NOT IN vs NOT EXISTS

  NOT IN                            NOT EXISTS
  --------------------------------- ----------------
  Fails if subquery returns NULL    Safe with NULL
  Can return no rows unexpectedly   Reliable

Prefer NOT EXISTS for anti-join logic.

------------------------------------------------------------------------

## 8. Index Concepts

### What is Index?

Data structure that improves read performance but slows writes.

### Composite Index Rule

Index (A, B, C)

Efficient: - WHERE A = ? - WHERE A = ? AND B = ? - WHERE A = ? AND B = ?
AND C = ?

Not Efficient: - WHERE B = ? - WHERE C = ? - WHERE A = ? AND C = ?

### Equality → Range → Order Rule

Best pattern:

``` sql
WHERE A = ?
AND B > ?
ORDER BY B
```

Index: (A, B)

------------------------------------------------------------------------

## 9. DELETE vs TRUNCATE

  DELETE                          TRUNCATE
  ------------------------------- ------------------------
  Can use WHERE                   Removes all rows
  Can rollback                    Usually auto-commit
  Fires triggers                  Does not fire triggers
  Slower                          Faster
  Does not reset auto increment   Resets auto increment

------------------------------------------------------------------------

## 10. Deadlock

Occurs when two transactions wait for each other's locks. Database
detects cycle and rolls back one transaction.

------------------------------------------------------------------------

## 11. Normalization

Purpose: - Reduce redundancy - Prevent update anomalies

Over-normalization: - Too many joins - Performance degradation

------------------------------------------------------------------------

## 12. Slow Query Checklist

-   Check execution plan
-   Check index usage
-   Avoid SELECT \*
-   Reduce joins if possible
-   Check low selectivity indexes
-   Avoid functions on indexed columns
-   Verify composite index order

------------------------------------------------------------------------

# End of Revision Notes
