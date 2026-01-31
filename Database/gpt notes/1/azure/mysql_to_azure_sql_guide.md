
# MySQL → Azure SQL (SQL Server) Migration Guide
## Syntax Differences, Functions, and What to Learn

---

## Overview
Azure SQL Database is a fully managed cloud database based on Microsoft SQL Server.
If you know MySQL, about 80% of SQL is the same. The main differences are:
- T-SQL syntax
- Built-in functions
- Index types
- Cloud/managed features

---

# 1. Basic Syntax Differences

| Feature | MySQL | Azure SQL (T-SQL) |
|--------|--------|------------------|
| Limit rows | LIMIT 5 | TOP 5 |
| Pagination | LIMIT 10 OFFSET 20 | OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY |
| Auto increment | AUTO_INCREMENT | IDENTITY(1,1) |
| Current date | NOW() | GETDATE() |
| Null handling | IFNULL() | ISNULL() |
| String concat | CONCAT(a,b) | a + b |
| Quotes | ` (backtick) | [] or none |
| Boolean | TRUE/FALSE | 1/0 |
| Replace into | REPLACE | MERGE |
| Upsert | ON DUPLICATE KEY | MERGE |

---

# 2. CRUD Examples

## Select
MySQL:
SELECT * FROM emp LIMIT 5;

Azure:
SELECT TOP 5 * FROM emp;

## Pagination
MySQL:
SELECT * FROM emp LIMIT 10 OFFSET 20;

Azure:
SELECT * FROM emp
ORDER BY id
OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY;

---

# 3. Function Differences

## Date Functions
| Purpose | MySQL | Azure SQL |
|---------|---------|-----------|
| Current date | NOW() | GETDATE() |
| Add days | DATE_ADD() | DATEADD() |
| Difference | DATEDIFF() | DATEDIFF() |
| Format | DATE_FORMAT() | FORMAT() |

## String Functions
| Purpose | MySQL | Azure |
|-----------|-----------|-----------|
| Length | LENGTH() | LEN() |
| Substring | SUBSTRING() | SUBSTRING() |
| Concat | CONCAT() | + |
| Replace | REPLACE() | REPLACE() |

## Null Functions
| MySQL | Azure |
|--------|---------|
| IFNULL | ISNULL |
| COALESCE | COALESCE |

---

# 4. Index Differences

| Feature | MySQL | Azure SQL |
|-----------|-----------|-------------|
| Primary index | clustered | clustered |
| Secondary | normal | non-clustered |
| Covering index | yes | included columns |
| Columnstore | limited | strong support |

Learn:
- Clustered index
- Non-clustered index
- Included columns
- Execution plans

---

# 5. Stored Procedures / T-SQL Features

Azure SQL supports:
- Advanced stored procedures
- TRY...CATCH
- Variables
- Temp tables (#temp)
- Table variables
- CTEs
- Window functions

Example:

DECLARE @count INT;
SELECT @count = COUNT(*) FROM emp;

---

# 6. Cloud Features (NEW for Azure)

| Feature | Why important |
|-----------|----------------|
| Automatic backups | No manual backup |
| Geo-replication | Disaster recovery |
| Auto scaling | Handles traffic spikes |
| Query Store | Track slow queries |
| Azure AD auth | Secure login |
| Firewall rules | Network security |
| Read replicas | Scale reads |

---

# 7. Performance Tools

| MySQL | Azure SQL |
|-----------|--------------|
| EXPLAIN | Execution Plan |
| Slow log | Query Store |
| SHOW PROCESSLIST | DMVs |
| Index stats | sys.indexes |

Useful commands:

SET STATISTICS IO ON;
SET STATISTICS TIME ON;

---

# 8. What You Should Learn (Roadmap)

## Week 1
- T-SQL basics
- TOP / OFFSET FETCH
- Date functions

## Week 2
- Index types
- Execution plans
- Stored procedures

## Week 3
- Azure portal
- Query Store
- Scaling/DTU/vCore

## Week 4
- Security
- Replication
- Backup/restore
- Performance tuning

---

# Final Cheat Sheet

MySQL → Azure mapping:
LIMIT → TOP/OFFSET
AUTO_INCREMENT → IDENTITY
NOW → GETDATE
IFNULL → ISNULL

---

Good luck with Azure SQL!
