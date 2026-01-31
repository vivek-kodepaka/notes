
# MongoDB Complete Guide
## SQL Comparison + Spring Boot Integration + CRUD Operations

---

# 📌 What is MongoDB?

MongoDB is a NoSQL document database that stores data as JSON-like documents (BSON).

Think like:

SQL → Tables & Rows  
MongoDB → Collections & Documents

---

# 🔥 Quick One-line Summary

MongoDB = Flexible JSON storage + schema-less + horizontally scalable + very fast for reads/writes.

---

# 📊 SQL vs MongoDB – Comparison Table

| Feature | SQL (Relational) | MongoDB |
|-------------|----------------|----------------------|
| Structure | Tables | Collections |
| Record | Row | Document (JSON) |
| Schema | Fixed | Flexible |
| Joins | Yes | Limited ($lookup) |
| Scaling | Vertical | Horizontal |
| Transactions | Full ACID | Multi-doc (newer versions) |
| Query | SQL | JSON Query |
| Performance | Medium | Fast |
| Best for | Banking/transactions | Logs, profiles, catalogs |
| Design | Normalized | Denormalized |

---

# 📦 Data Model Example

## SQL Table
| id | name | city |
|----|------|------|

## Mongo Document

```json
{
  "_id": 1,
  "name": "Vivek",
  "city": "Hyderabad",
  "skills": ["Java", "Spring"]
}
```

---

# 🔥 SQL vs Mongo Query Mapping

| Operation | SQL | Mongo |
|-------------|-------------------|--------------------------|
| Insert | INSERT | insertOne |
| Select | SELECT | find |
| Where | WHERE | { field: value } |
| Update | UPDATE | updateOne |
| Delete | DELETE | deleteOne |
| Join | JOIN | $lookup |
| Limit | LIMIT | limit() |
| Order | ORDER BY | sort() |

---

# 🔹 Mongo Query Syntax

## Insert
```javascript
db.users.insertOne({name:"Vivek", city:"Hyd"})
```

## Find
```javascript
db.users.find({city:"Hyd"})
```

## Update
```javascript
db.users.updateOne(
  {name:"Vivek"},
  {$set:{city:"Bangalore"}}
)
```

## Delete
```javascript
db.users.deleteOne({name:"Vivek"})
```

---

# 🔥 Important Mongo Concepts

| Concept | Meaning |
|--------------|-------------------------|
| Database | Like schema |
| Collection | Like table |
| Document | Row |
| _id | Primary key |
| Index | Speed search |
| Aggregation | Analytics |
| Replica Set | High availability |
| Sharding | Horizontal scale |

---

# 🚀 Spring Boot Integration

---

## Step 1 — Maven Dependency

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

---

## Step 2 — application.yml

```yaml
spring:
  data:
    mongodb:
      uri: mongodb://localhost:27017/appdb
```

For Atlas:

```yaml
spring:
  data:
    mongodb:
      uri: mongodb+srv://user:pass@cluster.mongodb.net/appdb
```

---

## Step 3 — Entity (Document)

```java
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

@Document(collection = "users")
public class User {

    @Id
    private String id;
    private String name;
    private String city;

    // getters/setters
}
```

---

## Step 4 — Repository

```java
import org.springframework.data.mongodb.repository.MongoRepository;

public interface UserRepository extends MongoRepository<User, String> {

    List<User> findByCity(String city);

}
```

---

## Step 5 — Service Layer

```java
@Service
public class UserService {

    @Autowired
    private UserRepository repo;

    public User save(User u){
        return repo.save(u);
    }

    public List<User> getAll(){
        return repo.findAll();
    }

    public Optional<User> get(String id){
        return repo.findById(id);
    }

    public void delete(String id){
        repo.deleteById(id);
    }
}
```

---

## Step 6 — REST Controller

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService service;

    @PostMapping
    public User create(@RequestBody User u){
        return service.save(u);
    }

    @GetMapping
    public List<User> list(){
        return service.getAll();
    }

    @GetMapping("/{id}")
    public User get(@PathVariable String id){
        return service.get(id).orElse(null);
    }

    @DeleteMapping("/{id}")
    public void delete(@PathVariable String id){
        service.delete(id);
    }
}
```

---

# 🔥 CRUD Operations in Spring Boot

## Insert
repo.save(user);

## Read
repo.findAll();

## Update
repo.save(updatedUser);

## Delete
repo.deleteById(id);

---

# 🔥 Advanced Queries

## Custom Query

```java
@Query("{ city: ?0 }")
List<User> getByCity(String city);
```

---

# 🔥 Aggregation Example

```javascript
db.orders.aggregate([
  {$group:{_id:"$city", total:{$sum:"$amount"}}}
])
```

---

# 🔥 Indexing

```javascript
db.users.createIndex({city:1})
```

---

# 🔥 Best Practices

| Tip | Why |
|----------------------|----------------|
| Embed related data | avoid joins |
| Use indexes | faster queries |
| Avoid huge docs | memory issues |
| Limit projections | performance |
| Use aggregation | analytics |
| Use replica sets | HA |
| Use sharding | scale |

---

# 🔥 When to Use MongoDB

Use when:
- flexible schema
- logs/events
- microservices
- user profiles
- catalogs

Avoid when:
- heavy joins
- complex transactions
- strict relational data

---

# 🔥 Interview Questions

1. MongoDB vs SQL difference?
2. What is document model?
3. What is _id?
4. What is aggregation?
5. What is indexing?
6. Replica set vs sharding?
7. When to use Mongo?
8. How Spring Boot integrates Mongo?
9. How transactions work?
10. Why denormalization?

---

# ✅ Final Cheat Sheet

SQL → structured tables  
Mongo → JSON docs  

JOIN → embed  
LIMIT → limit()  
WHERE → { field:value }  

---

Happy Learning 🚀
