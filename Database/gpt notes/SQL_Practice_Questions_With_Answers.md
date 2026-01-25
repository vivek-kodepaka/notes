# SQL Practice Set (with Answers) — Interview + Real Project (7 Years)

> Focus: PostgreSQL/MySQL style SQL (mostly portable)

---

## Tables Used (Example Schema)

### users
```sql
CREATE TABLE users (
  user_id BIGINT PRIMARY KEY,
  name    VARCHAR(100) NOT NULL,
  email   VARCHAR(255) UNIQUE NOT NULL,
  city    VARCHAR(50),
  created_at TIMESTAMP NOT NULL
);
```

### orders
```sql
CREATE TABLE orders (
  order_id BIGINT PRIMARY KEY,
  user_id  BIGINT NOT NULL,
  status   VARCHAR(20) NOT NULL,  -- CREATED / PAID / SHIPPED / CANCELLED
  total    NUMERIC(10,2) NOT NULL,
  created_at TIMESTAMP NOT NULL,
  FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

### order_items
```sql
CREATE TABLE order_items (
  order_item_id BIGINT PRIMARY KEY,
  order_id BIGINT NOT NULL,
  product_id BIGINT NOT NULL,
  quantity INT NOT NULL,
  price NUMERIC(10,2) NOT NULL,
  FOREIGN KEY(order_id) REFERENCES orders(order_id)
);
```

### products
```sql
CREATE TABLE products (
  product_id BIGINT PRIMARY KEY,
  name VARCHAR(200) NOT NULL,
  category VARCHAR(100) NOT NULL
);
```

---

# A) Basic SELECT + Filtering (1–10)

## 1) Fetch all users
```sql
SELECT * FROM users;
```

## 2) Fetch users from Hyderabad
```sql
SELECT * FROM users
WHERE city = 'Hyderabad';
```

## 3) Fetch users created in last 30 days (Postgres)
```sql
SELECT * FROM users
WHERE created_at >= NOW() - INTERVAL '30 days';
```

## 4) Find users whose email ends with gmail.com
```sql
SELECT * FROM users
WHERE email LIKE '%@gmail.com';
```

## 5) Fetch orders with total > 1000
```sql
SELECT * FROM orders
WHERE total > 1000;
```

## 6) Fetch PAID orders
```sql
SELECT * FROM orders
WHERE status = 'PAID';
```

## 7) Fetch orders between two dates
```sql
SELECT * FROM orders
WHERE created_at BETWEEN '2026-01-01' AND '2026-01-31';
```

## 8) Sort orders by latest first
```sql
SELECT * FROM orders
ORDER BY created_at DESC;
```

## 9) Top 5 expensive orders
```sql
SELECT * FROM orders
ORDER BY total DESC
LIMIT 5;
```

## 10) Fetch distinct cities of users
```sql
SELECT DISTINCT city FROM users;
```

---

# B) Aggregations (GROUP BY / HAVING) (11–20)

## 11) Total number of users
```sql
SELECT COUNT(*) AS total_users
FROM users;
```

## 12) Total number of orders per status
```sql
SELECT status, COUNT(*) AS cnt
FROM orders
GROUP BY status;
```

## 13) Total revenue (sum of PAID orders only)
```sql
SELECT SUM(total) AS revenue
FROM orders
WHERE status = 'PAID';
```

## 14) Avg order value per user
```sql
SELECT user_id, AVG(total) AS avg_order_value
FROM orders
GROUP BY user_id;
```

## 15) Users with more than 5 orders
```sql
SELECT user_id, COUNT(*) AS order_count
FROM orders
GROUP BY user_id
HAVING COUNT(*) > 5;
```

## 16) Month-wise order count (Postgres)
```sql
SELECT DATE_TRUNC('month', created_at) AS month, COUNT(*) AS order_count
FROM orders
GROUP BY month
ORDER BY month;
```

## 17) Total order amount per user (PAID only)
```sql
SELECT user_id, SUM(total) AS total_paid
FROM orders
WHERE status = 'PAID'
GROUP BY user_id;
```

## 18) Highest order total
```sql
SELECT MAX(total) AS max_total FROM orders;
```

## 19) Lowest order total
```sql
SELECT MIN(total) AS min_total FROM orders;
```

## 20) Category-wise product count
```sql
SELECT category, COUNT(*) AS product_count
FROM products
GROUP BY category;
```

---

# C) Joins (21–30)

## 21) List orders with user name
```sql
SELECT o.order_id, u.name, o.status, o.total
FROM orders o
JOIN users u ON u.user_id = o.user_id;
```

## 22) List users with their total orders count (include users with 0 orders)
```sql
SELECT u.user_id, u.name, COUNT(o.order_id) AS order_count
FROM users u
LEFT JOIN orders o ON o.user_id = u.user_id
GROUP BY u.user_id, u.name
ORDER BY order_count DESC;
```

## 23) Total paid amount per user name
```sql
SELECT u.name, SUM(o.total) AS total_paid
FROM users u
JOIN orders o ON o.user_id = u.user_id
WHERE o.status = 'PAID'
GROUP BY u.name;
```

## 24) Orders that have items (join order_items)
```sql
SELECT DISTINCT o.order_id, o.status
FROM orders o
JOIN order_items oi ON oi.order_id = o.order_id;
```

## 25) Total quantity ordered per product
```sql
SELECT p.product_id, p.name, SUM(oi.quantity) AS total_qty
FROM products p
JOIN order_items oi ON oi.product_id = p.product_id
GROUP BY p.product_id, p.name
ORDER BY total_qty DESC;
```

## 26) Total revenue per product (paid orders only)
```sql
SELECT p.product_id, p.name,
       SUM(oi.quantity * oi.price) AS revenue
FROM products p
JOIN order_items oi ON oi.product_id = p.product_id
JOIN orders o ON o.order_id = oi.order_id
WHERE o.status = 'PAID'
GROUP BY p.product_id, p.name
ORDER BY revenue DESC;
```

## 27) Users who never placed an order
```sql
SELECT u.*
FROM users u
LEFT JOIN orders o ON o.user_id = u.user_id
WHERE o.order_id IS NULL;
```

## 28) Fetch latest order of each user (Postgres using DISTINCT ON)
```sql
SELECT DISTINCT ON (o.user_id)
  o.user_id, o.order_id, o.total, o.created_at
FROM orders o
ORDER BY o.user_id, o.created_at DESC;
```

## 29) Same using window function (portable)
```sql
SELECT *
FROM (
  SELECT o.*,
         ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY created_at DESC) AS rn
  FROM orders o
) t
WHERE rn = 1;
```

## 30) Users + their last order (LEFT JOIN)
```sql
SELECT u.user_id, u.name, lo.order_id, lo.total, lo.created_at
FROM users u
LEFT JOIN (
  SELECT *
  FROM (
    SELECT o.*,
           ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY created_at DESC) AS rn
    FROM orders o
  ) x
  WHERE rn = 1
) lo ON lo.user_id = u.user_id;
```

---

# D) Subqueries + EXISTS (31–38)

## 31) Users who placed at least one PAID order
```sql
SELECT u.*
FROM users u
WHERE EXISTS (
  SELECT 1 FROM orders o
  WHERE o.user_id = u.user_id AND o.status = 'PAID'
);
```

## 32) Orders greater than average order total
```sql
SELECT *
FROM orders
WHERE total > (SELECT AVG(total) FROM orders);
```

## 33) Users whose total paid amount > 5000
```sql
SELECT u.*
FROM users u
WHERE u.user_id IN (
  SELECT user_id
  FROM orders
  WHERE status = 'PAID'
  GROUP BY user_id
  HAVING SUM(total) > 5000
);
```

## 34) Products never ordered
```sql
SELECT p.*
FROM products p
WHERE NOT EXISTS (
  SELECT 1 FROM order_items oi
  WHERE oi.product_id = p.product_id
);
```

## 35) Orders with more than 3 items
```sql
SELECT o.order_id
FROM orders o
JOIN order_items oi ON oi.order_id = o.order_id
GROUP BY o.order_id
HAVING COUNT(*) > 3;
```

## 36) Users who ordered from category = 'Mobiles'
```sql
SELECT DISTINCT u.user_id, u.name
FROM users u
JOIN orders o ON o.user_id = u.user_id
JOIN order_items oi ON oi.order_id = o.order_id
JOIN products p ON p.product_id = oi.product_id
WHERE p.category = 'Mobiles';
```

## 37) Find second highest order total
```sql
SELECT MAX(total) AS second_highest
FROM orders
WHERE total < (SELECT MAX(total) FROM orders);
```

## 38) Find top 3 users by paid amount
```sql
SELECT user_id, SUM(total) AS total_paid
FROM orders
WHERE status = 'PAID'
GROUP BY user_id
ORDER BY total_paid DESC
LIMIT 3;
```

---

# E) Data Cleaning + Duplicates (39–44)

## 39) Detect duplicate emails
```sql
SELECT email, COUNT(*)
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

## 40) Delete duplicates keeping latest (Postgres example)
```sql
DELETE FROM users
WHERE user_id IN (
  SELECT user_id
  FROM (
    SELECT user_id,
           ROW_NUMBER() OVER (PARTITION BY email ORDER BY created_at DESC) AS rn
    FROM users
  ) t
  WHERE rn > 1
);
```

## 41) Replace NULL city with 'UNKNOWN'
```sql
UPDATE users
SET city = 'UNKNOWN'
WHERE city IS NULL;
```

## 42) Trim spaces in email
```sql
UPDATE users
SET email = TRIM(email);
```

## 43) Find users with invalid email format (simple)
```sql
SELECT *
FROM users
WHERE email NOT LIKE '%@%';
```

## 44) Standardize city to uppercase
```sql
UPDATE users
SET city = UPPER(city);
```

---

# F) Pagination (45–48)

## 45) Basic pagination with LIMIT/OFFSET (not best for large tables)
```sql
SELECT *
FROM orders
ORDER BY created_at DESC
LIMIT 20 OFFSET 40;
```

## 46) Keyset pagination (better)
```sql
SELECT *
FROM orders
WHERE created_at < '2026-01-20 10:00:00'
ORDER BY created_at DESC
LIMIT 20;
```

## 47) Keyset with id tie-breaker
```sql
SELECT *
FROM orders
WHERE (created_at, order_id) < ('2026-01-20 10:00:00', 5000)
ORDER BY created_at DESC, order_id DESC
LIMIT 20;
```

## 48) Fetch first page only
```sql
SELECT *
FROM orders
ORDER BY created_at DESC
LIMIT 20;
```

---

# G) Updates + Transactions (49–52)

## 49) Transaction example: cancel order + update status
```sql
BEGIN;

UPDATE orders
SET status = 'CANCELLED'
WHERE order_id = 1001;

COMMIT;
```

## 50) Rollback example
```sql
BEGIN;

UPDATE orders SET total = total + 100 WHERE order_id = 1001;

ROLLBACK;
```

## 51) Increase price for category products by 10%
```sql
UPDATE products
SET name = name  -- dummy no price field here, example only
WHERE category = 'Mobiles';
```

> In real: `UPDATE products SET price = price * 1.1 WHERE category = 'Mobiles';`

## 52) Lock an order row for update (pessimistic lock)
```sql
BEGIN;

SELECT * FROM orders WHERE order_id = 1001 FOR UPDATE;

UPDATE orders SET status = 'SHIPPED' WHERE order_id = 1001;

COMMIT;
```

---

# H) Indexing Practice (53–58)

## 53) Create index on orders.user_id
```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

## 54) Composite index for user + status
```sql
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
```

## 55) Unique index on email
```sql
CREATE UNIQUE INDEX uq_users_email ON users(email);
```

## 56) When composite index helps?
- `WHERE user_id = ?`
- `WHERE user_id = ? AND status = ?`

Not good for only `WHERE status = ?`

## 57) Explain query plan (Postgres)
```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE user_id = 10;
```

## 58) Drop an index
```sql
DROP INDEX idx_orders_user_id;
```

---

# I) Advanced Window Functions (59–65)

## 59) Rank users by total paid amount
```sql
SELECT user_id,
       SUM(total) AS total_paid,
       RANK() OVER (ORDER BY SUM(total) DESC) AS rnk
FROM orders
WHERE status = 'PAID'
GROUP BY user_id;
```

## 60) Row number of orders per user by date
```sql
SELECT o.*,
       ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY created_at) AS rn
FROM orders o;
```

## 61) Running total per user
```sql
SELECT o.*,
       SUM(total) OVER (PARTITION BY user_id ORDER BY created_at) AS running_total
FROM orders o
WHERE status = 'PAID';
```

## 62) 7-day moving average revenue (Postgres)
```sql
SELECT created_at::date AS day,
       AVG(total) OVER (ORDER BY created_at::date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS moving_avg
FROM orders
WHERE status = 'PAID';
```

## 63) First order date per user
```sql
SELECT DISTINCT user_id,
       MIN(created_at) OVER (PARTITION BY user_id) AS first_order_date
FROM orders;
```

## 64) Last order date per user
```sql
SELECT DISTINCT user_id,
       MAX(created_at) OVER (PARTITION BY user_id) AS last_order_date
FROM orders;
```

## 65) Find users with gap > 30 days between orders (idea)
Use LAG:
```sql
SELECT user_id, order_id, created_at,
       LAG(created_at) OVER (PARTITION BY user_id ORDER BY created_at) AS prev_order
FROM orders;
```

---

# J) Bonus: Real Interview Problems (66–75)

## 66) Find customers who ordered in Jan but not in Feb
```sql
SELECT DISTINCT o1.user_id
FROM orders o1
WHERE o1.created_at >= '2026-01-01' AND o1.created_at < '2026-02-01'
AND o1.user_id NOT IN (
  SELECT o2.user_id
  FROM orders o2
  WHERE o2.created_at >= '2026-02-01' AND o2.created_at < '2026-03-01'
);
```

## 67) Find top product per category by revenue (PAID)
```sql
SELECT *
FROM (
  SELECT p.category, p.product_id, p.name,
         SUM(oi.quantity * oi.price) AS revenue,
         ROW_NUMBER() OVER (PARTITION BY p.category ORDER BY SUM(oi.quantity * oi.price) DESC) AS rn
  FROM products p
  JOIN order_items oi ON oi.product_id = p.product_id
  JOIN orders o ON o.order_id = oi.order_id
  WHERE o.status = 'PAID'
  GROUP BY p.category, p.product_id, p.name
) t
WHERE rn = 1;
```

## 68) Find users who placed 3 consecutive days orders
Concept: count distinct days and check streak using date difference (varies per DB).

## 69) Orders with total mismatch vs sum(order_items)
```sql
SELECT o.order_id, o.total,
       SUM(oi.quantity * oi.price) AS items_total
FROM orders o
JOIN order_items oi ON oi.order_id = o.order_id
GROUP BY o.order_id, o.total
HAVING o.total <> SUM(oi.quantity * oi.price);
```

## 70) Find users with more cancelled than paid orders
```sql
SELECT user_id
FROM orders
GROUP BY user_id
HAVING SUM(CASE WHEN status='CANCELLED' THEN 1 ELSE 0 END)
     > SUM(CASE WHEN status='PAID' THEN 1 ELSE 0 END);
```

## 71) Find users who ordered exactly 1 time
```sql
SELECT user_id
FROM orders
GROUP BY user_id
HAVING COUNT(*) = 1;
```

## 72) Find users with max paid order total
```sql
SELECT user_id, MAX(total) AS max_paid
FROM orders
WHERE status='PAID'
GROUP BY user_id
ORDER BY max_paid DESC
LIMIT 1;
```

## 73) Find orders with no items (data issue)
```sql
SELECT o.*
FROM orders o
LEFT JOIN order_items oi ON oi.order_id = o.order_id
WHERE oi.order_item_id IS NULL;
```

## 74) Find user contribution % to total revenue
```sql
SELECT user_id,
       SUM(total) AS user_paid,
       ROUND(100.0 * SUM(total) / (SELECT SUM(total) FROM orders WHERE status='PAID'), 2) AS pct
FROM orders
WHERE status='PAID'
GROUP BY user_id;
```

## 75) Find duplicate order_items for same product in same order
```sql
SELECT order_id, product_id, COUNT(*)
FROM order_items
GROUP BY order_id, product_id
HAVING COUNT(*) > 1;
```

---

✅ End of SQL Practice Set
