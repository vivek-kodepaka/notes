
# Microservices Reliability & Resilience – Complete Notes

## 1. What is Reliability and Resilience?

**Reliability**
- The system performs correctly and consistently over time.
- Example: APIs respond successfully without errors.

**Resilience**
- The system continues to function even when parts fail.
- Example: One service failing does not bring down the entire system.

In microservices, failures are expected:
- Network failures
- Service crashes
- Timeouts
- Partial failures

Designing for failure is essential.

---

# 2. Common Failure Types in Microservices

1. Network latency
2. Service downtime
3. Database connection issues
4. Message broker delays
5. Dependency failures

---

# 3. Core Reliability & Resilience Patterns

Key techniques used in production systems:

1. Timeouts
2. Retries
3. Circuit Breaker
4. Bulkhead Pattern
5. Rate Limiting
6. Idempotency
7. Graceful Degradation
8. Health Checks
9. Observability
10. Backpressure

---

# 4. Timeouts

## Why Timeouts Matter

Without timeout:
- Thread waits forever
- Resource exhaustion
- Cascading failure

## Best Practice

Always configure:
- Connection timeout
- Read timeout

Example:
Service A calls Service B with timeout of 2 seconds.
If B is slow → Fail fast.

---

# 5. Retries

Retries handle temporary failures like:
- Network glitches
- Transient DB errors

## Retry Strategies

### Fixed Delay
Retry every 2 seconds.

### Exponential Backoff
Retry after:
1s → 2s → 4s → 8s

Preferred in production.

Important:
Retries must be combined with idempotency and circuit breakers.

---

# 6. Circuit Breaker

## Problem
If Service B is down, Service A keeps retrying:
- Threads blocked
- System overload

## Solution
Circuit breaker stops calls temporarily.

### States

1. CLOSED – Normal operation
2. OPEN – Calls blocked
3. HALF-OPEN – Trial requests

---

# 7. Bulkhead Pattern

## Problem
One failing dependency consumes all resources.

## Solution
Isolate resources:
- Separate thread pools
- Separate connection pools

Failure in one does not affect others.

---

# 8. Rate Limiting

## Why Needed
Protect services from:
- Traffic spikes
- Abuse
- DDoS

Example:
Allow 100 requests per second.

---

# 9. Idempotency

Ensures repeated requests produce same result.

Example:
Payment retry should not charge twice.

Techniques:
- Idempotency keys
- Unique transaction IDs

---

# 10. Graceful Degradation

When a dependency fails:
- Return cached data
- Disable optional features

Example:
Recommendation service fails → Show product without recommendations.

---

# 11. Health Checks

Used by:
- Kubernetes
- Load balancers

Types:
- Liveness probe
- Readiness probe

Ensures traffic goes only to healthy instances.

---

# 12. Observability

Three pillars:
1. Logs
2. Metrics
3. Traces

Tools:
- Prometheus
- Grafana
- OpenTelemetry
- ELK Stack

---

# 13. Backpressure

When consumers cannot keep up:
- Queue grows
- Memory pressure

Solutions:
- Throttling
- Reactive streams
- Buffer limits

---

# 14. Real Production Architecture

Typical stack:

API Gateway
   ↓
Microservices
   ↓
Kafka / RabbitMQ
   ↓
Databases

Resilience implemented using:
- Retry
- Circuit breaker
- Timeouts
- Bulkheads
- Monitoring

---

# 15. Comparison Table

| Pattern | Purpose |
|--------|--------|
| Timeout | Fail fast |
| Retry | Handle transient errors |
| Circuit Breaker | Prevent cascading failures |
| Bulkhead | Resource isolation |
| Rate Limiting | Traffic control |
| Idempotency | Prevent duplicate effects |
| Graceful Degradation | Maintain usability |

---

# 16. Interview Questions

1. What is the difference between reliability and resilience?
2. Why is circuit breaker important?
3. Difference between retry and circuit breaker?
4. What is bulkhead pattern?
5. Why must retries use exponential backoff?
6. What is graceful degradation?
7. How does Kubernetes help with resilience?

---

# 17. Golden Interview Answer

"Microservices reliability and resilience are achieved using timeouts, retries with exponential backoff, circuit breakers to prevent cascading failures, bulkhead pattern for resource isolation, rate limiting to control traffic, idempotency to handle retries safely, and strong observability for monitoring and recovery."
