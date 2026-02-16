@Component and @Configuration

@Configuration is itself meta-annotated with @Component
@Configuration IS a @Component

both registers as beans

```java
@Configuration
class AppConfig {

    @Bean
    public PaymentService paymentService() {
        return new PaymentService();
    }

    @Bean
    public OrderService orderService() {
        return new OrderService(paymentService());
    }
}

```

When orderService() calls paymentService() its a Intercepted call


@Configuration enhancement:
Subclassing
Overridden methods
Self-invocation goes through overridden method
That’s why it works here.
```scss
AppConfig$$Enhancer
orderService() → calls overridden paymentService()
```
```java
class AppConfig$$EnhancerBySpring extends AppConfig

```
```text
@Configuration classes are enhanced using CGLIB subclassing.
Spring creates a subclass of the configuration class.
All @Bean methods are overridden in that subclass.
So when one @Bean method calls another, the call is intercepted.
Spring checks the container and returns the existing singleton instead of creating a new object.
```


@Configuration(proxyBeanMethods = false)
Then:
❌ No CGLIB subclass enhancement
❌ @Bean methods are NOT intercepted
❌ Method calls are plain Java calls
❌ Calling one @Bean method from another creates a new object

This is called:
👉 Lite mode configuration

proxyBeanMethods = false:
Disables CGLIB enhancement
Improves startup performance
Safe when @Bean methods don't call each other
Used heavily in Spring Boot auto-configuration


