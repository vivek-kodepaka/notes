# Rate Limiting in Spring Boot Microservices (Kubernetes)

------------------------------------------------------------------------

# 1. Introduction

Rate limiting is used to:

-   Prevent abuse (brute force, DDoS)
-   Protect backend services
-   Ensure fair usage
-   Improve system stability

In Kubernetes-based microservices, rate limiting can be applied at
multiple layers.

------------------------------------------------------------------------

# 2. Where Rate Limiting Can Be Applied

1.  API Gateway Level (Recommended)
2.  Kubernetes Ingress Level
3.  Application Level (Spring Boot)
4.  Distributed Redis-Based
5.  Service Mesh (Istio / Envoy)

------------------------------------------------------------------------

# 3. API Gateway Level (Spring Cloud Gateway + Redis)

Best practice: Apply rate limiting at the edge.

## application.yml Example

``` yaml
spring:
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: lb://USER-SERVICE
          predicates:
            - Path=/users/**
          filters:
            - name: RequestRateLimiter
              args:
                redis-rate-limiter.replenishRate: 10
                redis-rate-limiter.burstCapacity: 20
                redis-rate-limiter.requestedTokens: 1
```

## Required Dependency

``` xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
</dependency>
```

------------------------------------------------------------------------

# 4. Kubernetes Ingress (NGINX)

Rate limiting before traffic reaches pods.

## Example Ingress YAML

``` yaml
metadata:
  annotations:
    nginx.ingress.kubernetes.io/limit-rps: "10"
    nginx.ingress.kubernetes.io/limit-burst-multiplier: "2"
```

Pros: - Simple - No code changes

Cons: - Limited customization

------------------------------------------------------------------------

# 5. Application-Level Rate Limiting (Bucket4j)

Used inside Spring Boot app.

## Maven Dependency

``` xml
<dependency>
    <groupId>com.github.vladimir-bukhtoyarov</groupId>
    <artifactId>bucket4j-core</artifactId>
    <version>8.0.1</version>
</dependency>
```

## Example Filter

``` java
Bucket bucket = Bucket4j.builder()
    .addLimit(Bandwidth.simple(10, Duration.ofMinutes(1)))
    .build();

if (bucket.tryConsume(1)) {
    chain.doFilter(request, response);
} else {
    response.setStatus(429);
}
```

Limitation: - Works locally per pod unless backed by Redis.

------------------------------------------------------------------------

# 6. Distributed Rate Limiting with Redis

Architecture:

Client → Gateway → Redis → Service

Use Redis for:

-   Shared counters
-   Atomic increments
-   TTL-based expiry

Example concept:

``` java
Long current = redisTemplate.opsForValue()
    .increment("rate_limit:user123");

redisTemplate.expire("rate_limit:user123", 1, TimeUnit.MINUTES);
```

If current \> limit → return 429.

------------------------------------------------------------------------

# 7. Service Mesh (Istio)

Rate limiting at infrastructure level.

Uses: - Envoy Proxy - External Rate Limit Service

Pros: - No application code change - Centralized enforcement - Highly
scalable

------------------------------------------------------------------------

# 8. Rate Limiting Algorithms

1.  Fixed Window
2.  Sliding Window
3.  Token Bucket (Most common)
4.  Leaky Bucket

Spring Cloud Gateway uses Token Bucket algorithm.

------------------------------------------------------------------------

# 9. Common Interview Points

✔ Prefer rate limiting at gateway level\
✔ Use Redis for distributed systems\
✔ Return HTTP 429 (Too Many Requests)\
✔ Avoid per-pod local counters in multi-pod setups\
✔ Token bucket is scalable and burst-friendly

------------------------------------------------------------------------

# 10. Recommended Architecture (Interview Answer)

"In Kubernetes-based microservices, I prefer implementing rate limiting
at the API Gateway layer using a Redis-backed token bucket algorithm to
ensure distributed consistency, horizontal scalability, and centralized
enforcement."

------------------------------------------------------------------------

END OF NOTES
