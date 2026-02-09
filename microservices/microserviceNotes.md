
<details>
<summary> 1. Fundamentals </summary>

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

<details>
<summary>remove</summary>

```text
Designing Microservices

Communication

Data Management

Reliability & Resilience

Deployment & DevOps

Observability & Production Concerns








3. Why Spring Boot is Popular in Microservices

Important reasons:

Embedded server

Easy containerization

Actuator

External configuration

Dependency management

Spring Boot auto-configures components based on dependencies, reducing configuration overhead

4. Common Mistakes Developers Do in Spring Boot (Real Production Issues)

This is the part interviewers really like.

Mistake 1: Using Stateful Services

Wrong:

@Service
public class CounterService {
int count = 0;
}


Problem:

Multiple pods

Each pod has different memory

Data inconsistent

Rule:
Services must be stateless

Mistake 2: In-memory Cache in Multiple Pods

Wrong:

Using HashMap cache inside service

Problem:

Pod1 cache != Pod2 cache

Correct:

Redis

Distributed cache

Mistake 3: No Connection Pool Tuning

Default settings:

HikariCP small pool

Problem:

DB bottleneck under load

Correct:
Tune:

spring.datasource.hikari.maximum-pool-size

Mistake 4: Logging Too Much

Common issue:

Logging request body

Logging large JSON

Problem:

CPU spike

Disk I/O

In Kubernetes:

Logs shipped to ELK

Cost increases

Mistake 5: Not Using Timeouts

Big production bug:

restTemplate.getForObject()


Without timeout:

Thread hangs

Pod freezes

Correct:
Always configure:

connect timeout

read timeout

Mistake 6: Not Handling Retry & Circuit Breaker

In microservices:

Downstream service fails

Threads pile up

Correct:
Use:

Resilience4j

Retry

Circuit breaker

Mistake 7: Large Fat Jar and Slow Startup

Problem:

Pod restart slow

Kubernetes autoscaling slow

Solution:

Native image

Layered Docker images

Spring Boot 3 improved startup and memory footprint for containers

Mistake 8: Not Externalizing Config

Wrong:

application.properties inside jar


Correct:

ConfigMap

Secrets

Mistake 9: Ignoring Health Checks

Production issue:

Pod unhealthy but traffic routed

Correct:

readiness probe

liveness probe

actuator health

Mistake 10: Using Blocking Calls Everywhere

Bad:

RestTemplate heavy usage

Slow downstream

Better:

WebClient

Async

5. Mistakes Specifically in Multiple Pods (Important)

This is very interview-relevant.

Problem	Why Happens	Fix
Duplicate processing	Multiple pods consume same event	Distributed locks
Session loss	Stateful app	Use JWT or Redis
Cache inconsistency	Local cache	Distributed cache
Race conditions	Multiple writers	DB constraints
Thread pool exhaustion	Blocking calls	Async / tuning
6. Golden Rules for Microservices with Spring Boot

Memorize this:

Stateless services

External configuration

Distributed cache

Timeout + retry

Health checks

Observability (metrics + logs)

If you say these in interview → strong impression.

7. What Interviewers Actually Expect You to Know

Most important topics:

Actuator

Profiles

ConfigMap usage

Connection pooling

Thread pools

RestTemplate vs WebClient

Circuit breaker

Not version history.

If You Want to Really Stand Out (High-Level Interview Question)

I can explain:

What really breaks when you scale Spring Boot from 1 pod → 10 pods

This is one of the strongest real-world interview topics for backend engineers.

yes please

When a Spring Boot service runs on 1 pod, many problems are hidden.
When you scale to 5–10 pods, real distributed-system issues appear.

Here are the most common things that break, explained in a practical way.

What Breaks When You Scale Spring Boot from 1 Pod → Multiple Pods
1. Session Breaks (Login Problems)
   What works in 1 pod

User logs in → session stored in memory → works fine.

What breaks in multiple pods

Request 1 → Pod A
Request 2 → Pod B

Pod B does not have the session → user logged out.

Fix

Use:

JWT (best for microservices)

Redis session store

2. In-Memory Cache Becomes Wrong
   What works in 1 pod
   Map<Long,User> cache = new HashMap<>();

What breaks in multiple pods

Pod A cache ≠ Pod B cache

Different results returned.

Fix

Use:

Redis

Hazelcast

Distributed cache

3. Duplicate Processing (Very Common Production Bug)
   Scenario

Multiple pods consume events or process jobs.

Example:

Two pods pick the same order

Order processed twice

Fix

Use:

DB unique constraints

Distributed locks

Message queues with proper acknowledgment

4. Race Conditions in Database
   Example

Two pods booking same slot:

Pod A → slot available
Pod B → slot available

Both book.

Fix

Use:

Optimistic locking

Version column

Unique constraints

Very common interview scenario (doctor slot booking like your project).

5. Connection Pool Exhaustion
   What works in 1 pod

Default pool = 10 connections

What breaks

10 pods × 10 connections = 100 DB connections
DB crashes or slows.

Fix

Tune:

maximum-pool-size


Also:

Use PgBouncer or proxy

6. Logging Explosion
   What works in 1 pod

Logs manageable.

What breaks

10 pods:

Disk fills

ELK cost increases

CPU usage spikes

Fix

Structured logging

Log only necessary data

7. Health Checks Not Configured
   What happens

Pod is slow but still receiving traffic.

Users see:

timeouts

errors

Fix

Use Actuator:

readiness

liveness

Kubernetes stops routing traffic.

8. Thread Pool Exhaustion
   What works in 1 pod

Blocking calls are fine.

What breaks

High traffic:

Threads blocked

Requests pile up

Fix

Timeout

Async processing

WebClient

9. Configuration Problems
   What works in 1 pod

Hardcoded configs.

What breaks

Different environments:

Dev

QA

Prod

Fix

Use:

ConfigMap

Secrets

Profiles

10. Startup Time Becomes a Problem
    What works

Pod restart takes 40 seconds.

What breaks

Autoscaling slow.

Fix

Layered Docker images

Lazy initialization

Native image

11. Load Balancing Issues

Without:

Sticky sessions

Proper readiness probes

Traffic gets routed to:

Starting pods

Unhealthy pods

12. Distributed Transactions Assumptions

Developers assume:

One service = one DB transaction

In microservices:

Network failures happen

Partial failure happens

Fix:

Saga pattern

Idempotency

Simple Mental Model (Very Important)

When scaling pods, these 4 things break:

Memory

Threads

Database

Network

Memorize this — very useful in interviews.

Golden Rule of Microservices

Never rely on:

Local memory

Local cache

Local session

Local files

Pods are stateless and disposable.

Interview-Level Answer (Short Version)

If interviewer asks:
What changes when scaling from 1 pod to multiple pods?

Answer:

Problems arise with session management, caching, concurrency, and distributed coordination. Applications must be stateless, use distributed caches, implement proper connection pooling, and handle race conditions and retries.

One Real-World Example (Good for Interviews)

Doctor appointment booking system:

If two pods:

Both see slot available

Both book

Solution:

Unique constraint on slot_id + time

Optimistic locking

Interviewers love this example.

If You Want to Go One Level Stronger (Senior-Level Knowledge)

I can explain:

The 7 production mistakes Spring Boot developers make in Kubernetes that cause outages

This is something even experienced engineers often miss.

yes

Here are 7 real production mistakes Spring Boot developers make in Kubernetes that often cause outages. These are practical, interview-level and also useful in real projects.

7 Production Mistakes That Cause Outages (Spring Boot + Kubernetes)
1. No Timeouts on External Calls
   Problem

Developers call:

RestTemplate / WebClient
DB calls
Other microservices


without proper timeouts.

If a downstream service hangs:

Threads block

Tomcat thread pool fills

Pod stops responding

Kubernetes restarts pod

Fix

Always configure:

connection timeout

read timeout

circuit breaker

Golden rule:
Every network call must have a timeout.

2. Wrong Thread Pool Configuration
   Problem

Spring Boot default thread pool works in low traffic but fails in production.

Symptoms:

Requests stuck

High response time

CPU low but system slow

Reason:
Thread pool exhausted.

Fix

Tune:

server.tomcat.threads.max
spring.task.execution.pool.max-size


And monitor thread metrics.

3. Not Configuring Readiness and Liveness Probes
   Problem

Kubernetes sends traffic to a pod that:

is still starting

DB not connected

cache not loaded

Users get errors.

Fix

Use Actuator:

/actuator/health/readiness
/actuator/health/liveness


This is mandatory in production.

4. Connection Pool Misconfiguration (Very Common)
   Problem

Each pod opens DB connections.

Example:

Pool size = 20

Pods = 10

Total = 200 connections

DB crashes.

Fix

Tune HikariCP:

spring.datasource.hikari.maximum-pool-size


Rule:
Connection pool × pods ≤ DB max connections

5. Using In-Memory State
   Problem

Developers store:

cache in HashMap

session in memory

counters in fields

With multiple pods:

inconsistent data

random behavior

Fix

Use:

Redis

DB

Distributed cache

JWT for sessions

Golden rule:
Pods must be stateless.

6. Logging Too Much (Hidden Production Killer)
   Problem

Logging:

full request body

large JSON

debug logs in production

Result:

CPU spikes

disk I/O heavy

ELK cost increases

pods slow down

Fix

Use:

INFO or WARN

structured logging

avoid large payload logs

7. Not Handling Pod Restarts Properly
   Problem

Kubernetes can restart pods anytime:

deployment

scaling

node restart

If application:

doesn't retry

doesn't handle transient failures

Users see errors.

Fix

Use:

retry logic

idempotency

graceful shutdown

Enable:

server.shutdown=graceful

Bonus Mistake (Very Important in Interviews)
Duplicate Processing in Multiple Pods

Scenario:
Two pods process same message or job.

Result:

duplicate payments

duplicate orders

Fix:

unique constraints

distributed locks

message queues with acknowledgments

Mental Model to Remember (Interview Gold)

When scaling pods, failures come from:

Threads

Database

Network calls

State management

If you say this in an interview, it shows real production understanding.

One Strong Interview Answer (Memorize This)

If interviewer asks:

What are common issues when running Spring Boot in Kubernetes?

Answer:

Common issues include missing timeouts, connection pool misconfiguration, lack of readiness probes, improper thread pool tuning, storing state in memory, excessive logging, and not handling pod restarts or retries properly.

That answer sounds very strong and practical.
```
</details>
