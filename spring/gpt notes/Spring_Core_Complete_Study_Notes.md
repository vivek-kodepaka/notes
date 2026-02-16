# Spring Core Complete Notes (Full Session Summary)

------------------------------------------------------------------------

# 1. Why Spring (IoC Concept)

Traditional Java: - Classes create dependencies using `new` - Tight
coupling - Violates Open/Closed Principle - Hard to swap implementations

Spring Solution: - Inversion of Control (IoC) - Container creates and
injects objects - Loose coupling - Centralized lifecycle management

Flow: Container → creates bean → injects into dependent class

------------------------------------------------------------------------

# 2. IoC Container

Two core interfaces: - BeanFactory (lazy by default) -
ApplicationContext (eager singleton initialization)

Important: Spring first creates **BeanDefinition (metadata)**, not
objects.

BeanDefinition contains: - Class type - Scope - Constructor details -
Init & destroy methods - Lazy flag - Qualifiers

------------------------------------------------------------------------

# 3. Bean Lifecycle (Correct Order)

1.  BeanDefinition registration
2.  Bean instantiation (constructor)
3.  Dependency injection
4.  Aware callbacks (BeanNameAware, ApplicationContextAware, etc.)
5.  BeanPostProcessor (before initialization)
6.  Initialization phase:
    -   @PostConstruct
    -   afterPropertiesSet()
    -   custom init method
7.  BeanPostProcessor (after initialization) → Proxy wrapping happens
    here
8.  Bean ready for use
9.  Destroy phase (@PreDestroy)

------------------------------------------------------------------------

# 4. Bean Scopes

Default: Singleton

-   One instance per container
-   Stored in singleton cache (Map)
-   Shared across threads
-   Must be stateless

Prototype: - New instance each time requested from container - If
injected into singleton → created once at injection time

Request Scope: - One instance per HTTP request - Uses scoped proxy when
injected into singleton

------------------------------------------------------------------------

# 5. Thread Safety in Singleton

Singleton beans are shared across threads.

Mutable state example:

private int counter; counter++

Risk: - Lost updates - Race conditions

Never store business state in service beans. Use DB / Redis /
distributed systems instead.

------------------------------------------------------------------------

# 6. Circular Dependency

Constructor Injection: - Fails at startup - Safe (no partially
constructed bean exposed)

Setter Injection: - Allows early exposure - Uses earlySingletonObjects -
Risky when proxies are involved

Circular dependency indicates: - Tight coupling - SRP violation - Poor
architecture

------------------------------------------------------------------------

# 7. Internal Singleton Caches

DefaultSingletonBeanRegistry maintains:

-   singletonObjects (fully initialized beans)
-   earlySingletonObjects (partially initialized beans)
-   singletonFactories (ObjectFactory for early proxy exposure)

------------------------------------------------------------------------

# 8. BeanPostProcessor

Runs for every bean.

Two methods: - postProcessBeforeInitialization -
postProcessAfterInitialization

Whatever object is returned is stored in container.

Used internally for: - @Transactional - @Async - @Cacheable - AOP
proxies

------------------------------------------------------------------------

# 9. Proxy Mechanism (Core Spring Pattern)

Spring frequently uses wrapping:

Caller → Proxy → Target

Used in: - @Transactional - @Lazy - @RequestScope - @Configuration - AOP

Proxy types: 1. JDK Dynamic Proxy (interface-based) 2. CGLIB
(subclass-based)

Cannot proxy: - final methods - private methods - static methods

------------------------------------------------------------------------

# 10. Self Invocation Problem

Internal method call:

this.method()

Bypasses proxy.

Result: - @Transactional may not work - REQUIRES_NEW ignored

Solution: Move method to another bean.

------------------------------------------------------------------------

# 11. Transaction Propagation

Default: REQUIRED - Join existing transaction

REQUIRES_NEW: - Suspend current - Start new - Only works if call goes
through proxy

------------------------------------------------------------------------

# 12. Autowiring Resolution Order

When @Autowired is used:

1.  Match by type
2.  If multiple → @Primary
3.  If multiple → match by field/parameter name
4.  If multiple → @Qualifier
5.  If still ambiguous → NoUniqueBeanDefinitionException
6.  If none found → NoSuchBeanDefinitionException

------------------------------------------------------------------------

# 13. Optional Dependencies

Options:

1.  @Autowired(required=false) → injects null
2.  Optional`<T>`{=html} → safer
3.  ObjectProvider`<T>`{=html} → lazy + prototype support

ObjectProvider supports: - getObject() - getIfAvailable() - stream()

------------------------------------------------------------------------

# 14. Collection & Map Injection

List`<T>`{=html}: - Injects all beans of type T - Order controlled by
@Order

Map\<String, T\>: - Key = bean name - Value = bean instance

Better strategy pattern: - Inject List`<T>`{=html} - Convert to Map
using business key (getType()) - Avoid relying on bean names

Duplicate keys in toMap() → IllegalStateException (fail fast)

------------------------------------------------------------------------

# 15. @Configuration vs @Component

@Configuration: - Meta-annotated with @Component - Enhanced using CGLIB
subclass - @Bean methods intercepted - Ensures singleton when one @Bean
calls another

@Component: - No subclass enhancement - Direct Java method calls -
Calling @Bean-like methods creates new object

------------------------------------------------------------------------

# 16. proxyBeanMethods Modes

Full mode (default): @Configuration(proxyBeanMethods = true) - CGLIB
enhancement enabled - Safe when @Bean methods call each other

Lite mode: @Configuration(proxyBeanMethods = false) - No enhancement -
Faster startup - Safe when using parameter injection only

------------------------------------------------------------------------

# 17. @Lazy

Two usages:

1.  On bean:
    -   Delays bean instantiation globally
2.  On injection point:
    -   Injects lazy proxy
    -   Bean created when first method invoked

If another bean injects eagerly: - Bean still created at startup

------------------------------------------------------------------------

# 18. Key Architectural Insights

-   Spring relies heavily on proxy + delegation pattern
-   Constructor injection is safer than setter injection
-   Singleton beans must be stateless
-   Circular dependency + AOP is dangerous
-   BeanPostProcessor is core extension point
-   Lite mode configuration improves performance
-   ObjectProvider is powerful for advanced cases

------------------------------------------------------------------------

End of Complete Spring Core Notes
