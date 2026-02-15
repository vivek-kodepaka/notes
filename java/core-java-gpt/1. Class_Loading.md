# Java Class Loading – Deep Dive (Senior Level | 7+ Years Experience)

---

## 1. What is Class Loading?
Class Loading is the JVM process of **loading, linking, and initializing Java classes** so they can be executed.

> Before any Java code runs, its class **must be loaded into JVM memory**.

Key points:
- Happens **at runtime**
- Is **lazy by default**
- Managed by **ClassLoaders**

---

## 2. How is `.class` File Generated?

### Source to Bytecode Flow
```text
MyClass.java
   ↓ javac
MyClass.class
   ↓ ClassLoader
JVM Memory
```

### Compilation
```bash
javac MyClass.java
```

The Java compiler:
- Converts source code into **bytecode**
- Bytecode is **platform-independent**
- Stored in `.class` files

### What `.class` File Contains
- Constant Pool
- Method bytecode
- Field metadata
- Access flags
- Debug info (line numbers)

📌 `.class` is NOT machine code.  
JVM interprets or JIT-compiles it at runtime.

---

## 3. When Does Class Loading Happen?

A class is loaded when it is **actively used**:

- Object creation
```java
new OrderService();
```

- Static variable access
```java
OrderService.VERSION;
```

- Static method call
```java
OrderService.init();
```

- Reflection
```java
Class.forName("com.app.OrderService");
```

- Spring bean initialization

🚫 Not loaded if only imported or referenced as a type.

---

## 4. ClassLoader Architecture

Java uses the **Parent Delegation Model**.

### Built-in ClassLoaders

| ClassLoader | Loads |
|------------|------|
| Bootstrap | Core Java classes (`java.lang.*`) |
| Extension | JDK extension libraries |
| Application | Application classpath |

### Delegation Flow
```text
Application
   ↓
Extension
   ↓
Bootstrap
```

📌 Prevents overriding core Java classes.

---

## 5. Phases of Class Loading (CRITICAL)

### 1️⃣ Loading
- Reads `.class` file
- Creates `Class` object
- Stores metadata in **Metaspace**

---

### 2️⃣ Linking

#### a) Verification
- Bytecode validity
- Stack & type safety
- Prevents malicious code

#### b) Preparation
- Allocates memory for `static` variables
- Assigns default values

```java
static int count; // 0
```

#### c) Resolution
- Symbolic references → direct memory references

---

### 3️⃣ Initialization
- Executes `static` blocks
- Assigns actual static values

```java
static int count = 10;

static {
    System.out.println("Initialized");
}
```

📌 Runs **once per class per ClassLoader**.

---

## 6. Order of Execution

```java
class Demo {
    static {
        System.out.println("Static");
    }

    {
        System.out.println("Instance");
    }

    Demo() {
        System.out.println("Constructor");
    }
}
```

```java
new Demo();
```

Output:
```text
Static
Instance
Constructor
```

---

## 7. `Class.forName()` Explained

```java
Class.forName("com.mysql.cj.jdbc.Driver");
```

It:
1. Loads the class
2. Links it
3. Initializes it immediately

Used in:
- JDBC
- SPI
- Reflection frameworks

Example:
```java
static {
    DriverManager.registerDriver(new Driver());
}
```

---

## 8. JVM Memory Areas Involved

| Area | Stores |
|----|------|
| Metaspace | Class metadata, static variables |
| Heap | Objects |
| Stack | Method calls, local variables |

📌 Metaspace uses native memory (Java 8+).

---

## 9. Class Loading in Spring Boot

- Uses ApplicationClassLoader
- Lazy bean loading
- Heavy reflection & proxies
- Dynamic bytecode generation

Common issues:
- ClassNotFoundException
- NoClassDefFoundError
- Dependency conflicts

---

## 10. ClassNotFoundException vs NoClassDefFoundError

| Aspect | ClassNotFoundException | NoClassDefFoundError |
|------|------------------------|---------------------|
| Type | Checked | Error |
| Cause | Missing at runtime | Missing after compilation |
| Common Reason | Wrong classpath | Dependency conflict |

---

## 11. Custom ClassLoader (Advanced)

Used in:
- Plugin systems
- App servers
- OSGi
- SPI

```java
class CustomLoader extends ClassLoader {
    protected Class<?> findClass(String name) {
        // load bytes & defineClass
    }
}
```

📌 Same class + different ClassLoader = different class.

---

## 12. Senior-Level Interview One-Liners

- JVM loads classes lazily
- Static blocks run during initialization
- Bytecode is platform-independent
- Metaspace stores class metadata
- Class identity = ClassName + ClassLoader

---

## 13. Why Class Loading Matters in Production

- Memory leaks (Metaspace)
- Faster Spring startup
- Debugging prod issues
- Microservice isolation
- Plugin architectures

---

## 14. TL;DR Cheat Flow

```text
.java → javac → .class → ClassLoader
      → Load → Link → Initialize → Execute
```

---
