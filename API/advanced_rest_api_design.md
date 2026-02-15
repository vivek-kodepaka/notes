# Advanced REST API Design -- Senior Interview Guide

This guide covers advanced REST API concepts frequently discussed in
backend and microservices interviews.

------------------------------------------------------------------------

## 1. Idempotency Keys (Handling Retries Safely)

### Problem

Clients may retry requests due to: - Network failures - Timeouts - Load
balancer retries

For POST requests, retries can create duplicate records.

### Solution: Idempotency Key

Client sends: Idempotency-Key: 12345-abc

Server: - Stores request result using this key - If same key arrives
again, returns stored response

### Example (Spring Boot Concept)

Store key in DB or Redis:

Table: idempotency_key \| response \| status

------------------------------------------------------------------------

## 2. API Caching

Caching reduces: - Latency - DB load - Network traffic

### HTTP Headers Used

Cache-Control ETag If-None-Match

### Example Flow

1.  Server returns: ETag: "abc123"

2.  Client sends: If-None-Match: "abc123"

3.  Server responds: 304 Not Modified

No body sent → faster response.

------------------------------------------------------------------------

## 3. Rate Limiting

Protects services from: - Abuse - Traffic spikes - DDoS

### Common Algorithms

1.  Token Bucket
2.  Leaky Bucket
3.  Fixed Window
4.  Sliding Window (recommended)

### Example Response

HTTP 429 Too Many Requests

Headers: X-RateLimit-Limit X-RateLimit-Remaining

------------------------------------------------------------------------

## 4. HATEOAS (Hypermedia as the Engine of Application State)

Response includes links to next actions.

Example:

{ "orderId": 101, "status": "CREATED", "links": \[ { "rel": "cancel",
"href": "/orders/101/cancel" } \] }

Used in: - Mature REST systems - Discoverable APIs

------------------------------------------------------------------------

## 5. Pagination Best Practices

Use:

GET /orders?page=0&size=20

Response:

{ "content": \[\], "page": 0, "size": 20, "totalElements": 120 }

Avoid: Returning huge datasets.

------------------------------------------------------------------------

## 6. API Versioning Strategies

### URI Versioning

/v1/orders

### Header Versioning

Accept: application/vnd.company.v1+json

### Query Versioning

/orders?version=1

Recommended: URI versioning (most practical).

------------------------------------------------------------------------

## 7. Retry Strategy (Client + Server)

Retries should be: - Limited - Exponential backoff

Example delays: 1s → 2s → 4s → 8s

Never retry: POST without idempotency key.

------------------------------------------------------------------------

## 8. Circuit Breaker Pattern

Problem: Calling a failing service repeatedly wastes resources.

Solution: Circuit breaker stops calls temporarily.

States: - Closed - Open - Half-open

Tools: Resilience4j Hystrix (legacy)

------------------------------------------------------------------------

## 9. API Gateway Role

In microservices:

Client → API Gateway → Services

Gateway responsibilities: - Authentication - Rate limiting - Routing -
Logging - SSL termination

Tools: - Kong - NGINX - Spring Cloud Gateway - AWS API Gateway

------------------------------------------------------------------------

## 10. Security Best Practices

-   Always use HTTPS
-   JWT or OAuth2
-   Input validation
-   Avoid exposing internal IDs
-   Use API keys for external consumers

------------------------------------------------------------------------

## 11. Consistent Response Structure

Success:

{ "status": 200, "message": "Success", "data": {} }

Error:

{ "status": 400, "error": "Invalid request" }

------------------------------------------------------------------------

## 12. Logging and Observability

Important in production:

-   Correlation ID
-   Request ID
-   Distributed tracing

Headers: X-Correlation-ID

Tools: - Prometheus - Grafana - ELK stack

------------------------------------------------------------------------

## 13. Interview Answer (Senior Level)

If interviewer asks:

"How do you design production-ready REST APIs?"

Answer:

I design REST APIs using resource-based URLs, proper HTTP methods, and
standardized response formats. For production readiness, I include
pagination, filtering, caching using ETags, rate limiting, retries with
exponential backoff, and idempotency keys for safe retries. APIs are
secured with HTTPS and JWT, monitored with logging and tracing, and
exposed through an API gateway in microservices architecture.

------------------------------------------------------------------------

## 14. Quick Senior Engineer Checklist

-   Idempotency handled
-   Pagination implemented
-   Rate limiting enabled
-   Caching strategy defined
-   Versioning planned
-   Observability added
-   API Gateway used
-   Security enforced
