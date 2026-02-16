# Spring Core Deep Dive (Study Notes)

## 1. Why Spring Exists

### Problem in Traditional Java:

-   Tight coupling (class depends on concrete implementation)
-   Object creation inside business class
-   Hard to change implementation
-   Violates Open/Closed Principle
-   No runtime flexibility (profile/config-based switching difficult)

### Traditional Example:

OrderService -\> creates -\> PaymentService

### Spring Approach (IoC):

Container -\> creates -\> PaymentService\
Container -\> injects -\> OrderService

Inversion of Control means object creation responsibility is moved from
application code to container.

------------------------------------------------------------------------

## 2. IoC Container

### Key Interfaces:

-   BeanFactory (lazy initialization)
-   ApplicationContext (eager initialization by default)

### Important Clarification:

Spring first registers **BeanDefinition (metadata)**, not actual
objects.

Lifecycle: Scan → Register BeanDefinitions → Instantiate → Inject →
Initialize → Ready

------------------------------------------------------------------------

## 3. Dependency Injection

Types: - Constructor Injection (recommended) - Setter Injection - Field
Injection (not recommended)

### Circular Dependency

Constructor Injection: - Fails at container startup (runtime, not
compile-time)

Setter/Field Injection: - Historically worked for singleton beans -
Disabled by default in modern Spring Boot

### Design Insight:

Circular dependency indicates SRP violation and poor separation of
responsibilities.

------------------------------------------------------------------------

## 4. Bean Lifecycle (Accurate Order)

1.  BeanDefinition Registration
2.  Instantiation
3.  Dependency Injection
4.  Aware callbacks
5.  BeanPostProcessor (before init)
6.  Initialization:
    -   @PostConstruct
    -   afterPropertiesSet()
    -   custom init method
7.  BeanPostProcessor (after init) → AOP proxy wrapping happens here
8.  Bean ready
9.  Destroy phase:
    -   @PreDestroy
    -   destroy method

------------------------------------------------------------------------

## 5. Hooks in Spring

### @PostConstruct

-   Bean-level initialization
-   Runs after dependency injection

### InitializingBean

-   Older style
-   Couples class with Spring

### BeanPostProcessor

-   Intercepts ALL beans
-   Used internally for:
    -   @Transactional
    -   @Async
    -   @Cacheable
    -   AOP Proxies

------------------------------------------------------------------------

## 6. Proxy-Based AOP

Spring does NOT modify original class.

It creates:

Proxy → RealService

External Call: OtherBean → Proxy → RealService

Internal Call (self-invocation): RealService → this.method() (bypasses
proxy)

Result: @Transactional fails in self-invocation.

```
if you add any Transactional or other inside private ,final and static --> proxy does not work
because proxy    --> wraps original obj
 payment$proxy   --> payment
payment$proxy extends payment  (internally)
so only overridden methods visible to payment$proxy
```

------------------------------------------------------------------------

## 7. Self Invocation Problem

Transactional method called inside same class: - Does NOT go through
proxy - No new transaction created - If outer method has transaction →
inner joins same transaction

Solution: Move transactional method to another bean.

------------------------------------------------------------------------

## 8. Transaction Propagation

Default: Propagation.REQUIRED - Join existing transaction - Create new
if none exists

REQUIRES_NEW: - Suspends current transaction - Starts new transaction -
Only works if method call goes through proxy

------------------------------------------------------------------------

## 9. Proxy Types in Spring

### 1. JDK Dynamic Proxy

-   Used if interface exists
-   Proxies interface methods only

### 2. CGLIB

-   Used if no interface
-   Creates subclass at runtime

Cannot proxy: - final methods - private methods - static methods

------------------------------------------------------------------------

## 10. AspectJ vs Proxy-Based AOP

Proxy-Based AOP: - Wraps bean - Self-invocation fails - Lightweight and
common

AspectJ: - Modifies bytecode - Self-invocation works - More powerful but
complex

------------------------------------------------------------------------

# Common Mistakes Corrected

1.  Circular dependency is NOT compile-time error (it's runtime
    container error).
2.  Self-invocation does NOT go through proxy.
3.  Creating new instance manually bypasses Spring container.
4.  @Transactional does not work on private methods.
5.  REQUIRES_NEW does not work during self-invocation.
6.  ApplicationContext injection in business class violates DIP.
7.  BeanDefinition metadata is created before actual bean instantiation.

------------------------------------------------------------------------

# Senior-Level Interview Phrases

-   "Spring uses proxy-based AOP by default."
-   "Self-invocation bypasses proxy."
-   "Transaction propagation depends on proxy interception."
-   "Circular dependency indicates design smell."
-   "BeanPostProcessor is responsible for wrapping beans with proxies."

------------------------------------------------------------------------

End of Notes
