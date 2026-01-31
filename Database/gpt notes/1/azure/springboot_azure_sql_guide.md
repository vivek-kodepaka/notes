
# Spring Boot + Azure SQL Guide
## Configuration, Best Practices, and Interview Questions

---

# 📌 What is Azure SQL?
Azure SQL Database is a managed cloud relational database built on Microsoft SQL Server.
You don't manage servers, backups, patching, or HA — Azure handles everything.

---

# 🚀 Spring Boot Configuration

## 1️⃣ Maven Dependency

```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>12.6.1.jre17</version>
</dependency>
```

---

## 2️⃣ application.yml (Recommended)

```yaml
spring:
  datasource:
    url: jdbc:sqlserver://<server>.database.windows.net:1433;database=<db>;encrypt=true;trustServerCertificate=false;loginTimeout=30;
    username: <username>
    password: <password>
    driver-class-name: com.microsoft.sqlserver.jdbc.SQLServerDriver

  jpa:
    database-platform: org.hibernate.dialect.SQLServerDialect
    hibernate:
      ddl-auto: validate
    show-sql: false

  datasource.hikari:
    maximum-pool-size: 20
    minimum-idle: 5
    connection-timeout: 30000
```

---

## 3️⃣ application.properties (Alternative)

```properties
spring.datasource.url=jdbc:sqlserver://server.database.windows.net:1433;database=db;encrypt=true
spring.datasource.username=user
spring.datasource.password=pass
spring.jpa.database-platform=org.hibernate.dialect.SQLServerDialect
```

---

# 🔥 Important Notes

| Setting | Why |
|-----------|----------------|
| encrypt=true | Required for Azure |
| SQLServerDialect | Hibernate compatibility |
| HikariCP | Best connection pool |
| ddl-auto=validate | Avoid prod schema issues |

---

# 🔥 Pagination Query (Azure Syntax)

```sql
SELECT *
FROM emp
ORDER BY id
OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY;
```

---

# 🔥 Stored Procedure Call Example

```java
@Procedure("getEmployees")
List<Employee> getEmployees();
```

---

# 🔥 Performance Best Practices

| Area | Recommendation |
|-----------|----------------|
| Indexing | Add clustered + non-clustered indexes |
| Pagination | Avoid large OFFSET |
| Projections | Avoid SELECT * |
| Pooling | Use HikariCP |
| Monitoring | Use Query Store |
| Scaling | Use read replicas |
| Caching | Use Redis |

---

# 🔥 Azure SQL Concepts to Learn

| Concept | Meaning |
|-----------|----------------|
| DTU/vCore | Performance sizing |
| Query Store | Track slow queries |
| Geo-replication | Disaster recovery |
| Automatic backups | Point-in-time restore |
| Firewall rules | Network access control |
| Azure AD auth | Secure login |
| Read replicas | Scale reads |

---

# 🎯 Interview Questions – Azure SQL

## Basics
1. What is Azure SQL Database?
2. Difference between Azure SQL and SQL Server on VM?
3. What is DTU vs vCore?
4. What is T-SQL?

## Syntax
5. LIMIT vs TOP?
6. AUTO_INCREMENT vs IDENTITY?
7. NOW() vs GETDATE()?
8. Pagination syntax in Azure SQL?

## Performance
9. How to check execution plan?
10. What is Query Store?
11. Clustered vs Non-clustered index?
12. How to optimize slow queries?
13. Why avoid OFFSET for large pages?

## Cloud/Architecture
14. What is geo-replication?
15. How backups work in Azure SQL?
16. How do you scale database?
17. How to secure DB connections?
18. What is read replica?

## Spring Boot Integration
19. How to configure SQL Server driver?
20. Which Hibernate dialect to use?
21. How to handle connection pooling?
22. How to call stored procedures?

## Advanced
23. Temp tables vs table variables?
24. What is columnstore index?
25. How to avoid deadlocks?
26. Explain isolation levels.
27. How does failover work?
28. When to use caching vs DB queries?
29. How to migrate from MySQL to Azure SQL?
30. Explain monitoring strategy.

---

# 🔥 Quick Cheat Sheet

MySQL → Azure SQL

LIMIT → TOP / OFFSET FETCH  
AUTO_INCREMENT → IDENTITY  
NOW() → GETDATE()  
IFNULL → ISNULL  
EXPLAIN → Execution Plan  
Slow Log → Query Store  

---

# ✅ Final Advice

If you know SQL + Spring Boot:
Just learn:
- T-SQL syntax
- Indexing
- Query Store
- Azure portal basics
- Scaling and backups

You're ready for production 🚀
