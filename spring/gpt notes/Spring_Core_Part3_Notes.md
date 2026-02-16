# Spring Core Deep Dive -- Part 3

## Topics Covered After: AspectJ vs Proxy-Based AOP

------------------------------------------------------------------------

# 1. Bean Scopes (Deep Understanding)

## Singleton Scope (Default)

Definition: One instance per ApplicationContext.

Key Properties: - Shared across all requests - Stored in singleton
cache - Must be stateless

Why Spring chose Singleton as default: - Web apps are multi-threaded -
Services are typically stateless - Reduces object creation overhead -
Improves performance

### Thread Safety Issue

Example:

private int counter = 0;

public void increment() { counter++; }

Problem: - Multiple threads modify same variable - Lost update problem

Conclusion: Never store mutable state in singleton services.

Correct places for state: - Database - Redis - Distributed cache - Kafka
or external systems

------------------------------------------------------------------------

# 2. Prototype Scope (Deep Understanding)

Definition: A new bean instance is created every time it is requested
from the container.

Important Behavior: If injected into singleton: - Only one instance is
created at injection time. - Not recreated automatically.

Why? Dependency injection happens only once during singleton creation.

### Correct Pattern

Use ObjectProvider:

@Autowired ObjectProvider`<PrototypeBean>`{=html} provider;

PrototypeBean bean = provider.getObject();

Each call → new instance.

------------------------------------------------------------------------

# 3. Request Scope

Definition: One bean instance per HTTP request.

Lifecycle: Created when request starts. Destroyed when request ends.

### Injection into Singleton

Spring injects a proxy instead of real object.

Flow:

Singleton → Scoped Proxy → Actual Request Bean

Why proxy? Because request bean does not exist at application startup.

------------------------------------------------------------------------

# 4. Scoped Proxy Concept

Used when: Short-lived bean injected into longer-lived bean.

Examples: - Request into Singleton - Session into Singleton

Proxy resolves actual instance at runtime.

------------------------------------------------------------------------

# 5. Internal Singleton Caches

Spring uses:

DefaultSingletonBeanRegistry

Important Maps:

1.  singletonObjects Fully initialized beans

2.  earlySingletonObjects Partially initialized beans

3.  singletonFactories Factories used for early references

Purpose: Resolve circular dependencies.

------------------------------------------------------------------------

# 6. Circular Dependency Internals

Constructor Injection:

A needs B B needs A

Fails because: Constructor requires fully initialized dependency.

Setter Injection:

Steps: 1. Create empty object 2. Expose early reference 3. Inject
dependencies later

Works but unsafe.

Design Insight: Circular dependency indicates: - Tight coupling - SRP
violation - Hard testing - Hard maintenance

Best solution: Redesign architecture.

------------------------------------------------------------------------

# 7. Autowiring Resolution Order (Detailed)

When Spring sees:

@Autowired private PaymentService service;

Resolution steps:

1.  Match by type
2.  If multiple → check @Primary
3.  If multiple → match bean name with field/parameter name
4.  If multiple → check @Qualifier
5.  If still ambiguous → NoUniqueBeanDefinitionException
6.  If no beans found → NoSuchBeanDefinitionException

------------------------------------------------------------------------

# 8. @Primary vs @Qualifier

@Primary: Default candidate when multiple beans exist.

@Qualifier: Explicit selection.

Priority: @Qualifier overrides @Primary.

------------------------------------------------------------------------

# 9. Optional Dependencies

Approaches:

1.  @Autowired(required=false) Risk: NullPointerException

2.  Optional`<T>`{=html} Safer and readable.

3.  ObjectProvider`<T>`{=html} Advanced and lazy resolution.

------------------------------------------------------------------------

# 10. ObjectProvider Capabilities

Supports:

-   getObject()
-   getIfAvailable()
-   getIfUnique()
-   stream()

Best for: - Prototype beans - Lazy resolution - Iterating beans

------------------------------------------------------------------------

# 11. Collection Injection

List Injection:

@Autowired List`<PaymentService>`{=html} services;

Injects all beans of matching type.

Ordering: Use @Order or Ordered.

Lower value = higher priority.

------------------------------------------------------------------------

# 12. Map Injection

@Autowired Map\<String, PaymentService\> services;

Key: Bean name

Value: Bean instance

------------------------------------------------------------------------

# 13. Strategy Pattern Using Spring

Problem: Using bean name as key is fragile.

Better design:

Each service defines:

String getType();

Convert List → Map:

Key → business type Value → service instance

Benefits: - Decoupled from bean names - Refactoring safe -
Business-driven mapping

Duplicate keys: Collectors.toMap throws IllegalStateException (fail
fast).

------------------------------------------------------------------------

# 14. Design Lessons Learned

1.  Singleton beans must be stateless.
2.  Circular dependencies indicate poor design.
3.  Proxy mechanism is core to Spring AOP.
4.  Self invocation bypasses proxy.
5.  Scoped proxy solves lifecycle mismatch.
6.  ObjectProvider is powerful for lazy and prototype use cases.
7.  Strategy pattern integrates naturally with Spring collections.

------------------------------------------------------------------------

# Mind Map (Concept Flow)

Spring Core Internals │ ├── Scopes │ ├── Singleton │ ├── Prototype │ └──
Request │ ├── Dependency Injection │ ├── Constructor │ ├── Setter │ └──
Field │ ├── Container Internals │ ├── singletonObjects │ ├──
earlySingletonObjects │ └── singletonFactories │ ├── Autowiring │ ├──
Type │ ├── Primary │ ├── Name │ ├── Qualifier │ └── Exceptions │ └──
Advanced Patterns ├── Strategy Pattern ├── ObjectProvider └── Scoped
Proxy

------------------------------------------------------------------------

End of Part 3 Notes
