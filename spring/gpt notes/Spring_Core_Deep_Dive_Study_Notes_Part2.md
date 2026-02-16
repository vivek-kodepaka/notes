# Spring Core Deep Dive -- Part 2 (Bean Scopes & Advanced Internals)

------------------------------------------------------------------------

# 1. Bean Scopes

## Default Scope: Singleton

-   Only one instance per ApplicationContext.
-   Stored inside singleton cache (Map).
-   Shared across multiple threads.
-   Suitable for stateless services (recommended in web/microservices
    apps).

### Why Singleton is Default:

-   Most services are stateless.
-   Avoid unnecessary object creation.
-   Better performance and memory efficiency.
-   Designed for multi-threaded web apps.

⚠ Important: Singleton beans must be stateless to avoid race conditions.

------------------------------------------------------------------------

## Thread Safety in Singleton

Example:

private int counter = 0; counter++

Not thread-safe because: - Read → Increment → Write (not atomic). -
Multiple threads can cause lost updates.

In microservices: - Each pod has its own singleton. - No cross-pod
consistency.

State should be stored in: - Database - Redis - Distributed cache -
External systems

------------------------------------------------------------------------

# 2. Request Scope

-   One bean per HTTP request.
-   Destroyed after request completes.

When injected into Singleton:

Spring injects a **Scoped Proxy**.

Singleton → Proxy(RequestBean) → Actual Request Instance

This solves lifecycle mismatch.

------------------------------------------------------------------------

# 3. Prototype Scope

-   New object created every time requested from container.
-   Not automatically recreated when injected into singleton.

Direct injection into singleton: - Prototype created once during
singleton creation. - After that, no new instance.

To get new instance each time: - ObjectProvider`<T>`{=html} -
Provider`<T>`{=html} - @Lookup - Scoped proxy

Correct approach:

@Autowired ObjectProvider`<PrototypeBean>`{=html} provider;

provider.getObject(); // new instance each time

------------------------------------------------------------------------

# 4. Internal Singleton Storage

Spring stores singleton beans inside:

DefaultSingletonBeanRegistry

Important structures:

-   singletonObjects → fully initialized beans
-   earlySingletonObjects → partially initialized beans
-   singletonFactories → used for circular dependency handling

------------------------------------------------------------------------

# 5. Circular Dependency Internals

Constructor Injection: - Fails at runtime during container startup. -
Needs fully resolved dependencies before object creation.

Setter Injection: - Object created first. - Early reference exposed. -
Dependencies injected later. - Works due to earlySingletonObjects.

But circular dependency indicates: - Tight coupling - SRP violation -
Poor separation of responsibilities - Hard maintenance

Best practice: Redesign.

------------------------------------------------------------------------

# 6. Scoped Proxy Concept

Used when injecting shorter-lived bean into longer-lived bean.

Example: Singleton → RequestScope

Spring injects proxy instead of real object.

Proxy resolves actual instance at runtime.

------------------------------------------------------------------------

# 7. Proxy Types in Spring

1.  JDK Dynamic Proxy
    -   Used when interface exists.
    -   Proxies interface methods only.
2.  CGLIB Proxy
    -   Used when no interface.
    -   Creates subclass at runtime.

Cannot proxy: - final methods - private methods - static methods

------------------------------------------------------------------------

# 8. Self Invocation Problem

External call: OtherBean → Proxy → RealBean (AOP works)

Internal call: this.method() → bypasses proxy (AOP fails)

Solution: Move method to another bean.

------------------------------------------------------------------------

# 9. Transaction Propagation

Default: REQUIRED - Join existing transaction. - Create new if none
exists.

REQUIRES_NEW: - Suspends existing transaction. - Starts new
transaction. - Works only when call goes through proxy.

Self invocation → REQUIRES_NEW does NOT work.

------------------------------------------------------------------------

# 10. AspectJ vs Proxy-Based AOP

Proxy-Based AOP: - Wraps object. - Self-invocation fails. - Lightweight
& common.

AspectJ: - Modifies bytecode directly. - Self-invocation works. - More
powerful but complex.

------------------------------------------------------------------------

# Key Architectural Insights

-   Spring uses proxy-based AOP by default.
-   Self-invocation bypasses proxy.
-   Circular dependency is runtime container issue.
-   BeanDefinition metadata created before instantiation.
-   Singleton beans must be stateless.
-   Prototype scope does not auto-refresh inside singleton.
-   Scoped proxy solves lifecycle mismatch.
-   Constructor injection prevents unsafe circular dependencies.

------------------------------------------------------------------------

End of Part 2 Notes
