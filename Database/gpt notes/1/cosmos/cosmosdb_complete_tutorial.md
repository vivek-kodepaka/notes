
# Azure Cosmos DB – Complete Tutorial & SQL Comparison Guide
## For Spring Boot / SQL Developers

---

# 📌 What is Cosmos DB?

Azure Cosmos DB is a globally distributed, schema-less NoSQL database service on Azure.

Think like:

MySQL / SQL Server → Structured relational tables  
Cosmos DB → Flexible JSON document store

---

# 🔥 Quick One-line Summary

Cosmos DB = MongoDB-style JSON documents + cloud scale + global replication + low latency.

---

# 📊 SQL vs Cosmos DB – Full Comparison

| Feature | SQL (Relational DB) | Cosmos DB |
|------------|--------------------|--------------------|
| Model | Tables/Rows | JSON Documents |
| Schema | Fixed | Schema-less |
| Joins | Yes | Limited |
| Scaling | Vertical | Horizontal (auto) |
| Transactions | Strong ACID | Scoped (single partition) |
| Queries | SQL | SQL-like API |
| Performance | Medium | Very fast |
| Best for | Banking/transactions | Microservices/logs/catalogs |
| Structure | Normalized | Denormalized |
| Global replication | Manual | Built-in |
| Latency | ms–10ms | single-digit ms |

---

# 📦 Cosmos DB Data Model

## Document Example

```json
{
  "id": "101",
  "name": "Vivek",
  "skills": ["Java", "Spring"],
  "address": {
    "city": "Hyderabad"
  }
}
```

---

# 🔹 Core Concepts You MUST Learn

| Concept | Meaning |
|------------|----------------------------|
| Container | Like table |
| Item | Like row |
| Partition Key | Data distribution key |
| RU (Request Units) | Cost of queries |
| Throughput | Performance capacity |
| Consistency Levels | Data freshness |
| TTL | Auto delete data |
| Indexing | Automatic indexes |

---

# 🔥 Containers vs Tables

| SQL | Cosmos |
|------|---------|
| Table | Container |
| Row | Item |
| Column | JSON field |
| PK | id |
| Index | Auto created |

---

# 🔥 Query Syntax (SQL-like)

## Select

```sql
SELECT * FROM c
```

## Filter

```sql
SELECT * FROM c WHERE c.city = 'Hyderabad'
```

## Projection

```sql
SELECT c.name, c.salary FROM c
```

## Top

```sql
SELECT TOP 5 * FROM c
```

---

# 🔥 No JOINs (Important Difference)

SQL:
```sql
SELECT * FROM orders o JOIN users u ON ...
```

Cosmos:
Store related data together (denormalize)

```json
{
  "orderId": 1,
  "user": { "name": "Vivek" }
}
```

---

# 🔥 Partition Key (VERY IMPORTANT)

Partition Key determines:
- performance
- scalability
- cost

Example:

/userId

Queries using partition key are VERY FAST.

---

# 🔥 RU (Request Units)

Each query costs RU.

| Operation | RU Cost |
|------------|----------|
| Read | Low |
| Write | Medium |
| Complex query | High |
| Cross partition | Very High |

Rule:
Design queries to use partition key.

---

# 🔥 Consistency Levels

| Level | Meaning |
|------------|------------------|
| Strong | Always latest |
| Bounded | Slight delay |
| Session | Default |
| Consistent Prefix | Ordered |
| Eventual | Fastest |

Most apps use Session.

---

# 🔥 Indexing

Automatic by default.

Can customize:

```json
{
  "indexingMode": "consistent"
}
```

---

# 🔥 Spring Boot Integration

## Dependency

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-spring-data-cosmos</artifactId>
</dependency>
```

## application.yml

```yaml
spring:
  cloud:
    azure:
      cosmos:
        endpoint: https://<account>.documents.azure.com:443/
        key: <key>
        database: mydb
```

## Entity

```java
@Container(containerName = "users")
public class User {
    @Id
    private String id;
    private String name;
}
```

## Repository

```java
public interface UserRepo extends CosmosRepository<User, String> {}
```

---

# 🔥 When to Use Cosmos DB

Use when:
- flexible schema
- huge scale
- microservices
- logs/events
- user profiles
- IoT/time-series

Avoid when:
- complex joins
- heavy transactions
- relational reports

---

# 🔥 SQL vs Cosmos – Real Usage

| Scenario | Use |
|-------------|-----------|
| Payments | SQL |
| Orders | SQL |
| Logs | Cosmos |
| User sessions | Cosmos |
| Catalog | Cosmos |
| Analytics | Both |

---

# 🔥 Learning Roadmap

Week 1:
- Data model
- Containers
- Partition key

Week 2:
- Queries
- Indexing
- RU optimization

Week 3:
- Spring Boot integration
- Scaling
- Monitoring

Week 4:
- Consistency
- Multi-region
- Security

---

# 🔥 Interview Questions

1. What is Cosmos DB?
2. SQL vs Cosmos DB difference?
3. What is partition key?
4. What are Request Units?
5. How to optimize RU?
6. Why avoid cross-partition queries?
7. Explain consistency levels.
8. Why no joins?
9. When to use Cosmos over SQL?
10. How to integrate with Spring Boot?
11. What is denormalization?
12. How replication works?
13. How indexing works?
14. How scaling works?
15. Explain TTL.

---

# ✅ Final Cheat Sheet

SQL → Structured, ACID, joins  
Cosmos → JSON, scalable, flexible  

Partition key = performance  
RU = cost  
Denormalize = design rule  

---

Happy learning 🚀
