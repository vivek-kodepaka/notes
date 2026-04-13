# Java Scenario-Based Interview Notes (Complete)

## 1. Strings & SCP

- SCP (String Constant Pool) stores literals
- Heap stores objects created using new

```java
String s1 = "Java";        // SCP
String s2 = new String("Java"); // Heap
```

### Comparison
- `==` → reference
- `equals()` → value

### intern()
```java
s2 = s2.intern(); // moves to SCP
```

---

## 2. String Immutability

```java
String s1 = "Java";
s1 = s1.concat(" Rocks");
```

- Creates new object

---

## 3. Integer Caching

- Range: **-128 to 127**

```java
Integer a = 100;
Integer b = 100; // same object

Integer x = 200;
Integer y = 200; // different objects
```

---

## 4. equals() vs ==

| Operator | Meaning |
|----------|--------|
| ==       | reference |
| equals() | value |

---

## 5. Null Handling

- `null == obj` → false
- `null.equals()` → NPE
- `obj.equals(null)` → false

---

## 6. Method Overloading Rules

### Most specific wins

```java
test(Object)
test(String)

test(null) → String
```

### Ambiguity

```java
test(String)
test(Integer)

test(null) → compile error
```

---

## 7. Priority Order

> Exact > Widening > Autoboxing > Varargs

---

## 8. Overriding vs Hiding

- Non-static → runtime (overriding)
- Static → compile-time (hiding)

---

## 9. Constructors

- Parent executes first
- `this()` → same class
- `super()` → parent class
- Cannot use both

---

## 10. Exception Handling

- finally always executes
- finally overrides return if present

```java
try {
    return 1;
} finally {
    return 2;
}
```

---

## 11. Catch Order

- Specific → General

Wrong:
```java
catch(Exception)
catch(ArithmeticException)
```

---

## Final Cheat Sheet

- String → immutable
- SCP vs Heap
- Integer cache → -128 to 127
- == vs equals
- Priority → Exact > Widening > Autoboxing > Varargs
- Overriding → runtime
- Static → compile-time
- finally → always executes
