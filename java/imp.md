
<details>
<summary>How lamdas work internally</summary>

```text
Lambdas are compiled into private synthetic methods and linked at runtime using invokedynamic and LambdaMetafactory, which creates an implementation of the functional interface without generating a separate class like anonymous inner classes.
```
```text
In Java, lambdas don’t create a new anonymous class at compile time (like anonymous inner classes used to). Internally they are implemented using invokedynamic and the LambdaMetafactory.

Let’s break it down clearly.

1. Simple Lambda Example
Runnable r = () -> System.out.println("Hello");
r.run();
    At source level it looks simple, but internally several steps happen.

2. What the Compiler Does

When you compile, the compiler:
    Converts the lambda body into a private static method (synthetic method).
    Replaces the lambda expression with an invokedynamic instruction.
    At runtime, JVM creates an implementation of the functional interface.

So lambda is:
Not a class file
Not an anonymous inner class
A runtime-generated object

3. What It Looks Like Internally (Conceptual)

Your code:
    Runnable r = () -> System.out.println("Hello");

Compiler roughly converts to something like:

private static void lambda$main$0() {
    System.out.println("Hello");
}

Then JVM links it using:
    invokedynamic → LambdaMetafactory
    Which creates a Runnable instance whose run() calls:
lambda$main$0()

4. Runtime Flow

Step by step:
JVM sees invokedynamic
Calls LambdaMetafactory
Creates a lightweight function object
Links it to the functional interface method
So execution becomes:

r.run() → lambda$main$0()

5. Why Lambdas Are Faster Than Anonymous Classes

Anonymous class:
Creates a new class file
Loads class into Metaspace
More memory overhead

Lambda:
Uses invokedynamic
No extra class file
Lightweight object creation

This is why lambdas are generally more efficient.

6. Functional Interface Role

Lambda works only because of functional interfaces:

Example:

@FunctionalInterface
interface Printer {
    void print();
}


Lambda:

Printer p = () -> System.out.println("Hi");


Compiler maps:

Lambda → print() implementation

7. Capturing Variables (Closure)

Example:

int x = 10;
Runnable r = () -> System.out.println(x);


Internally:
Variable x is copied as a final value
Lambdas can capture only effectively final variables

Reason:
To keep lambda implementation thread-safe and simple.

8. Anonymous Class vs Lambda (Internal Difference)

Anonymous class:

Runnable r = new Runnable() {
    public void run() {
        System.out.println("Hello");
    }
};


Internally:

New class generated: OuterClass$1.class

Separate object

Lambda:

No new class file

Uses invokedynamic

9. Bytecode Difference (Interview Gold Point)

Lambda uses:
   invokedynamic
Anonymous class uses:
  new
  invokespecial
```
</details>

<details>
<summary>Exceptions in lamdas</summary>

```text
Checked exceptions must be handled inside the lambda using try-catch or wrapped into RuntimeException, because functional interfaces like Consumer or Function do not declare checked exceptions.
```
</details>