## This module demonstrates a plain Spring application using
## IOC, DI, component scanning, and Java-based configuration.

-create plain maven project
-add spring-context (need for IOC and DI)
- added Engine interface and PetrolEngine class
- add Car class
- <img src="images/img.png" width="500" />
- @Component:
     - Spring creates beans only for annotated classes
     - Spring will register this class as a bean in the IOC container”
- AppConfig -> acts as spring configuration 
      - @Configuration -> Without @ComponentScan, Spring container starts but finds zero beans
      - @ComponentScan("com.example.core") -> tell spring to check below modules to check for components
- App.java -> main file
  - <img src="images/img_1.png" width="500" />
  -  // 1️⃣ Start Spring IOC Container
     var context =
     new AnnotationConfigApplicationContext(AppConfig.class);
     // 2️⃣ Ask Spring for a bean
     Car car = context.getBean(Car.class);
     // 3️⃣ Use it
     car.drive();
- new AnnotationConfigApplicationContext(AppConfig.class);
  - Spring:
     - Creates ApplicationContext
     - Scans package
     - Creates beans
     - Resolves dependencies
     - Manages lifecycle
     - This is IOC in action.
<img src="images/img_2.png" width="500" />


## -BeanFactory vs applicationContext

BeanFactory: BeanFactory is a basic container mainly used to demonstrate lazy loading.
<img src="images/img_6.png" width="500" />
<img src="images/img_7.png" width="500" />

      * XmlBeanFactory loads beans.xml
      * Bean is NOT created immediately
      * Bean is created only when getBean() is called
      * This is lazy initialization

**Important Interview Notes**

* XmlBeanFactory is deprecated (Spring 3.1+)*
* Still asked for conceptual understanding 
* Replaced by ApplicationContext

### ApplicationContext:
- ApplicationContext is an advanced Spring container built on top of BeanFactory.
Key Characteristics

  * Uses eager initialization (creates beans at startup)
  * Supports enterprise features
  * Most commonly used container
  * Used by Spring Boot internally

ApplicationContext = eager initialization by default
It does not matter whether beans are defined via:
     * @Component
      * @Bean in @Configuration
      * XML

## **Can we have Lazy Initialization in ApplicationContext?**

<img src="images/img_8.png" width="500" />
What happens?
* Spring context starts
* engine bean is NOT created
* Bean is created only when first requested

<img src="images/img_9.png" width="500" />
<img src="images/img_10.png" width="500" />
Meaning
* Car is created at startup
* Engine is created only when first used
* 📌 This uses a proxy internally.

<img src="images/img_11.png" width="500" />


## Spring Bean Lifecycle:
1. Instantiation
2. Dependency Injection
3. Initialization (@PostConstruct)
4. Ready for use
5. Destruction (@PreDestroy)

@PostConstruct runs after dependencies are injected.
@PreDestroy runs before bean destruction.
Prototype beans are not fully managed during destruction.

### Step 1: Bean Instantiation
- Spring creates the object

Uses:
- Constructor
- Factory method
- Reflection

📌 At this point:
- Object exists
- Dependencies NOT injected yet


### Step 2: Dependency Injection

- Spring injects dependencies
- Constructor / Setter / Field

📌 After this:
* Bean is in a valid state
* Dependencies are available


### Step 3: Initialization (IMPORTANT)
- This is where custom initialization logic runs.

There are 3 ways:
option 1: use this approach
    `@Component
    public class Engine {
    
        @PostConstruct
        public void init() {
            System.out.println("Engine initialized");
        }
    }`

#### Key points:
* Runs after dependency injection
* Runs once per bean
* Preferred over XML methods
* @PostConstruct runs after dependencies are injected.

#### option 2:

    `@Component
    public class Engine implements InitializingBean {
    
        @Override
        public void afterPropertiesSet() {
            System.out.println("Engine initialized");
        }
    }`

❌ Tightly couples code to Spring
⚠️ Avoid in business logic

#### option 3:
@Bean(initMethod=...) (CONFIG LEVEL)

    `@Bean(initMethod = "init")
    public Engine engine() {
    return new Engine();
    }`

- Useful for third-party classes

Bean Is Ready for Use ✅
At this stage:
* Bean is fully initialized
* Available via getBean()
* Used by application


## Bean Destruction Phase (VERY IMPORTANT)

Occurs when:
* Application shuts down
* Context is closed

#### @PreDestroy (MOST USED)

        `@Component
        public class Engine {
        
            @PreDestroy
            public void cleanup() {
                System.out.println("Engine destroyed");
            }
        }`

#### Used for:
* Closing DB connections
* Releasing resources
* Flushing buffers

📌 Interview line:
“@PreDestroy is used for cleanup before bean removal.”



DisposableBean Interface (LESS USED)
`@Component
public class Engine implements DisposableBean {

    @Override
    public void destroy() {
        System.out.println("Cleanup");
    }
}`


❌ Spring-coupled
⚠️ Avoid when possible


# DAY 3 – SPRING BEAN SCOPES (VERY IMPORTANT)
<img src="images/img_2.png" width="500" />

What Is a Bean Scope?
    Bean scope defines how many instances of a bean Spring creates and how long those instances live.

* How many objects?
* When created?
* When destroyed?



### Core scopes (most important):
* singleton -> default -> One bean instance per Spring container
* prototype -> A new bean instance is created every time it is requested
  * `@Component
    @Scope("prototype")
    class Engine {
    }`
  * does not handles @PreDestroy

### Web scopes (later / optional):
* request
* session
* application --> one bean instance per servletContext (servlet conatiner)

#### Is singleton = one instance per JVM?
    ❌ No
    ✅ One instance per Spring container

#### ❓ When to use prototype?
    ✅ Short-lived objects
    ✅ Non-shared state
    ❌ Heavy resources

Bean Scope defines the lifecycle and number of bean instances.
Singleton is the default scope with one instance per container.
Prototype creates a new instance per request, and Spring does not
manage its destruction phase.


## web scope
Web scopes are available only in Spring web applications (mvc or boot).

#### * Request scope creates a bean per HTTP request.

  * @Scope(value = WebApplicationContext.SCOPE_REQUEST)
  * New bean for every HTTP request
  * Destroyed after request completes
    * uses:
      *  Request-specific data
      *  Request logging
      *  Correlation IDs
      
#### * Session scope creates a bean per user session.

  * @Scope(value = WebApplicationContext.SCOPE_SESSION)
  * Same bean reused across multiple requests
  * New bean created when new session starts
  * Destroyed when session expires
    * uses
      * User login data
      * Shopping cart
      * Shopping cart
    
#### * Application scope creates a single bean for the entire web app.    
  * @Scope(value = WebApplicationContext.SCOPE_APPLICATION)
  * Single instance shared by all users
  * Lives until application shutdown
    * Use Cases
      * Global caches
      * Shared configuration
      * Application-wide counters

#### Scoped proxies are required when injecting web-scoped beans into singleton beans.
`@Component
public class OrderService {

    @Autowired
    private RequestBean requestBean; // ❌ problem
}`

above code doesnt work requestBean is request scope cant be used in singleton
* Singleton created at startup
* RequestBean exists only during request

    `@Component
    @Scope(
    value = WebApplicationContext.SCOPE_REQUEST,
    proxyMode = ScopedProxyMode.TARGET_CLASS
    )
    public class RequestBean {
    }`
* Spring injects a proxy, not the real bean.


# DAY 4 – MULTIPLE BEANS INJECTION

The Problem: Multiple Beans of Same Type
You have one interface and multiple implementations.

    `public interface Engine {
    void start();
    }`

    `@Component
    public class PetrolEngine implements Engine {
    public void start() {
    System.out.println("Petrol engine");
    }
    }`

    `@Component
    public class DieselEngine implements Engine {
    public void start() {
    System.out.println("Diesel engine");
    }
    }`

    `@Component
    public class Car {
    
        private final Engine engine;
    
        public Car(Engine engine) {
            this.engine = engine;
        }
    }`

NoUniqueBeanDefinitionException:
expected single matching bean but found 2

solution:
- @Primary - defines the default bean among multiple candidates.
- @Qualifier 
  * You want different implementations in different places
  * Explicit control is required
  * @Qualifier("dieselEngine")
  * if both presents Qualifier takes precedence

    `@Component
    public class Car {
    @Autowired
    private Engine petrolEngine; // byName
    }`
  * Exception 
    * usually byType it matches 
    * But in field injection -> byname also it matches but not recommended


# DAY 5 – (@Configuration vs @Component) and (@Bean vs @Component)

## @Configuration vs @Component

1️⃣ First: What Problem Are These Solving?

Spring needs to know:
  * which classes are beans
  * How beans are created
  * Whether Spring should manage method calls between beans

There are two ways to register beans:
   1. Component scanning
   2. Java configuration (@Bean)

@Component     -->  marks a class as a Spring-managed bean.
@Configuration -->marks a class that defines bean creation methods.

@Configuration uses CGLIB proxying
This ensures:
Only ONE bean instance is created
Method calls between @Bean methods are intercepted


    `@Component
    public class AppConfig {
        @Bean
        public Engine engine() {
            return new Engine();
        }
        @Bean
        public Car car() {
            return new Car(engine());
        }
    }`

 1. creating beans inside @component using @Bean
 2. 2 engine objs are created  --> No interception btw method calls

    `@Component
    public class AppConfig {
    @Bean
    public Engine engine() {
    return new Engine();
    }
    @Bean
    public Car car() {
    return new Car(engine());
    }
    }`

- only one engine obj is created 
- @configuration -> “@Configuration ensures singleton behavior for 
                      @Bean methods using proxying, whereas @Component does not.”

# @Bean vs @Component

 - @Component  -> you own class
 - @Bean       -> You do NOT own the class
                  Third-party libraries
                  Need custom construction logic

    - Need custom construction logic
      - `@Component
        public class DataSource {
        public DataSource(String url, String user, String pwd) { }
        }`
      - fails UnsatisfiedDependencyException: No qualifying bean of type 'java.lang.String' available
      - `@Component
         public class DataSource {
         public DataSource(
         @Value("${db.url}") String url,
         @Value("${db.user}") String user,
         @Value("${db.pwd}") String pwd
         ) {
         }
         }`
      - works but Still less flexible,   ⚠️ Harder to test, ⚠️ Tight coupling to configuration
      - BEST WAY
      - `@Configuration
         public class DbConfig {
         @Bean
         public DataSource dataSource() {
         return new DataSource(
         "jdbc:mysql://localhost:3306/app",
         "user",
         "pwd"
         );
         }
         }`




- @Component is used for business classes discovered via component scanning.
  @Configuration is used for defining @Bean methods and ensures singleton
  behavior through proxying.
  @Bean is preferred for third-party or externally constructed objects.
  Using @Bean inside @Component may lead to multiple instances.

# DAY 6 – SPRING PROFILES (@Profile)

Dev / Test / Prod configuration

1. jvm arg -> -Dspring.profiles.active=dev
2. application.properties -> spring.profiles.active=dev
3.plain spring -> context.getEnvironment().setActiveProfiles("dev")
4. @Configuration
   @Profile("dev") --> all beans inside configuration
5. @Profile("dev, qa")
   @Component     --> only this component for both dev and qa

- Spring Profiles allow beans and configurations to be loaded conditionally
  based on the active environment. Only beans matching the active profile
  are created during context startup.


# DAY 7 – CONDITIONAL BEANS

- present in spring boot auto configure -> @Conditional, @ConditionalOnProperty, @ConditionalOnClass
- spring core -> only @Conditional

## What Are Conditional Beans?
- Conditional beans are beans that are created only if a specific condition is satisfied.
- Spring checks conditions:
  - at startup
  - before bean creation
If condition fails → bean is not created.

## 2️⃣ Why Conditional Beans Are Needed (Real World)
- Without conditional beans:
  - Kafka config loads even if Kafka is not used ❌
  - DB config loads even if DB is disabled ❌
  - Auto-config becomes impossible ❌
  
- With conditional beans:
  - Lightweight startup
  - Feature toggles
  - Environment-based behavior

📌 Spring Boot auto-configuration is built on conditional beans.

1. @ConditionalOnProperty
    - `@Component
      @ConditionalOnProperty(
      name = "feature.cache.enabled",
      havingValue = "true",
      matchIfMissing = false
      )
      public class CacheService {
      }`

2. @ConditionalOnClass
   - `@Configuration
     @ConditionalOnClass(name = "org.apache.kafka.KafkaProducer")
     public class KafkaConfig {
       @Bean
       public KafkaService kafkaService() {
       return new KafkaService();
       }
       }`
   - ✔ Kafka jar present → bean created
     ❌ Kafka jar missing → bean skipped
📌 This is how Spring Boot avoids ClassNotFoundException.

3. @ConditionalOnMissingBean
    - `@Bean
      @ConditionalOnMissingBean(DataSource.class)
      public DataSource defaultDataSource() {
      return new DefaultDataSource();
      }`
    - ✔ User defines own DataSource → this bean skipped
    - ✔ User does nothing → default bean used
    - 📌 This is core to Spring Boot starters.

4. @Conditional (LOW-LEVEL)
    - `public class LinuxCondition implements Condition {
       @Override
       public boolean matches(
       ConditionContext context,
       AnnotatedTypeMetadata metadata
       ) {
       String os = System.getProperty("os.name");
       return os.contains("Linux");
       }
       }`
    - `@Bean
      @Conditional(LinuxCondition.class)
      public OsService linuxService() {
      return new LinuxService();
      }`


## Bean Definition vs Bean Instance (Small but Useful)
Add a short note:
- A bean definition is metadata about how a bean should be created.
- A bean instance is the actual object created from that definition.

## Bean Definition vs Bean Instance (Small but Useful)
Add a short note:
- A bean definition is metadata about how a bean should be created.
- A bean instance is the actual object created from that definition.




  

