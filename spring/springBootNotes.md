# Why Spring Boot Came into Picture? (VERY IMPORTANT)

## Problems with traditional Spring 😵

* Too much XML / Java config
* Manual dependency management
* External Tomcat setup
* Boilerplate code
* Steep learning curve

## Spring Boot Solution ✅

* Spring Boot = Spring + Opinionated Defaults + Auto Configuration

## 2️⃣ What EXACTLY is Spring Boot?

📌 Interview definition:

* Spring Boot is an extension of Spring that simplifies application development 
   by providing auto-configuration, starter dependencies, and embedded servers.

## 3️⃣ Core Pillars of Spring Boot (Must Remember)

1. Auto Configuration
    * Spring Boot:
    * Looks at classpath
    * Looks at application.properties
    * Automatically configures beans

* What does Classpath mean?
    - Simple definition:
      - Classpath = all the classes and libraries available to your application at runtime
    - It includes:
      - Your compiled classes (target/classes)
      - JARs added via Maven/Gradle dependencies
      - JDK classes
📌 If a class is present in classpath, Java (and Spring Boot) can see and use it.

* Real-life analogy 🧠
  * Think of classpath like a toolbox 🧰
  * If a tool is inside the toolbox, you can use it
  * If it’s not inside, you can’t
  * Spring Boot opens the toolbox and asks:

“Which tools (libraries) are present?”
  * Example: spring-boot-starter-web

When you add:
`<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>`

- This adds to classpath:
    * DispatcherServlet
    * @RestController
    * Tomcat
    * Jackson (ObjectMapper)

- Now Spring Boot detects:
    * “Ah! Web classes are present”
    * It auto-configures:
      * Embedded Tomcat
      * DispatcherServlet
      * JSON converter
      
- How Spring Boot checks the classpath?
  - Internally, it uses:
  - @ConditionalOnClass(SomeClass.class)
Example:

`@ConditionalOnClass(DispatcherServlet.class)
public class WebMvcAutoConfiguration {
}`
📌 Meaning:
- “Configure Spring MVC ONLY IF DispatcherServlet is present in classpath”

- Concrete Example (Most Asked)
  - Case 1️⃣: Web dependency present
  - spring-boot-starter-web

Classpath contains:
✔ DispatcherServlet
✔ Tomcat

Result:
✔ Web app
✔ REST APIs enabled

Case 2️⃣: Web dependency NOT present
spring-boot-starter

Classpath contains:
❌ DispatcherServlet

Result:
❌ No Tomcat
❌ No controllers
✔ Simple Java application

- Another Example: Database Auto Configuration
If this class is present:
javax.sql.DataSource
Spring Boot says:
- “Database-related classes exist”
Then it checks:
- spring.datasource.url
- spring.datasource.username

➡️ Auto-creates DataSource bean

❌ If DB driver not in classpath → no DataSource

# Why this is powerful 🚀

Without Spring Boot ❌:
- You manually configure everything
With Spring Boot ✅:
- Dependencies decide behavior
- Minimal config
- Less code
- Fewer bugs

## Interview-ready one-liner 🎯
 * Spring Boot inspects the classpath to detect available libraries and automatically configures beans using conditional annotations like @ConditionalOnClass.


2. Starter Dependencies

- Instead of 10 dependencies ❌
- You add 1 starter ✅

`<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>`

Includes:
* Spring MVC
* Jackson
* Validation
* Embedded Tomcat
* Logging

- Convention over Configuration

### **4. Embedded Server**

- No external Tomcat needed ❌
java -jar app.jar
✔ Tomcat (default)
✔ Jetty / Undertow (optional)

## Entry Point – @SpringBootApplication

`@SpringBootApplication
public class MyApp {
public static void main(String[] args) {
SpringApplication.run(MyApp.class, args);
}
}`

#### What it actually contains 👇

- @SpringBootConfiguration
- @EnableAutoConfiguration
- @ComponentScan


### Profiles (VERY COMMON)
- spring.profiles.active=dev

Files:
- application-dev.properties
- application-prod.properties

Use case:
* DB config
* Logging
* Feature toggles

8️⃣ Dependency Injection – Same as Spring
Nothing new here ✔
Your Spring knowledge applies 100%
