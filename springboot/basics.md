<details>
<summary>why spring boot </summary>

```text
Before Boot, in pure Spring:
    You had to configure DispatcherServlet
    Configure ComponentScan
    Configure DataSource
    Configure TransactionManager
    Configure ViewResolver
    Write XML or heavy Java config
    Deploy WAR to external Tomcat
```
```text
Spring Boot = Spring + opinionated defaults + automation around configuration
```
```text
A layer on top of Spring that eliminates boilerplate configuration using:
   1. Auto-configuration
   2. Conditional bean registration
   3. Classpath scanning
   4. Embedded container support
```
</details>

<details>
<summary>auto configuration</summary>

```text
Question 1:
When you add:
spring-boot-starter-data-jpa dependecny

How does Spring Boot know to create:
    EntityManagerFactory
    DataSource
    TransactionManager
```

```text
from spring-boot-autoconfigure
it contains all 
```
<img src="images/img.png" width="500">
</details>

