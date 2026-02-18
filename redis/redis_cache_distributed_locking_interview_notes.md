# Redis in Microservices – Complete Notes (Service-Based Interview Focus)

---

# 1. What is Redis?

Redis is an in-memory key-value data store used for:
- Caching
- Distributed locking
- Rate limiting
- Session storage
- Pub/Sub messaging
- Counters

It is extremely fast because data is stored in memory.

---

# 2. Why Do We Use Redis in Microservices?

## Problems Without Redis

- High DB load
- Increased latency
- Poor scalability
- Repeated computation
- No coordination between instances

## What Redis Solves

- Reduces DB hits
- Improves response time
- Enables distributed locking
- Enables shared sessions
- Enables rate limiting

---

# 3. Redis as Cache

## Cache-Aside Pattern (Most Common)

Flow:
1. Check Redis
2. If present → return
3. If absent → fetch from DB
4. Store in Redis
5. Return response

---

# 4. Spring Boot Redis Configuration

## Maven Dependencies

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

## application.yml

```yaml
spring:
  cache:
    type: redis
  redis:
    host: localhost
    port: 6379
```

## Enable Caching

```java
@EnableCaching
```

---

# 5. Spring Cache Annotations

## @Cacheable

Used for read operations.

```java
@Cacheable(value = "products", key = "#id")
public Product getProduct(Long id) {
    return repository.findById(id);
}
```

## @CacheEvict

Used to remove cache after update.

```java
@CacheEvict(value = "products", key = "#id")
public void updateProduct(Long id, Product p) {
    repository.save(p);
}
```

## @CachePut

Updates cache without skipping method execution.

```java
@CachePut(value = "products", key = "#product.id")
public Product update(Product product) {
    return repository.save(product);
}
```

---

# 6. Common Cache Problems

## 1. Stale Data
Solution: @CacheEvict or TTL

## 2. Cache Stampede
When many requests hit DB after cache expiry.

Solution:
- Distributed Lock
- Preloading cache
- Random TTL

## 3. Cache Penetration
Invalid keys repeatedly hitting DB.

Solution:
- Cache null values
- Use Bloom filter

---

# 7. Distributed Lock Using Redis (Manual Implementation)

## Lock Service

```java
@Service
public class RedisLockService {

    @Autowired
    private StringRedisTemplate redisTemplate;

    public boolean acquireLock(String key, String value, long timeoutSeconds) {
        Boolean success = redisTemplate.opsForValue()
                .setIfAbsent(key, value, Duration.ofSeconds(timeoutSeconds));
        return Boolean.TRUE.equals(success);
    }

    public void releaseLock(String key, String value) {
        String currentValue = redisTemplate.opsForValue().get(key);
        if (value.equals(currentValue)) {
            redisTemplate.delete(key);
        }
    }
}
```

## Usage

```java
public void updateOrder(Long orderId) {

    String lockKey = "lock:order:" + orderId;
    String uuid = UUID.randomUUID().toString();

    boolean locked = lockService.acquireLock(lockKey, uuid, 10);

    if (!locked) {
        throw new RuntimeException("Another request is processing");
    }

    try {
        // critical section
    } finally {
        lockService.releaseLock(lockKey, uuid);
    }
}
```

---

# 8. Why UUID is Required

Prevents deleting another instance's lock.

Without UUID:
- Lock expires
- Another instance acquires
- First instance deletes wrong lock

---

# 9. Production Approach – Redisson

## Dependency

```xml
<dependency>
  <groupId>org.redisson</groupId>
  <artifactId>redisson-spring-boot-starter</artifactId>
  <version>3.23.5</version>
</dependency>
```

## Usage

```java
@Autowired
private RedissonClient redissonClient;

public void updateOrder(Long orderId) {
    RLock lock = redissonClient.getLock("lock:order:" + orderId);

    lock.lock();
    try {
        // critical section
    } finally {
        lock.unlock();
    }
}
```

Advantages:
- Auto lock renewal
- Safe unlock
- Handles race conditions

---

# 10. Rate Limiting Using Redis

## Basic Counter Approach

```java
Long count = redisTemplate.opsForValue().increment("rate:user:101");

if (count == 1) {
    redisTemplate.expire("rate:user:101", 60, TimeUnit.SECONDS);
}

if (count > 5) {
    throw new RuntimeException("Rate limit exceeded");
}
```

---

# 11. Redis for Sessions

```yaml
spring:
  session:
    store-type: redis
```

Ensures all instances share same session store.

---

# 12. Interview Questions (Service-Based)

## Basic

1. What is Redis and why is it used?
2. Difference between cache and database?
3. What is cache-aside pattern?
4. What problems does Redis solve in microservices?

## Intermediate

5. What is cache stampede?
6. How does distributed locking work?
7. Why is TTL required in Redis lock?
8. Why do we store UUID in lock value?
9. Difference between Redis lock and DB lock?
10. What happens if Redis goes down?

## Advanced

11. How does Redisson prevent premature lock expiry?
12. When should we use optimistic locking instead of Redis lock?
13. How do you prevent cache penetration?
14. How does rate limiting work in distributed systems?
15. What are the risks of using Redis distributed locks?

---

# 13. Architecture Best Practices

- Always use TTL for locks
- Prefer Redisson over manual implementation
- Design proper cache key naming
- Use distributed cache instead of local cache
- Keep DB connection pool optimized
- Monitor Redis memory usage

---

# Final Summary

Redis in microservices is used for:

- Performance improvement (Caching)
- Concurrency control (Distributed Lock)
- Request control (Rate Limiting)
- Shared state (Sessions)

It improves scalability but must be used carefully with proper TTL, lock safety, and monitoring.

---

# 14. Azure Redis Setup (Step-by-Step)

## Step 1: Create Redis Instance

1. Login to Azure Portal
2. Click **Create a Resource**
3. Search for **Azure Cache for Redis**
4. Click **Create**

Fill:
- Subscription
- Resource Group
- DNS Name (example: my-redis-cache)
- Location (same as app region)
- Pricing Tier (Basic / Standard / Premium)

Click **Review + Create** → **Create**

---

## Step 2: Get Connection Details

After deployment:

1. Open Redis resource
2. Go to **Access Keys**
3. Copy:
   - Hostname
   - Primary key
   - SSL Port (usually 6380)

---

## Step 3: Configure Spring Boot

application.yml

```yaml
spring:
  cache:
    type: redis
  redis:
    host: my-redis-cache.redis.cache.windows.net
    port: 6380
    password: <primary-key>
    ssl: true
```

---

## Step 4: Verify Connection

Start application and check:
- Logs for Redis connection success
- Keys appearing in Redis console

---

## What You Would See in Screenshots (Conceptual)

If screenshots were shown in an interview or documentation, they usually include:

- Azure Portal "Create Redis" page
- Pricing tier selection page
- Access keys page showing hostname and keys
- Redis metrics dashboard (CPU, memory, connections)

Knowing these screens conceptually is enough for interviews.

---

# 15. Kubernetes Deployment Example with Redis

## Architecture

Pods:
- Spring Boot Service (multiple replicas)
- Redis (managed Azure or Redis Helm chart)

Flow:
Client → Service Pod → Redis → DB

---

## Step 1: ConfigMap for Redis Host

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config

data:
  REDIS_HOST: my-redis-cache.redis.cache.windows.net
  REDIS_PORT: "6380"
```

---

## Step 2: Secret for Redis Password

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: redis-secret

type: Opaque
data:
  REDIS_PASSWORD: <base64-password>
```

---

## Step 3: Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: product-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: product-service
  template:
    metadata:
      labels:
        app: product-service
    spec:
      containers:
      - name: product-service
        image: myrepo/product-service:latest
        env:
        - name: SPRING_REDIS_HOST
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: REDIS_HOST
        - name: SPRING_REDIS_PASSWORD
          valueFrom:
            secretKeyRef:
              name: redis-secret
              key: REDIS_PASSWORD
```

---

## Step 4: Service YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: product-service
spec:
  selector:
    app: product-service
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

---

## Interview Explanation

In Kubernetes:
- Multiple pods connect to same Redis
- Distributed cache shared across instances
- Locks and rate limiting work across pods
- ConfigMap and Secrets used for secure configuration

---

End of Notes

