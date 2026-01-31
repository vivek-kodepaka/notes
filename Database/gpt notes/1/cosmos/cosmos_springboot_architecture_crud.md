
# Azure Cosmos DB + Spring Boot
# Real Project Architecture + Configuration + CRUD Guide

---

# 📌 Architecture Diagram (Real Microservice Setup)

```
                +---------------------+
                |     Frontend UI     |
                +----------+----------+
                           |
                           v
                +---------------------+
                |  Spring Boot APIs   |
                |  (Microservices)    |
                +----------+----------+
                           |
          +----------------+----------------+
          |                                 |
          v                                 v
+----------------------+         +------------------------+
|  Azure SQL Database  |         |   Azure Cosmos DB      |
|  (Transactions)      |         |   (JSON Documents)     |
+----------------------+         +------------------------+
                                          |
                                          v
                               +--------------------+
                               |  Global Replicas   |
                               |  Auto Scaling      |
                               +--------------------+
```

---

# 🔥 When to Use Both Together (Polyglot Persistence)

| Data | Store |
|-------------------|----------------|
| Payments/Orders | Azure SQL |
| Logs/Profiles | Cosmos DB |
| Sessions/Cache | Redis |

---

# 🚀 Step 1 — Maven Dependency

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>5.11.0</version>
</dependency>
```

---

# 🚀 Step 2 — application.yml

```yaml
spring:
  cloud:
    azure:
      cosmos:
        endpoint: https://<account>.documents.azure.com:443/
        key: <primary-key>
        database: appdb
        populate-query-metrics: true
```

---

# 🚀 Step 3 — Entity (Container)

Cosmos uses JSON documents instead of tables.

```java
import com.azure.spring.data.cosmos.core.mapping.Container;
import org.springframework.data.annotation.Id;

@Container(containerName = "users")
public class User {

    @Id
    private String id;

    private String name;
    private String email;
    private String city;

    // getters/setters
}
```

---

# 🚀 Step 4 — Repository

```java
import com.azure.spring.data.cosmos.repository.CosmosRepository;

public interface UserRepository extends CosmosRepository<User, String> {

    List<User> findByCity(String city);

}
```

---

# 🚀 Step 5 — Service Layer

```java
@Service
public class UserService {

    @Autowired
    private UserRepository repo;

    public User save(User u){
        return repo.save(u);
    }

    public List<User> getAll(){
        return (List<User>) repo.findAll();
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

# 🚀 Step 6 — REST Controller

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

# 🔥 CRUD Operations (SQL vs Cosmos)

## Insert
```java
repo.save(user);
```

## Read
```java
repo.findById("101");
```

## Update
```java
user.setCity("Bangalore");
repo.save(user);
```

## Delete
```java
repo.deleteById("101");
```

---

# 🔥 Cosmos SQL-like Queries

```sql
SELECT * FROM c WHERE c.city = 'Hyderabad'
```

Custom query:

```java
@Query("SELECT * FROM c WHERE c.city = @city")
List<User> getByCity(String city);
```

---

# 🔥 Partition Key (Very Important)

Best practice:

```
/userId or /tenantId
```

Benefits:
- faster queries
- cheaper RU
- scalable

---

# 🔥 Performance Tips

| Tip | Reason |
|----------------------|-----------------|
| Choose good partition key | avoid cross-partition |
| Avoid joins | denormalize |
| Select only needed fields | lower RU |
| Use indexing wisely | faster queries |
| Monitor RU usage | cost control |

---

# 🔥 Real Project Flow

```
POST /users  → save → Cosmos DB
GET /users   → fetch → Cosmos DB
Analytics    → SQL DB
```

---

# 🔥 What to Learn (Checklist)

✅ Containers  
✅ Partition keys  
✅ RU optimization  
✅ Query patterns  
✅ Spring Boot integration  
✅ Scaling & replication  
✅ Security (keys/firewall)  

---

# 🎯 Interview Questions

1. What is Cosmos DB?
2. Why no joins?
3. What is partition key?
4. What are RU?
5. How to reduce RU?
6. How scaling works?
7. How to integrate Spring Boot?
8. Why denormalization?
9. When to choose SQL vs Cosmos?
10. Explain consistency levels.

---

# ✅ Final Summary

Cosmos DB:
- JSON
- flexible
- massive scale
- low latency

Spring Boot:
- use repository
- same CRUD style as JPA
- easy integration

---

Happy Building 🚀
