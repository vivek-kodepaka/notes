span

<details>
    <summary>what is micro service</summary>

```text
    Microservices = Application split into small independent services that:
      1. run independently
      2. have their own database
      3. communicate over network (REST, messaging)
```

</details>

<details>
<summary>monolith vs microservices</summary>

```text
| Feature    | Monolith           | Microservices    |
| ---------- | ------------------ | ---------------- |
| Deployment | Single             | Multiple         |
| Scaling    | Whole app          | Service level    |
| Technology | Same stack         | Different stacks |
| Failure    | Whole app affected | Isolated         |
| Complexity | Low initially      | High initially   |
```

```text
Advantages:
   Independent deployment
   Fault isolation
   Technology flexibility
   Better scaling

Disadvantages:
   Network latency
   Distributed transactions
   Deployment complexity
```

</details>
</details>

<details>
<summary>Designing Microservice</summary>

```text
Domain Driven Design (DDD)
Break system based on business capability, not technical layers

How do you split microservices?
  Answer:
  Business capability
  Data ownership
  Team ownership

Single Responsibility Principle
  Each service should do one business job well.

Example:
  User Service should not handle orders.
```

</details>

<details>
<summary>API Design Best Practices</summary>

- API design best practices are guidelines to make APIs easy to use, consistent, scalable, and maintainable.

```text
RESTful APIs
Versioning--> /api/v1/orders
Idempotency
Pagination
```

<details>
<summary>RESTful API</summary>

```text
1. RESTful APIs

REST = Representational State Transfer

A RESTful API follows principles:
Core Principles

1. Stateless
   Each request contains all required information.
   Server does NOT store session.

2. Resource-based URLs
   /orders
   /orders/100
Not:
    /getOrder?id=101

3. Use HTTP methods correctly

GET /orders
POST /orders
PUT /orders/1
DELETE /orders/1


4. Representation in JSON
   Most APIs use JSON.

Interview One-Liner

RESTful API = Stateless API that exposes resources using HTTP methods.
```

```text
1. Use nouns, not verbs
    GET /orders        ✔
    POST /orders       ✔
    GET /getOrders     ✘
 
2. Use proper HTTP methods
    | Method | Purpose          |
    | ------ | ---------------- |
    | GET    | Read data        |
    | POST   | Create           |
    | PUT    | Update (full)    |
    | PATCH  | Update (partial) |
    | DELETE | Remove           |

```

[http_methods_advanced_notes.md](subNotes/http_methods_advanced_notes.md)

```text
3. Use proper HTTP status codes
    | Code | Meaning      |
    | ---- | ------------ |
    | 200  | Success      |
    | 201  | Created      |
    | 400  | Bad request  |
    | 401  | Unauthorized |
    | 404  | Not found    |
    | 500  | Server error |

4. Consistent response format
   {
  "status": "success",
  "data": {...},
  "message": "Order fetched"
}

5. Proper naming
    /users
    /orders
    /products

```

</details>
<details>
<summary>Versioning</summary>

```text
Versioning ensures backward compatibility when APIs change.

1. URL Versioning (most common)
    /api/v1/orders
    /api/v2/orders
  
2. Header Versioning
    Accept: application/vnd.company.v1+json

3. Query Parameter
    /orders?version=1
```

</details>
<details>
<summary>Idempotency</summary>

```text
Idempotent means:
Calling the same API multiple times gives the same result.

GET, put (updates whole obj),delete --> idempotent

post and patch --> not idemopenent
use Idempotency-Key: abc123 for retries
```

</details>

<details>
<summary>Pagination</summary>

```text
Pagination is used when data is large.
Instead of:
   GET /orders
Use:
   GET /orders?page=1&size=10

Example Response
{
  "page": 1,
  "size": 10,
  "totalPages": 50,
  "data": [...]
}
```

```text

Types of Pagination

1. Offset Pagination
   ?page=1&size=10
   
   Hibernate: select c1_0.customer_id,c1_0.active,c1_0.address_id,c1_0.create_date,c1_0.email,c1_0.first_name,c1_0.last_name,c1_0.last_update,c1_0.store_id from customer c1_0 order by c1_0.first_name limit ?,?
   Hibernate: select count(c1_0.customer_id) from customer c1_0
   
   Two quires executed by jpa one offset and other count
2. Cursor Pagination (better for large data)
   /orders?cursor=abc123
   @Query("select c from Customers c where c.customerId > :cursor  ")
    Page<Customers> findnext(@Param("cursor") Integer cursor,Pageable pageable);
}

Used by:
Twitter
Instagram

Interview Tip
Cursor pagination is more scalable than offset.
```

</details>
</details>

<details>
<summary>Communication Between Microservices</summary>

<details>
<summary>Synchronous Communication</summary>

```text
Java HttpClient

java 11 both synchronous and asynchronous,
 synchronous (Blocking):
1. HttpClient client = HttpClient.newHttpClient();
2.HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts/1"))
                .GET()
                .build();
3. HttpResponse<String> response =
                client.send(request, HttpResponse.BodyHandlers.ofString());  
asynchronous(Non-Blocking)

1.client.sendAsync(request, HttpResponse.BodyHandlers.ofString())
      .thenApply(HttpResponse::body)
      .thenAccept(System.out::println)
      .join();
 2. CompletableFuture.
 
 
Timeout:
HttpClient client = HttpClient.newBuilder()
        .connectTimeout(Duration.ofSeconds(5))
        .build();

HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create("https://example.com"))
        .timeout(Duration.ofSeconds(5))
        .GET()
        .build();

| Feature              | HttpClient | WebClient | Feign     |
| -------------------- | ---------- | --------- | --------- |
| Plain Java support   | Yes        | No        | No        |
| Spring Boot friendly | Medium     | Excellent | Excellent |
| Async support        | Yes        | Yes       | Limited   |
| Retry support        | Manual     | Easy      | Easy      |
| Circuit breaker      | Manual     | Easy      | Easy      |
| Load balancing       | Manual     | Supported | Supported |
| JSON mapping         | Manual     | Automatic | Automatic |
| Boilerplate          | Medium     | Low       | Very Low  |

OkHTTP

Apache HttpClient
```

</details>

<details>
<summary>Asynchronous Communication</summary>

```text

```

</details>
</details>


---

# Data Consistency in microservices

[data_consistency_microservices.md](data_consistency_microservices.md)

---

# Reliability & Resilience

[microservices_reliability_resilience.md](microservices_reliability_resilience.md)

[resilience4j_springboot_notes.md](resilience4j_springboot_notes.md)


---

# Service Discovery

[service_discovery_notes.md](service_discovery_notes.md)

Eureka
Consul
Kubernetes DNS
---
# API GATEWAY

Responsibilities:
 Routing
 Authentication
 Rate limiting

Tools:
 Spring Cloud Gateway
 Kong
 Nginx
---
# Security in Microservices

JWT Authentication
OAuth2
---
# Configuration Management
Problem:
Multiple services, different configs.

Solution:
  Config Server
Tools:
  Spring Cloud Config
  Kubernetes ConfigMaps
---
# Deployment & Containerization

- Docker
[docker_complete_notes.md](docker_complete_notes.md)
- Kubernetes

[kubernetes_complete_notes.md](kubernetes_complete_notes.md)

Build Jar → Docker Image → Kubernetes Deployment

Kubernetes handles:
Scaling
Restart
Load balancing
---
# Load Balancing

Types:
Client Side:
Ribbon (old)
Spring Cloud LoadBalancer

Server Side:
Nginx
Kubernetes Service
gslb

---
# Observability (Production Systems)

Very important in real systems.

Logging
Centralized logging:
ELK Stack
Monitoring
Prometheus
Grafana
Distributed Tracing
Zipkin
Jaeger
Helps trace:

Request → Gateway → Order → Payment

---
# CI/CD in Microservices

Pipeline:

Code → Build → Test → Docker → Deploy

Tools:
Jenkins
GitHub Actions
ArgoCD