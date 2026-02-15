
# Java Inner Classes & Class Loading – Interview Notes (7+ Years)

## 1. What is an Inner Class?
An inner class is a class declared inside another class.
At JVM level, every inner class is a separate `.class` file.

```
Outer.class
Outer$Inner.class
```

Inner classes are NOT loaded automatically when the outer class is loaded.

---

## 2. Types of Inner Classes
1. Static Inner Class  
2. Non-static (Member) Inner Class  
3. Local Inner Class  
4. Anonymous Inner Class  

---

## 3. Static Inner Class

```java
class Outer {
    static class Inner {
        void work() {}
    }
}
```

### Key Points
- No outer class instance required
- Can access only static members of outer class
- No implicit outer reference

```java
Outer.Inner i = new Outer.Inner();
Class.forName("Outer$Inner");
```

---

## 4. Non-static (Member) Inner Class

```java
class Outer {
    int x = 10;
    class Inner {
        void print() {
            System.out.println(x);
        }
    }
}
```

```java
Outer o = new Outer();
Outer.Inner i = o.new Inner();
Class.forName("Outer$Inner");
```

⚠️ Holds implicit reference to outer object → possible memory leaks.

---

## 5. Local Inner Class

```java
class Outer {
    void process() {
        int x = 10;
        class LocalInner {
            void run() {
                System.out.println(x);
            }
        }
        new LocalInner().run();
    }
}
```

- Scope limited to method
- Access only final / effectively-final variables

---

## 6. Anonymous Inner Class

```java
Runnable r = new Runnable() {
    public void run() {
        System.out.println("run");
    }
};
```

Java 8+ replacement:
```java
Runnable r = () -> System.out.println("run");
```

---

## 7. Inner Class Naming (Bytecode)

```java
class Outer {
    static class A {}
    class B {}
}
```

Compiled as:
```
Outer.class
Outer$A.class
Outer$B.class
```

---

## 8. Class Loading of Inner Classes

![Inner Class Loading](images/inner_class_loading.png)

```java
Class.forName("Outer");       // loads Outer only
Class.forName("Outer$Inner"); // loads Inner explicitly
```

`Outer.Inner` is NOT a valid name for class loading.

---

## 9. Static Block Proof

```java
class Outer {
    static { System.out.println("Outer loaded"); }
    static class Inner {
        static { System.out.println("Inner loaded"); }
    }
}
```

---

## 10. Why JVM Does Not Auto-load Inner Classes?
- Lazy loading
- Performance optimization
- Memory efficiency

---

## 11. Inner Classes and Spring (Interview Bonus)

- Static inner classes CAN be Spring beans
- Non-static inner classes CANNOT

```java
@Component
public static class Helper {}
```

---

## 12. Interview One-liners
- Inner classes are independent runtime classes
- Use `$` to load inner classes via reflection
- Prefer static inner classes
- Anonymous inner classes are legacy after Java 8

---

## 13. Final Takeaway
Inner classes are compile-time nested but runtime-independent.
Class loading is always explicit and lazy.
