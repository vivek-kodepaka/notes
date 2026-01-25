# Database Notes (Complete) — Theory + Examples
*For a Java/Spring Boot developer (7 years experience)*

---

## 0) Why Databases Exist (Core Idea)

A **database** is a system to store data so it can be:

- **Persisted** (not lost when app restarts)
- **Queried** efficiently (find/filter/join)
- **Updated safely** (transactions, consistency)
- **Shared** across many users/services
- **Backed up / restored**
- **Secured & audited**

---

## 1) Database Categories

### 1.1 Relational Databases (RDBMS) ✅
Examples: **PostgreSQL, MySQL, Oracle, SQL Server**

**Data model:** tables (relations) with rows (tuples) and columns (attributes)

**Strengths**
- Strong consistency + ACID transactions
- Powerful querying using SQL
- Strong constraints: PK, FK, UNIQUE, CHECK
- Great for complex joins/reporting

**Best for**
- Banking, payments, inventory, order systems
- Most business apps using Spring Boot + JPA

---

### 1.2 Non‑Relational Databases (NoSQL)
“NoSQL” = not only SQL. Many types.

#### A) Document DB
Examples: **MongoDB, CouchDB**

Stores JSON-like documents.

**Best for**
- Flexible schema, rapidly changing fields
- Product catalogs, profiles, CMS

Example document:
```json
{
  "userId": 101,
  "name": "Vivek",
  "skills": ["Java", "Spring"],
  "address": { "city": "Hyderabad", "pin": 500001 }
}
```

#### B) Key‑Value DB
Examples: **Redis, DynamoDB**

Key → Value mapping.

Best for:
- Cache, session store, rate limiting

Example:
```
"user:101" -> "{...json...}"
"otp:phone:98765" -> "842193"
```

#### C) Wide‑Column DB
Examples: **Cassandra, HBase**

Good for high write throughput and distributed scaling.

Best for:
- Time-series logs, IoT data, event storage

#### D) Graph DB
Examples: **Neo4j**

Nodes + edges for relationships.

Best for:
- Social networks, fraud detection, recommendation systems

---

## 2) Relational Model (RM) Basics

### 2.1 Terms
- **Relation** = Table  
- **Tuple** = Row  
- **Attribute** = Column  
- **Domain** = allowed values for an attribute (e.g., age 0..150)

Example table:
```sql
CREATE TABLE employee (
  emp_id BIGINT PRIMARY KEY,
  name   VARCHAR(100) NOT NULL,
  dept   VARCHAR(50) NOT NULL
);
```

A tuple (row):
`(101, 'Ravi', 'IT')`

### 2.2 Keys
#### Primary Key (PK)
Uniquely identifies a row.

#### Candidate Key
Any column set that can uniquely identify a row.

#### Alternate Key
Candidate keys not chosen as PK.

#### Composite Key
PK made from multiple columns.

#### Foreign Key (FK)
References PK of another table.

```sql
CREATE TABLE orders (
  order_id BIGINT PRIMARY KEY,
  user_id  BIGINT NOT NULL,
  CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

---

## 3) SQL Fundamentals (CRUD)

### 3.1 Create
```sql
INSERT INTO employee(emp_id, name, dept)
VALUES (101, 'Vivek', 'IT');
```

### 3.2 Read
```sql
SELECT emp_id, name FROM employee WHERE dept = 'IT';
```

### 3.3 Update
```sql
UPDATE employee SET dept = 'HR' WHERE emp_id = 101;
```

### 3.4 Delete
```sql
DELETE FROM employee WHERE emp_id = 101;
```

---

## 4) Relationships in RDBMS (with examples)

### 4.1 One‑to‑One (1:1)
Example: `User` ↔ `UserProfile`

```sql
CREATE TABLE user_profile (
  user_id BIGINT PRIMARY KEY,
  bio     TEXT,
  CONSTRAINT fk_profile_user FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

### 4.2 One‑to‑Many (1:N)
Example: `User` → many `Orders`

```sql
CREATE TABLE orders (
  order_id BIGINT PRIMARY KEY,
  user_id  BIGINT NOT NULL,
  total    NUMERIC(10,2),
  FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

### 4.3 Many‑to‑Many (M:N)
Example: `Student` ↔ `Course` via junction table

```sql
CREATE TABLE student_course (
  student_id BIGINT NOT NULL,
  course_id  BIGINT NOT NULL,
  PRIMARY KEY(student_id, course_id),
  FOREIGN KEY(student_id) REFERENCES student(student_id),
  FOREIGN KEY(course_id) REFERENCES course(course_id)
);
```

---

## 5) Constraints (Data Integrity)

### 5.1 NOT NULL
```sql
name VARCHAR(100) NOT NULL
```

### 5.2 UNIQUE
```sql
email VARCHAR(255) UNIQUE
```

### 5.3 CHECK
```sql
age INT CHECK (age >= 0)
```

### 5.4 FOREIGN KEY
Ensures referential integrity.

---

## 6) Normalization (Very Important)

### 6.1 Why Normalize?
To avoid:
- data duplication
- update anomalies
- inconsistent data

### 6.2 Normal Forms
#### 1NF
- atomic columns (no lists in single field)
Bad:
`skills = "Java,Spring,Docker"`
Good:
Separate table: employee_skill

#### 2NF
- no partial dependency on part of composite key

#### 3NF
- no transitive dependency (non-key depends on non-key)

**Example issue:**
`employee(emp_id, dept_id, dept_name)`
Here `dept_name` depends on `dept_id`, not directly on `emp_id`.
Fix: separate `department(dept_id, dept_name)`

### Denormalization
Used for performance, reporting, analytics.

---

## 7) Transactions (ACID)

### A — Atomicity
All or nothing.

### C — Consistency
Constraints always valid before/after transaction.

### I — Isolation
Concurrent transactions shouldn’t break correctness.

### D — Durability
Committed data survives crash.

Example transfer:
```sql
BEGIN;

UPDATE account SET balance = balance - 100 WHERE id = 1;
UPDATE account SET balance = balance + 100 WHERE id = 2;

COMMIT;
```

---

## 8) Isolation Levels + Problems

### 8.1 Common Problems
- **Dirty Read**: reading uncommitted data
- **Non‑Repeatable Read**: same query returns different result
- **Phantom Read**: new rows appear in range query

### 8.2 Isolation Levels
From weakest to strongest:

1. **READ UNCOMMITTED** (rare)
2. **READ COMMITTED** (common default in many DBs)
3. **REPEATABLE READ**
4. **SERIALIZABLE** (strongest)

---

## 9) Indexing (Huge Interview Topic ✅)

### 9.1 What is an Index?
A data structure that speeds up reads.

Most indexes are **B‑Tree** indexes (balanced tree).

Without index:
- DB scans full table → `O(n)`

With index:
- DB searches faster → `O(log n)`

### 9.2 Create Index
```sql
CREATE INDEX idx_employee_dept ON employee(dept);
```

### 9.3 Composite Index
```sql
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
```

**Important Rule (Left‑Most Prefix):**
Index `(user_id, status)` helps:
- `WHERE user_id = ?`
- `WHERE user_id = ? AND status = ?`
Not great for:
- `WHERE status = ?` alone

### 9.4 Unique Index
```sql
CREATE UNIQUE INDEX uq_user_email ON users(email);
```

### 9.5 Pros and Cons of Indexing
✅ Pros:
- faster SELECT queries
- supports sorting, filtering

❌ Cons:
- slower INSERT/UPDATE/DELETE (index maintenance)
- extra storage

### 9.6 Clustered vs Non‑Clustered Index
- **Clustered**: table data stored in index order (usually PK)
- **Non‑clustered**: separate structure referencing rows

(PostgreSQL uses heap + indexes; InnoDB uses clustered PK internally)

---

## 10) Query Optimization (How DB Executes SQL)

### 10.1 Explain Plan
Use it to see query plan.

Postgres:
```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE user_id = 10;
```

Common plan operations:
- Seq Scan (full table scan)
- Index Scan
- Hash Join / Merge Join / Nested Loop Join

### 10.2 Common Performance Fixes
- add index on filter column
- avoid `SELECT *`
- avoid functions on indexed column:
  - BAD: `WHERE LOWER(email) = 'x'`
  - Better: store normalized email or use functional index
- pagination: use keyset pagination instead of big OFFSET

---

## 11) Joins (Must Know)

Tables:
```text
users(user_id, name)
orders(order_id, user_id, total)
```

### INNER JOIN
Only matching rows:
```sql
SELECT u.name, o.order_id
FROM users u
JOIN orders o ON u.user_id = o.user_id;
```

### LEFT JOIN
All users + matching orders:
```sql
SELECT u.name, o.order_id
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id;
```

### RIGHT JOIN
All orders + matching users (rarely used)

### FULL OUTER JOIN
All rows from both sides.

---

## 12) Aggregations (GROUP BY / HAVING)

```sql
SELECT user_id, COUNT(*) AS order_count
FROM orders
GROUP BY user_id
HAVING COUNT(*) > 5;
```

---

## 13) Subqueries + EXISTS

### IN (works but sometimes slower)
```sql
SELECT * FROM users
WHERE user_id IN (SELECT user_id FROM orders);
```

### EXISTS (often better)
```sql
SELECT * FROM users u
WHERE EXISTS (
  SELECT 1 FROM orders o WHERE o.user_id = u.user_id
);
```

---

## 14) Stored Procedures / Functions / Triggers

### Stored Procedure
Encapsulated logic in DB.

Pros:
- close to data, fast for batch ops

Cons:
- vendor lock-in
- harder testing & version control (but migrations solve it)

### Trigger Example (Audit)
On update:
- store old/new values into audit table

Use carefully → triggers can surprise application behavior.

---

## 15) Partitioning (Scaling Large Tables)

Split large table into partitions by:
- date (range)
- hash
- list

Example: `orders_2025_01`, `orders_2025_02`

Benefits:
- faster queries on partitions
- easier archival

---

## 16) Replication, Read Replicas, Sharding

### Replication
Primary → replica copies.

- **Sync replication**: safer, slower
- **Async replication**: faster, can lose some data on crash

### Read Replica
Use for read-heavy systems:
- writes go to primary
- reads go to replica

### Sharding
Split data across multiple databases (horizontal scale).
Harder due to:
- cross-shard joins
- transactions

---

## 17) CAP Theorem (NoSQL + Distributed)

In distributed systems, you can’t fully guarantee all 3 simultaneously:
- **C** Consistency
- **A** Availability
- **P** Partition tolerance

Partition tolerance is mandatory in real distributed networks, so tradeoff is usually:
- CP (strong consistency, may reject some requests)
- AP (high availability, eventual consistency)

---

## 18) Consistency Models (NoSQL)

### Strong Consistency
Reads always reflect latest write.

### Eventual Consistency
After some time, replicas converge.

Used in AP systems (Dynamo-style).

---

## 19) Schema Design: Relational vs Document (Practical View)

### Relational design
- normalized tables
- joins for relations
- constraints for safety

### Document design
- embed related data for faster reads
- duplicate data sometimes acceptable

Example:
User + Address:
- RDBMS → separate tables
- MongoDB → embedded address inside user document

---

## 20) Data Types (Common)

### Numeric
- INT, BIGINT
- DECIMAL/NUMERIC (money)
- FLOAT/DOUBLE (approx)

### String
- VARCHAR, TEXT

### Date/time
- DATE, TIMESTAMP

### Boolean
- BOOLEAN

### JSON
Postgres supports JSON/JSONB:
```sql
ALTER TABLE orders ADD COLUMN metadata JSONB;
```

---

## 21) Database Locks (Important)

### Pessimistic Locking
Lock row while updating:
```sql
SELECT * FROM orders WHERE order_id = 10 FOR UPDATE;
```

### Optimistic Locking
Used in JPA via `@Version`.

```java
@Version
private Long version;
```

Best for high concurrency where conflicts are rare.

---

## 22) Deadlocks

Deadlock = two transactions wait on each other.

Example:
- T1 locks row A, wants B
- T2 locks row B, wants A

DB will abort one transaction.

Fix:
- consistent lock ordering
- smaller transactions
- proper indexes (reduce lock time)

---

## 23) Backups & Recovery

### Backup Types
- Full backup
- Incremental backup
- WAL / Binlog based (Point-in-time recovery)

### Restore Testing
Always test restore in staging.

---

## 24) Security Best Practices

- least privilege (app user should not be superuser)
- parameterized queries (avoid SQL injection)
- encrypt at rest (disk encryption)
- encrypt in transit (TLS)
- audit logs

---

## 25) Database Migration Tools (Must Know ✅)

### Why DB migrations?
Because schema changes must be:
- versioned
- repeatable
- consistent across dev/test/prod

### 25.1 Flyway (Most used in Java)
- simple, predictable
- versioned SQL files

Example structure:
```
V1__init.sql
V2__add_orders_table.sql
V3__add_index_on_orders.sql
```

Example migration:
```sql
-- V2__add_orders_table.sql
CREATE TABLE orders (
  order_id BIGINT PRIMARY KEY,
  user_id BIGINT NOT NULL,
  total NUMERIC(10,2),
  created_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

### 25.2 Liquibase
- supports XML/YAML/JSON/SQL changelogs
- better for complex rollbacks

YAML example:
```yaml
databaseChangeLog:
  - changeSet:
      id: 1
      author: vivek
      changes:
        - createTable:
            tableName: users
            columns:
              - column:
                  name: user_id
                  type: BIGINT
                  constraints:
                    primaryKey: true
              - column:
                  name: email
                  type: VARCHAR(255)
```

### 25.3 Hibernate ddl-auto (Not recommended in prod)
```properties
spring.jpa.hibernate.ddl-auto=update
```
Okay for local development, risky for production.

---

## 26) Migration Strategies in Real Projects

### Expand → Migrate → Contract (Zero downtime)
Example: rename column `name` → `full_name`

1) Expand: add `full_name`
2) Migrate: backfill data
3) App reads both
4) Contract: drop old `name`

---

## 27) Handling Large Data Migrations

- run in batches
- use background jobs
- avoid locking huge tables
- add indexes carefully (CONCURRENTLY in Postgres)

---

## 28) Database in Microservices

### One DB per service (recommended)
✅ independent deploy + scaling  
❌ harder reporting + joins across services

### Shared DB (not ideal)
Causes coupling.

### Event-driven consistency
Use Kafka / outbox pattern.

---

## 29) Caching (DB + Redis)

### Cache-aside pattern
1) check cache
2) if miss → read DB → set cache

Best for:
- read-heavy endpoints
- frequent lookups

---

## 30) Practical Checklist for Production DB

✅ Schema
- PK for each table
- proper FK constraints where needed
- unique constraints for business keys

✅ Indexing
- index on search fields
- index FKs for joins
- avoid too many indexes

✅ Queries
- avoid N+1 (JPA fetch join)
- use pagination wisely
- analyze slow queries

✅ Reliability
- backups + restore test
- monitoring (connections, slow queries, replication lag)

✅ Security
- secrets management (Vault/KMS)
- least privilege
- rotate credentials

---

## 31) Quick Comparison Table

| Feature | Relational (RDBMS) | Document (MongoDB) | Key-Value (Redis) |
|---|---|---|---|
| Schema | Fixed | Flexible | None |
| Joins | Strong | Limited | No |
| Transactions | Strong (ACID) | Yes (newer versions, limited) | Limited |
| Best for | Business apps | flexible data | caching |
| Scaling | vertical + some horizontal | horizontal | horizontal |

---

## 32) Spring Boot + Database (Practical)

### 32.1 JPA Repositories
```java
public interface OrderRepo extends JpaRepository<Order, Long> {
    List<Order> findByUserId(Long userId);
}
```

### 32.2 N+1 Problem
Bad:
```java
List<User> users = userRepo.findAll();
users.forEach(u -> u.getOrders().size()); // triggers N queries
```

Fix: fetch join:
```java
@Query("select u from User u join fetch u.orders")
List<User> findAllWithOrders();
```

---

## 33) Final Cheat Sheet (Fast Revision)

- **ACID** = correctness guarantee
- **Index** speeds reads, slows writes
- **Composite index** follows left-most rule
- **Normalization** reduces duplication
- **Isolation** prevents concurrency issues
- **Migration tools**: Flyway/Liquibase
- **Explain Analyze** for query tuning
- **Read replicas** for scale reads
- **Sharding** for extreme scaling
- **Redis** for caching, rate limiting

---

✅ End of Notes
