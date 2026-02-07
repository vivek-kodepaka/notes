### Diffrent ways to make http communicaation
1. Raw Socket Programming (Very old school)
2. ```java 
   Socket socket = new Socket("localhost", 8080);
   ```
   ```text
    Problems
    ❌ Manual HTTP parsing
    ❌ Hard to maintain
    ❌ No retry/timeout
    ❌ Reinvent everything
    ```
3. Dont use

### 2. HttpURLConnection (Java built-in legacy)
```java
URL url = new URL("http://service/api");
HttpURLConnection con = (HttpURLConnection) url.openConnection();

```
```drawbacks
❌ Very verbose
❌ Blocking
❌ Hard to configure
❌ No pooling
```


### 3. Apache HttpClient / OkHttp
```java
CloseableHttpClient client = HttpClients.createDefault();

```
```text
Pros
✅ Connection pooling
✅ Timeouts
✅ Stable

Cons
❌ Still blocking
❌ Boilerplate
❌ Manual retry logic
```


### 4. RestTemplate (Spring – old standard)
```java
RestTemplate rest = new RestTemplate();
rest.getForObject(url, User.class);

```
```text
Pros
✅ Simple
✅ Easy
✅ Good for beginners

Cons
❌ Blocking
❌ Deprecated by Spring Framework
❌ Poor scaling under high traffic

Use
👉 Small apps only
```

### Rest Client
✅ Introduced in

👉 Spring 6 / Boot 3
Blocking (sync) but modern API
RestClient restClient = RestClient.create();

```java
User user = restClient.get()
.uri("http://localhost:8081/users/1")
.retrieve()
.body(User.class);
```

```text
Pros
✅ Very clean API
✅ Fluent style
✅ Simple
✅ Better than RestTemplate
✅ Good for sync calls
✅ Less boilerplate

Cons
❌ Still blocking
❌ Not reactive
❌ Not ideal for very high traffic
```

### 5. WebClient (Spring Reactive) ⭐ Recommended
```java
WebClient webClient = WebClient.create();

Mono<User> user =
    webClient.get()
             .uri(url)
             .retrieve()
             .bodyToMono(User.class);

```

```text
Pros
✅ Non-blocking
✅ High performance
✅ Fewer threads
✅ Timeouts/retry/filter easily
✅ Best for microservices

Cons
⚠️ Slight learning curve (Mono/Flux)

Use
👉 ⭐ Production microservices (recommended)
```


### 6. Feign Client (Declarative REST) ⭐⭐⭐ Best DX
```java
@FeignClient(name="user-service")
public interface UserClient {
   @GetMapping("/users/{id}")
   User getUser(@PathVariable Long id);
}

```

```text
Pros
✅ Cleanest code
✅ Declarative
✅ Built-in load balancing
✅ Retry
✅ Circuit breaker
✅ Eureka/Service discovery
✅ Perfect for microservices

Cons
⚠️ Slight abstraction (less control)

Use
👉 ⭐⭐⭐ Best for most Spring Boot microservices
```


### 7. Event Driven (Kafka / Async) ⭐⭐⭐⭐ Ultimate scale

```text
Service A → publish event → Kafka → Service B consumes

Pros

✅ No blocking
✅ Super scalable
✅ Decoupled
✅ Reliable
✅ Best for high traffic

Cons

❌ Not request/response
❌ More infra

Use

👉 Payments, orders, notifications, async workflows
```
