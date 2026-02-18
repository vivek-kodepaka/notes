# Azure Cosmos DB -- Complete Notes (Theory + Interview + Code Examples)

## 1. What is Azure Cosmos DB?

Azure Cosmos DB is a fully managed, globally distributed NoSQL database
service by Microsoft Azure.

Key features: - Multi‑model database (Core SQL API, MongoDB API,
Cassandra API, Table API, Gremlin API) - Global distribution with
multi‑region writes - Automatic indexing - Tunable consistency levels -
Low latency (single‑digit milliseconds) - Elastic scalability using
Request Units (RU/s)

------------------------------------------------------------------------

## 2. When to Use Cosmos DB

Use Cosmos DB when: - You need global distribution - Very high
read/write throughput - Low latency APIs - Schema‑less or flexible
schema - Large scale event or telemetry data

Avoid when: - Complex relational joins are required - Strong ACID across
many entities is critical

------------------------------------------------------------------------

## 3. Core Concepts

### 3.1 Account

Top-level container in Azure.

### 3.2 Database

Logical namespace for containers.

### 3.3 Container (Collection)

Stores items and defines partition key.

### 3.4 Item

JSON document stored inside container.

Example item:

``` json
{
  "id": "101",
  "name": "Vivek",
  "city": "Hyderabad"
}
```

------------------------------------------------------------------------

## 4. Partitioning (Very Important for Interviews)

Cosmos DB scales using partition keys.

Partition key: - Distributes data - Determines scalability - Affects
query cost

Good partition key: - High cardinality - Even distribution - Frequently
used in queries

Bad partition key example: - Country (few values) Good example: -
userId - orderId

------------------------------------------------------------------------

## 5. Request Units (RU/s)

RU is the cost unit for: - Reads - Writes - Queries

Approx: - 1KB read ≈ 1 RU - Write ≈ 5--10 RU

RU depends on: - Document size - Indexing - Query complexity

------------------------------------------------------------------------

## 6. Consistency Levels

Cosmos DB offers 5 consistency levels:

1.  Strong -- most consistent, highest latency
2.  Bounded Staleness
3.  Session (default)
4.  Consistent Prefix
5.  Eventual -- fastest, least consistent

Interview tip: Most applications use Session consistency.

------------------------------------------------------------------------

## 7. Indexing

Automatic by default.

You can: - Include paths - Exclude paths - Use composite indexes

Why customize indexing? - Reduce RU cost - Improve performance

------------------------------------------------------------------------

## 8. Querying in Cosmos DB

Uses SQL‑like syntax:

``` sql
SELECT * FROM c WHERE c.city = "Hyderabad"
```

Supports: - SELECT - WHERE - ORDER BY - JOIN (within document only)

------------------------------------------------------------------------

## 9. Change Feed

Tracks inserts and updates.

Use cases: - Event-driven systems - Kafka pipelines - Auditing

------------------------------------------------------------------------

## 10. Global Distribution

Benefits: - Low latency worldwide - Multi-region writes - Automatic
failover

------------------------------------------------------------------------

## 11. Cosmos DB vs Traditional SQL

  Feature   Cosmos DB    SQL
  --------- ------------ ----------
  Schema    Flexible     Fixed
  Scaling   Horizontal   Vertical
  Joins     Limited      Full
  Latency   Very Low     Medium

------------------------------------------------------------------------

## 12. Java Code Example

### 12.1 Maven Dependency

``` xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>4.61.0</version>
</dependency>
```

------------------------------------------------------------------------

### 12.2 Create Client

``` java
CosmosClient client = new CosmosClientBuilder()
    .endpoint("<endpoint>")
    .key("<key>")
    .consistencyLevel(ConsistencyLevel.SESSION)
    .buildClient();
```

------------------------------------------------------------------------

### 12.3 Create Database

``` java
CosmosDatabase database =
    client.createDatabaseIfNotExists("testdb").getDatabase();
```

------------------------------------------------------------------------

### 12.4 Create Container

``` java
CosmosContainer container =
    database.createContainerIfNotExists(
        "users",
        "/id",
        ThroughputProperties.createManualThroughput(400)
    ).getContainer();
```

------------------------------------------------------------------------

### 12.5 Insert Item

``` java
User user = new User("101", "Vivek", "Hyderabad");
container.createItem(user);
```

------------------------------------------------------------------------

### 12.6 Query Items

``` java
CosmosPagedIterable<User> users =
    container.queryItems(
        "SELECT * FROM c WHERE c.city='Hyderabad'",
        new CosmosQueryRequestOptions(),
        User.class
    );

for (User u : users) {
    System.out.println(u.getName());
}
```

------------------------------------------------------------------------

## 13. Spring Boot Integration (Basic)

application.properties

    cosmos.uri=YOUR_URI
    cosmos.key=YOUR_KEY
    cosmos.database=testdb

Config Example:

``` java
@Bean
public CosmosClient cosmosClient() {
    return new CosmosClientBuilder()
        .endpoint(uri)
        .key(key)
        .buildClient();
}
```

------------------------------------------------------------------------

## 14. Performance Best Practices

-   Choose correct partition key
-   Avoid cross-partition queries
-   Limit indexing
-   Use bulk operations
-   Use change feed for streaming

------------------------------------------------------------------------

## 15. Common Interview Questions

### Q1: What is partition key and why important?

It distributes data and enables horizontal scaling.

### Q2: What are consistency levels?

Strong, Bounded Staleness, Session, Consistent Prefix, Eventual.

### Q3: What is RU?

Unit measuring request cost.

### Q4: Difference between container and table?

Container stores JSON documents and supports indexing and partitioning.

### Q5: How to reduce RU cost?

-   Better partition key
-   Optimize queries
-   Reduce indexing

------------------------------------------------------------------------

## 16. Scenario-Based Interview Questions

**Scenario 1: High RU consumption** Answer: - Check indexing policy -
Review query patterns - Partition key design

**Scenario 2: Slow queries** Answer: - Ensure partition key filter -
Avoid full scans - Use proper indexing

------------------------------------------------------------------------

## 17. Real World Use Cases

-   IoT telemetry
-   E-commerce catalog
-   User sessions
-   Event sourcing

------------------------------------------------------------------------

## 18. Quick Revision Cheat Sheet

-   Container = Collection
-   Item = JSON document
-   RU = Throughput
-   Partition key = Scalability
-   Session consistency = Default
-   Automatic indexing enabled

------------------------------------------------------------------------

END OF NOTES
