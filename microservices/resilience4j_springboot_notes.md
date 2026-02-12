
# Implementing Reliability & Resilience in Spring Boot using Resilience4j

This guide explains how to implement:
- Timeouts
- Retry
- Circuit Breaker
- Bulkhead
- Rate Limiting (overview)
using Spring Boot and Resilience4j.

---

# 1. What is Resilience4j?

Resilience4j is a lightweight fault‑tolerance library designed for Java 8 and functional programming.

It provides:
- Retry
- Circuit Breaker
- Bulkhead
- Rate Limiter
- TimeLimiter

Works well with:
- Spring Boot
- WebClient
- RestTemplate
- Feign

---

# 2. Maven Dependency

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
</dependency>
```

---

# 3. Timeout (TimeLimiter)

Timeout prevents waiting indefinitely.

## Configuration (application.yml)

```yaml
resilience4j.timelimiter:
  instances:
    paymentService:
      timeoutDuration: 2s
```

---

# 4. Retry

Retry handles temporary failures.

## Configuration

```yaml
resilience4j.retry:
  instances:
    paymentService:
      maxAttempts: 3
      waitDuration: 2s
```

---

## Example Code

```java
@Retry(name = "paymentService", fallbackMethod = "fallbackPayment")
public String callPaymentService() {
    return restTemplate.getForObject("http://payment/api", String.class);
}

public String fallbackPayment(Exception e) {
    return "Payment service temporarily unavailable";
}
```

---

# 5. Circuit Breaker

Prevents cascading failures.

## Configuration

```yaml
resilience4j.circuitbreaker:
  instances:
    paymentService:
      slidingWindowSize: 10
      failureRateThreshold: 50
      waitDurationInOpenState: 10s
```

---

## Example Code

```java
@CircuitBreaker(name = "paymentService", fallbackMethod = "fallbackPayment")
public String callPaymentService() {
    return restTemplate.getForObject("http://payment/api", String.class);
}
```

---

# 6. Bulkhead Pattern

Limits concurrent calls to protect resources.

## Configuration

```yaml
resilience4j.bulkhead:
  instances:
    paymentService:
      maxConcurrentCalls: 5
```

---

## Example Code

```java
@Bulkhead(name = "paymentService")
public String callPaymentService() {
    return restTemplate.getForObject("http://payment/api", String.class);
}
```

---

# 7. Combining Retry + Circuit Breaker

Best practice:

```java
@Retry(name = "paymentService")
@CircuitBreaker(name = "paymentService", fallbackMethod = "fallbackPayment")
public String callPaymentService() {
    return restTemplate.getForObject("http://payment/api", String.class);
}
```

---

# 8. WebClient Example

```java
webClient.get()
    .uri("/payment")
    .retrieve()
    .bodyToMono(String.class);
```

Resilience4j works the same using annotations or filters.

---

# 9. Fallback Methods

Rules:
- Same return type
- Exception parameter optional
- Must be in same class

---

# 10. Rate Limiter (Overview)

Limits requests per time window.

Example config:

```yaml
resilience4j.ratelimiter:
  instances:
    paymentService:
      limitForPeriod: 10
      limitRefreshPeriod: 1s
```

---

# 11. Best Practices in Production

Always combine:
- Timeout
- Retry
- Circuit Breaker
- Idempotency

Avoid:
- Infinite retries
- Very short timeouts
- Retrying non‑idempotent operations

---

# 12. Real Production Flow

Service A calls Service B:

1. Timeout configured
2. Retry if transient failure
3. Circuit breaker opens if failures exceed threshold
4. Fallback response returned

---

# 13. Observability Integration

Resilience4j integrates with:
- Micrometer
- Prometheus
- Grafana

Metrics available:
- Failure rate
- Slow calls
- Retry attempts

---

# 14. Interview Questions

1. Difference between Retry and Circuit Breaker?
2. Why combine Retry with Circuit Breaker?
3. What happens when Circuit Breaker opens?
4. What is fallback?
5. Why timeout is important in microservices?

---

# 15. Golden Interview Answer

"In Spring Boot microservices, resilience is implemented using Resilience4j by configuring timeouts to fail fast, retries for transient failures, circuit breakers to prevent cascading failures, and bulkheads to isolate resources. These patterns ensure services remain available even during partial failures."
