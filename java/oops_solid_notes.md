
# OOP and SOLID Principles – Complete Notes

## Part 1: Object-Oriented Programming (OOP)

### What is OOP?
Object-Oriented Programming (OOP) is a programming paradigm based on objects that contain:
- Data (fields)
- Behavior (methods)

Languages: Java, C++, C#, Python

---

## Four Pillars of OOP

1. Encapsulation
2. Inheritance
3. Polymorphism
4. Abstraction

---

### 1. Encapsulation

Wrapping data and methods together and restricting direct access to data.

Example:
```java
class Employee {
    private int salary;

    public int getSalary() {
        return salary;
    }

    public void setSalary(int salary) {
        this.salary = salary;
    }
}
```

Benefits:
- Data hiding
- Controlled access
- Security

---

### 2. Inheritance

One class acquires properties of another.

Example:
```java
class Animal {
    void eat() {
        System.out.println("Eating...");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("Barking...");
    }
}
```

Benefits:
- Code reuse
- Hierarchical structure

---

### 3. Polymorphism

One interface, many implementations.

#### Method Overloading
```java
int add(int a, int b) {}
int add(int a, int b, int c) {}
```

#### Method Overriding
```java
class Animal {
    void sound() {}
}

class Dog extends Animal {
    void sound() {}
}
```

---

### 4. Abstraction

Hiding implementation details and showing only functionality.

Example:
```java
interface Payment {
    void pay();
}
```

Benefits:
- Reduced complexity
- Better design

---

## Composition vs Inheritance

Composition:
Car has Engine

Preferred when flexibility is required.

---

# Part 2: SOLID Principles

SOLID stands for:

S – Single Responsibility Principle  
O – Open/Closed Principle  
L – Liskov Substitution Principle  
I – Interface Segregation Principle  
D – Dependency Inversion Principle  

---

## 1. Single Responsibility Principle (SRP)

A class should have only one reason to change.

Bad Example:
One class handling business logic, DB, and email.

Good Example:
Separate classes:
- Service
- Repository
- EmailSender

---

## 2. Open/Closed Principle (OCP)

Software should be:
- Open for extension
- Closed for modification

Example:
```java
interface Payment {
    void pay();
}
```

Add new payment methods without modifying existing code.

---

## 3. Liskov Substitution Principle (LSP)

Subclasses should be replaceable for their parent classes without breaking behavior.

Example violation:
Square inheriting Rectangle and breaking expected behavior.

Rule:
Child classes must honor parent behavior.

---

## 4. Interface Segregation Principle (ISP)

Do not force classes to implement methods they do not use.

Bad:
```java
interface Worker {
    void work();
    void eat();
}
```

Good:
```java
interface Workable { void work(); }
interface Eatable { void eat(); }
```

---

## 5. Dependency Inversion Principle (DIP)

Depend on abstractions, not concrete classes.

Example:
```java
interface PaymentService {}
class OrderService {
    private PaymentService paymentService;
}
```

Used heavily in Spring Dependency Injection.

---

# Real Spring Boot Mapping

Controller → Service → Repository (SRP)  
Interfaces and Implementations (OCP, DIP)  
Small focused interfaces (ISP)  
Proper inheritance design (LSP)

---

# Interview Questions

1. What are the four pillars of OOP?
2. Difference between abstraction and encapsulation?
3. Explain SOLID principles.
4. Which SOLID principle does Spring use most?
5. Give a real-world example of LSP.

---

# Golden Interview Answer

OOP is based on encapsulation, inheritance, polymorphism, and abstraction.  
SOLID principles improve maintainability, scalability, and flexibility by enforcing good object-oriented design practices.
