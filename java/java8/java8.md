Java 8 was a major release focused on:

- Functional programming - writeing code by using functions , avoiding changing data and focusing what to do then how to do, lamdas and streams
- Streams
- Date & Time API
- Performance improvements

<details>
<summary>Lambda Expressions</summary>

```text
A short way to implement functional interfaces.

before:
Runnable r = new Runnable() {
    public void run() {
        System.out.println("Hello");
    }
};
after:

Runnable r = () -> System.out.println("Hello");
```
</details>
<details>
<summary>Lambda vs Anonymous Inner Class</summary>

```text
Use lambdas when implementing a single-method interface (functional interface) with short, 
readable logic; use anonymous inner classes when you need multiple methods, state, or more complex behavior.
```
</details>

<details>
<summary>Functional Interfaces</summary>

```text
- Interface with only one abstract method
- default and static methods allowed.
- private in java 9 
```
</details>

<details>
<summary>Built in Functional interface</summary>

```text
| Interface       | Method              | Used For                       | Example Usage                | Limitations          |
| --------------- | ------------------- | ------------------------------ | ---------------------------- | -------------------- |
| `Predicate<T>`  | `boolean test(T t)` | Condition checking / filtering | `x -> x > 10`                | Only returns boolean |
| `Function<T,R>` | `R apply(T t)`      | Transform data                 | `x -> x * 2`                 | Single input only    |
| `Consumer<T>`   | `void accept(T t)`  | Perform action (no return)     | `x -> System.out.println(x)` | Cannot return value  |
| `Supplier<T>`   | `T get()`           | Provide/generate values        | `() -> new Date()`           | No input allowed     |

```

```text
list.stream()
    .filter(x -> x > 10); --> uses predicate
    .map(x -> x * 2);     ---> Function
list.forEach(x -> System.out.println(x));  --> consumer

Supplier<Double> s = () -> Math.random(); -->Supplier
```

```text
Primitive Specializations
| Interface        | Purpose                 | Example                      |
| ---------------- | ----------------------- | ---------------------------- |
| `IntPredicate`   | Predicate for int       | `x -> x > 5`                 |
| `IntFunction<R>` | Function with int input | `x -> x * 2`                 |
| `IntConsumer`    | Consume int             | `x -> System.out.println(x)` |
| `IntSupplier`    | Supply int              | `() -> 10`                   |

```

```text
Bi Functional Interfaces (Two Inputs)

| Interface           | Method        | Used For             | Example                | Limitation      |
| ------------------- | ------------- | -------------------- | ---------------------- | --------------- |
| `BiPredicate<T,U>`  | `test(T,U)`   | Compare two values   | `(a,b) -> a.equals(b)` | Only two params |
| `BiFunction<T,U,R>` | `apply(T,U)`  | Transform two inputs | `(a,b) -> a+b`         | Two inputs only |
| `BiConsumer<T,U>`   | `accept(T,U)` | Process two values   | `(k,v)->map.put(k,v)`  | No return       |

```

```text
Unary & Binary Operators

| Interface           | Used For               | Example      | Limitation         |
| ------------------- | ---------------------- | ------------ | ------------------ |
| `UnaryOperator<T>`  | Same input/output type | `x -> x*x`   | Same type only     |
| `BinaryOperator<T>` | Combine two same types | `(a,b)->a+b` | Same type required |

```

```text
| Interface         | Purpose        | Example        |
| ----------------- | -------------- | -------------- |
| `Comparator<T>`   | Sorting        | `(a,b) -> a-b` |
| `BooleanSupplier` | Supply boolean | `() -> true`   |

| Interface         | Purpose        | Example        |
| ----------------- | -------------- | -------------- |
| `Comparator<T>`   | Sorting        | `(a,b) -> a-b` |
| `BooleanSupplier` | Supply boolean | `() -> true`   |

```

```text
| Feature            | Functional Interface Used |
| ------------------ | ------------------------- |
| Stream filter      | Predicate                 |
| Stream map         | Function                  |
| forEach            | Consumer                  |
| Optional.orElseGet | Supplier                  |
| Collectors         | BiConsumer / Supplier     |
| Sorting            | Comparator                |

```
</details>

<details>
<summary>limitations of Lambda</summary>

```text
| Limitation                 | Explanation                          |
| -------------------------- | ------------------------------------ |
| Only one abstract method   | Otherwise not functional             |
| No state in lambda         | Cannot declare instance variables    |
| Checked exceptions awkward | Need try/catch or wrapper            |
| Harder debugging           | Lambdas hide stack context sometimes |

```
</details>


<details>
<summary>Streams API</summary>
Streams process collections in functional style.

```text
list.stream()
    .filter(x -> x > 10)
    .map(x -> x * 2)
    .forEach(System.out::println);

```
Source → Intermediate → Terminal
list → filter → map → collect

</details>

<details>
<summary>Intermediate and terminal</summary>

```text
Intermediate → lazy, return Stream
Terminal → trigger execution, return result

source → intermediate → intermediate → terminal

    
```
intermediate
```text
| Operation            | Method Signature              | Used For                  | Example                        | Notes / Limitations                |
| -------------------- | ----------------------------- | ------------------------- | ------------------------------ | ---------------------------------- |
| `filter()`           | `Stream<T> filter(Predicate)` | Select elements           | `s.filter(x -> x>10)`          | Does not execute until terminal op |
| `map()`              | `Stream<R> map(Function)`     | Transform elements        | `s.map(x -> x*2)`              | One-to-one mapping                 |
| `flatMap()`          | `Stream<R> flatMap(Function)` | Flatten nested structures | `list.flatMap(List::stream)`   | Used for nested collections        |
| `distinct()`         | `Stream<T> distinct()`        | Remove duplicates         | `s.distinct()`                 | Uses `equals()`                    |
| `sorted()`           | `Stream<T> sorted()`          | Natural sorting           | `s.sorted()`                   | Elements must be Comparable        |
| `sorted(Comparator)` | `Stream<T>`                   | Custom sorting            | `s.sorted((a,b)->b-a)`         | Slower than hashing                |
| `limit()`            | `Stream<T> limit(n)`          | Restrict size             | `s.limit(5)`                   | Short-circuiting                   |
| `skip()`             | `Stream<T> skip(n)`           | Skip first n              | `s.skip(3)`                    | Often used with pagination         |
| `peek()`             | `Stream<T> peek(Consumer)`    | Debugging/logging         | `s.peek(System.out::println)`  | Not for business logic             |
| `mapToInt()`         | `IntStream mapToInt()`        | Primitive conversion      | `s.mapToInt(x->x)`             | Avoid boxing                       |
| `mapToLong()`        | `LongStream`                  | Primitive conversion      | `s.mapToLong()`                | Performance use                    |
| `mapToDouble()`      | `DoubleStream`                | Primitive conversion      | `s.mapToDouble()`              | Numeric streams                    |
| `flatMapToInt()`     | `IntStream`                   | Flatten primitive streams | `flatMapToInt()`               | Less common                        |
| `unordered()`        | `Stream<T>`                   | Ignore encounter order    | `parallelStream().unordered()` | Parallel optimization              |
| `parallel()`         | `Stream<T>`                   | Convert to parallel       | `s.parallel()`                 | Depends on workload                |
| `sequential()`       | `Stream<T>`                   | Convert to sequential     | `s.sequential()`               | Rarely used                        |

```

terminal
```text
| Operation          | Method Signature         | Used For              | Example                             | Result Type |
| ------------------ | ------------------------ | --------------------- | ----------------------------------- | ----------- |
| `forEach()`        | `void forEach(Consumer)` | Iterate elements      | `s.forEach(System.out::println)`    | void        |
| `forEachOrdered()` | `void`                   | Maintain order        | `parallelStream().forEachOrdered()` | void        |
| `collect()`        | `R collect(Collector)`   | Convert to collection | `s.collect(Collectors.toList())`    | Collection  |
| `toArray()`        | `Object[] toArray()`     | Convert to array      | `s.toArray()`                       | Array       |
| `reduce()`         | `Optional<T> reduce()`   | Aggregate values      | `s.reduce((a,b)->a+b)`              | Optional    |
| `count()`          | `long count()`           | Count elements        | `s.count()`                         | long        |
| `findFirst()`      | `Optional<T>`            | First element         | `s.findFirst()`                     | Optional    |
| `findAny()`        | `Optional<T>`            | Any element           | `s.findAny()`                       | Optional    |
| `anyMatch()`       | `boolean`                | Any matches condition | `s.anyMatch(x->x>10)`               | boolean     |
| `allMatch()`       | `boolean`                | All match             | `s.allMatch(x->x>0)`                | boolean     |
| `noneMatch()`      | `boolean`                | None match            | `s.noneMatch(x->x<0)`               | boolean     |
| `min()`            | `Optional<T>`            | Minimum value         | `s.min(Integer::compare)`           | Optional    |
| `max()`            | `Optional<T>`            | Maximum value         | `s.max(Integer::compare)`           | Optional    |
| `iterator()`       | `Iterator<T>`            | Manual iteration      | `s.iterator()`                      | Iterator    |
| `spliterator()`    | `Spliterator<T>`         | Parallel processing   | `s.spliterator()`                   | Spliterator |

```

collectors
```text
| Collector          | Purpose          | Example                         |
| ------------------ | ---------------- | ------------------------------- |
| `toList()`         | Convert to List  | `collect(Collectors.toList())`  |
| `toSet()`          | Convert to Set   | `collect(Collectors.toSet())`   |
| `toMap()`          | Convert to Map   | `collect(Collectors.toMap())`   |
| `groupingBy()`     | Group elements   | `groupingBy(Employee::getDept)` |
| `partitioningBy()` | Boolean grouping | `partitioningBy(x->x>10)`       |
| `joining()`        | Join strings     | `joining(",")`                  |
| `counting()`       | Count elements   | `counting()`                    |
| `averagingInt()`   | Average          | `averagingInt()`                |
| `summarizingInt()` | Stats            | `summarizingInt()`              |

```

```text
1. toList()
List<String> names =
    stream.collect(Collectors.toList());


Converts stream → List.

2. toSet()
Set<Integer> set =
    stream.collect(Collectors.toSet());


Removes duplicates.

3. joining()

Used for Strings.

String result =
    List.of("Java","Spring","Boot")
        .stream()
        .collect(Collectors.joining(", "));


Output:

Java, Spring, Boot

4. counting()
long count =
    list.stream()
        .collect(Collectors.counting());


Counts elements.

5. groupingBy() ⭐ Very Important

Used to group data.

Example:

Map<Integer, List<String>> grouped =
    names.stream()
         .collect(Collectors.groupingBy(String::length));


Output:

3 → [Ram, Sam]
4 → [John]

6. partitioningBy()

Splits into two groups (true/false).

Map<Boolean, List<Integer>> result =
    numbers.stream()
           .collect(Collectors.partitioningBy(n -> n % 2 == 0));


Output:

true → even numbers
false → odd numbers

7. mapping() (Used inside grouping)

Example:
Group employees by department but collect only names:

Map<String, List<String>> result =
    employees.stream()
        .collect(Collectors.groupingBy(
            Employee::getDept,
            Collectors.mapping(Employee::getName, Collectors.toList())
        ));

8. collectingAndThen()

Post-process result.

Example:
Get list and make it unmodifiable.

List<String> list =
    stream.collect(Collectors.collectingAndThen(
        Collectors.toList(),
        Collections::unmodifiableList
    ));

9. summing / averaging
int total =
    employees.stream()
             .collect(Collectors.summingInt(Employee::getSalary));

10. toMap() ⭐ Important
Map<Integer,String> map =
    employees.stream()
             .collect(Collectors.toMap(
                 Employee::getId,
                 Employee::getName
             ));


Important interview point:
If duplicate keys occur → Exception.

Fix:

Collectors.toMap(
   Employee::getId,
   Employee::getName,
   (oldVal, newVal) -> oldVal
);
```
</details>

<details>
<summary>Stream pipeline internal working</summary>

```text
A Stream pipeline processes data lazily using a chain of intermediate operations that are fused together, and execution starts only when a terminal operation pulls elements through the pipeline.

stream pipeline:
    Source → Intermediate Operations → Terminal Operation
    
Important Concept: Lazy Evaluation
    Intermediate operations:
     filter()
     map()
     limit()
- Do NOT execute immediately.
- They only build a pipeline description.

** Execution starts only when:
    forEach()
    collect()
    reduce()
runs.
```
How execution actually happens

```text
Many developers think:
    filter all → map all → limit all
❌ Wrong
Actual execution:
    One element at a time flows through whole pipeline

Example Input
[5, 12, 20, 3, 25]

Pipeline:
    .filter(x -> x > 10)
    .map(x -> x * 2)
    .limit(2)

Execution:
    5   → filter fail → stop
    12  → filter pass → map → 24 → output
    20  → filter pass → map → 40 → output
    limit reached → STOP

Notice:
    3 and 25 never processed

This is called:
    🔥 Short-circuiting
```

Internal Architecture

```text
Inside JVM:
    Stream builds a chain of objects called:

Pipeline stages
    Each stage wraps the previous stage.

Simplified structure:
    Source → Stage → Stage → Stage → Terminal


Internally classes like:
    ReferencePipeline
    Sink
    Spliterator
are used.

Internal Flow:
 Role of Spliterator

Source (like List) provides:
Spliterator
It:
traverses elements
splits data (for parallel streams)

Example sources:
ArrayList → ArrayListSpliterator
HashMap → KeySpliterator

- Operation Fusion (Performance Benefit)
Instead of:
    multiple loops
Stream internally behaves like:
single loop


```
</details>

<details>
<summary>Parallel stream internals</summary>

```text
parallelStream() splits data using a Spliterator, processes chunks in parallel using the ForkJoinPool, and then merges the results into a final result.

Collection
   ↓
Spliterator splits data
   ↓
ForkJoinPool threads process chunks
   ↓
Results merged

```
Spliterator
```text
Role of Spliterator:
    Every stream source provides a Spliterator.

It does two things:
    Traverse elements
    Split data into smaller chunks

Example:
[1,2,3,4,5,6,7,8]

Split → [1,2,3,4]  [5,6,7,8]
Split → [1,2] [3,4] [5,6] [7,8]

This enables parallel processing.
```
ForkJoinPool
```text
ForkJoinPool Execution:

Parallel streams use:
    ForkJoinPool.commonPool()

Default threads:
    CPU cores - 1
Example on 8-core machine:
    7 worker threads

Fork-Join Algorithm
Fork:
    Split task into subtasks.
Join:
    Combine results.
```
Pipeline Execution in Parallel
```text
Each thread runs the same pipeline:
    filter → map → collect

But on different subsets of data.
Example:

Thread 1:
    [1,2,3]
Thread 2:
    [4,5,6]
Thread 3:
    [7,8]

Combining Results:
Terminal operation merges results.

For example:
collect(Collectors.toList())
Internally:
- Each thread builds partial result
- Combiner merges them
```

bad for shared mutable state
order not guaranteed
    - forEachOrdered()
</details>



<details>
<summary>Parallel Streams – Pitfalls & Real Bugs</summary>

```text
| Issue                                  | Why it Happens                                                 | How to Resolve                                           | Best Approach                                       |
| -------------------------------------- | -------------------------------------------------------------- | -------------------------------------------------------- | --------------------------------------------------- |
| Race condition with shared collections | Multiple threads modify same object (e.g., ArrayList, HashMap) | Use `collect()` or concurrent collections                | Prefer `Collectors.toList()` or `ConcurrentHashMap` |
| Wrong results in `reduce()`            | Operation not associative or order-dependent                   | Use associative operations (sum, min, max)               | Ensure `(a op b) op c == a op (b op c)`             |
| Order not preserved                    | Parallel execution processes chunks independently              | Use `forEachOrdered()` if order required                 | Avoid parallel when order matters heavily           |
| Slower performance on small data       | Thread creation, splitting, merging overhead                   | Use sequential stream or loop                            | Use parallel only for large datasets                |
| Blocking operations (DB, HTTP, sleep)  | ForkJoinPool threads get blocked                               | Use `ExecutorService` instead                            | Parallel streams only for CPU-bound tasks           |
| Locking or synchronized blocks         | Threads wait for locks → no parallelism                        | Remove shared locks or redesign logic                    | Prefer stateless functions                          |
| Using non-thread-safe collections      | HashMap, ArrayList not safe for concurrent writes              | Use `ConcurrentHashMap`, `Collectors`                    | Avoid mutating shared state                         |
| High memory / GC pressure              | Each thread creates intermediate objects                       | Reduce object creation or use sequential                 | Prefer primitive streams (`IntStream`)              |
| Parallel streams in web servers        | Uses shared `ForkJoinPool.commonPool()`                        | Use custom executor or sequential stream                 | Avoid parallelStream in request threads             |
| Infinite or very large streams         | Hard to split efficiently                                      | Use bounded streams or sequential                        | Avoid parallel for infinite streams                 |
| Debugging difficulty                   | Multiple threads execute pipeline                              | Use logs carefully or switch to sequential for debugging | Debug sequential first                              |

```

```text
Parallel streams work best when:
✔ large data
✔ CPU-bound
✔ stateless operations
✔ associative reductions

```
```text
avoid 
❌ shared state
❌ blocking I/O
❌ small datasets
❌ strict ordering needed
```
</details>

<details>
<summary>learn about fork join pool</summary>

```text

```
</details>


<details>
<summary>Collectors internals</summary>

```text
Collectors are used in Stream terminal operations to transform or aggregate data.

Example:
    list.stream().collect(Collectors.toList());

But internally a Collector is not just a method — it is a structured object with 4 important functions.
    A Collector works by 
        1. creating a result container
        2. accumulating stream elements into it 
        3. optionally combining partial results in parallel
        4. finally finishing the result.


A Collector consists of:
    Supplier → creates container
    Accumulator → adds element
    Combiner → merges results (parallel streams)
    Finisher → converts result (optional)


🟡 Step-by-step Execution

Example:
List<Integer> result =
    list.stream().collect(Collectors.toList());

Internally:
Step 1 – Supplier
    Create empty container:
        - new ArrayList<>()

Step 2 – Accumulator
    For each element:
        list.add(element)

Step 3 – Combiner (parallel only)
    Merge partial lists:
        list1.addAll(list2)

Step 4 – Finisher
    Usually identity:
        return list



Collector interface:

public interface Collector<T, A, R> {

    Supplier<A> supplier();

    BiConsumer<A, T> accumulator();

    BinaryOperator<A> combiner();

    Function<A, R> finisher();

    Set<Characteristics> characteristics();
}




Parallel streams create multiple containers:

Thread 1 → partial result
Thread 2 → partial result
Thread 3 → partial result


Then Combiner merges them.



🔵 Example – Custom Collector
Collector<Integer, List<Integer>, List<Integer>> collector =
    Collector.of(
        ArrayList::new,        // supplier
        List::add,             // accumulator
        (l1, l2) -> {          // combiner
            l1.addAll(l2);
            return l1;
        }
    );

🟡 Example – groupingBy Internals
list.stream()
    .collect(Collectors.groupingBy(Employee::getDept));

Internally:
    Map<Dept, List<Employee>>

Steps:
Supplier → new HashMap
Accumulator → map.computeIfAbsent()
Combiner → merge maps


```
</details>

<details>
<summary>reduce() Works Internally</summary>

```text
reduce() processes elements one by one, combining them using an associative function, and in parallel streams it computes partial results in multiple threads and merges them using the same function.

reduce() is used to combine elements into a single result.
int sum = list.stream()
              .reduce(0, (a, b) -> a + b);

[1,2,3,4] → 10

| Version | Signature                                          | Use                              |
| ------- | -------------------------------------------------- | -------------------------------- |
| 1       | `Optional<T> reduce(BinaryOperator<T>)`            | No initial value                 |
| 2       | `T reduce(T identity, BinaryOperator<T>)`          | With initial value               |
| 3       | `U reduce(U identity, BiFunction, BinaryOperator)` | Parallel-friendly transformation |



```
sequential
```text
list.stream().reduce(0, (a, b) -> a + b);

[1,2,3,4]
Execution steps:

result = 0
result = 0 + 1 = 1
result = 1 + 2 = 3
result = 3 + 3 = 6
result = 6 + 4 = 10
So internally it behaves like:

T result = identity;
for (T element : stream)
    result = accumulator.apply(result, element);
```
Parallel Stream
```text
[1,2,3,4,5,6]
Split into:

Thread1 → [1,2]
Thread2 → [3,4]
Thread3 → [5,6]
Each thread reduces locally:

T1 → 3
T2 → 7
T3 → 11
Then combine:

3 + 7 + 11 = 21
```
Accumulator must satisfy:
(a op b) op c = a op (b op c)  --> Associative Operation

```text
Good:
    sum
    multiplication
    min/max

Bad:
    subtraction
    division
    
| Operation     | Identity |
| ------------- | -------- |
| Sum           | 0        |
| Multiply      | 1        |
| String concat | ""       |

```
```text
reduce()
   ↓
identity
   ↓
accumulator (combine elements)
   ↓
combiner (parallel only)
   ↓
final result

```
</details>

<details>
<summary>groupingBy working</summary>

```text
Map<String, List<Employee>> result =
    employees.stream()
             .collect(Collectors.groupingBy(Employee::getDept));


| Step | Phase                        | What Happens Internally                      | Example Action             |
| ---- | ---------------------------- | -------------------------------------------- | -------------------------- |
| 1    | Stream starts                | Stream pipeline is prepared but not executed | `employees.stream()`       |
| 2    | Terminal operation triggered | `collect()` starts execution                 | `collect(groupingBy(...))` |
| 3    | Supplier called              | A new empty Map is created                   | `new HashMap<>()`          |
| 4    | Read element                 | Stream pulls one element at a time           | `Employee e`               |
| 5    | Key generation               | Classifier function applied                  | `e.getDept()`              |
| 6    | Check map                    | Map checked for key existence                | `map.containsKey(dept)`    |
| 7    | Create list if absent        | New list created if key not present          | `computeIfAbsent()`        |
| 8    | Accumulate value             | Element added to list                        | `list.add(e)`              |
| 9    | Repeat                       | Steps 4–8 repeat for all elements            | Loop continues             |
| 10   | Parallel only – Combiner     | Partial maps merged                          | `list1.addAll(list2)`      |
| 11   | Finisher                     | Final Map returned                           | Result Map                 |

input :
[IT, HR, IT, Sales]


| Element | Key   | Action               | Map State                                      |
| ------- | ----- | -------------------- | ---------------------------------------------- |
| Emp1    | IT    | Create list + add    | IT → [Emp1]                                    |
| Emp2    | HR    | Create list + add    | IT → [Emp1], HR → [Emp2]                       |
| Emp3    | IT    | Add to existing list | IT → [Emp1, Emp3]                              |
| Emp4    | Sales | Create list + add    | IT → [Emp1, Emp3], HR → [Emp2], Sales → [Emp4] |

```
</details>


<details>
<summary>summary</summary>

```text
filter → select
map → transform
flatMap → flatten
collect → container
reduce → single value
groupingBy → map of lists
parallel → ForkJoinPool

```
</details>

<details>
<summary>Method References</summary>

```text
Shortcut for lambda.

Types:
    1. Static method reference
    2. Instance method reference
    3. Constructor reference
    
list.forEach(System.out::println);

Supplier<List<String>> list = ArrayList::new;


Method references work only with functional interfaces because they are a shorthand form of lambda expressions, and lambdas can only implement single-abstract-method interfaces.

When you write:
    Consumer<String> c = System.out::println;
Compiler thinks:
    Consumer.accept(String x)

Then it maps:
    accept(x) → System.out.println(x)

So:
    method reference = implementation of functional interface method
Not the other way around.
```
</details>


<details>
<summary>default, static and private in interface</summary>

```text
1️⃣ Default Methods – Why Introduced
Problem (Before Java 8)

Step 1: Interfaces could only have abstract methods
Step 2: If a new method was added to an interface
Step 3: All implementing classes were forced to implement it
Step 4: This broke existing applications (backward compatibility issue)

Example problem:

interface List {
   void add();
}


Later:
void sort();   // breaks all implementations

Solution (Java 8)

Step 1: Allow methods with implementation in interfaces
Step 2: Mark them with default
Step 3: Existing classes continue working
Step 4: New behavior becomes available

Example:

default void sort() {
   // implementation
}

Purpose of Default Methods

Step 1: Backward compatibility
Step 2: Interface evolution
Step 3: Add new features to APIs safely

Real examples:

Collection.stream()
Iterable.forEach()
Map.forEach()
```
```text
2️⃣ Static Methods – Why Introduced
Problem (Before Java 8)

Step 1: Utility methods were placed in helper classes
Step 2: Example: Collections.sort()
Step 3: Related logic was separated from interface
Step 4: API design became less intuitive

Solution

Step 1: Allow static methods inside interfaces
Step 2: Keep utility behavior close to abstraction
Step 3: Improve API readability

Example:

Comparator.comparing(...)
Comparator.naturalOrder()

Purpose of Static Methods

Step 1: Utility/helper methods
Step 2: Better API organization
Step 3: Cleaner design
```
```text
3️⃣ Private Methods – Why Introduced (Java 9)
Problem

Step 1: Multiple default methods needed common logic
Step 2: Code duplication occurred
Step 3: No way to share logic inside interface

Example:

default void m1() { log(); }
default void m2() { log(); }

Solution

Step 1: Allow private methods inside interfaces
Step 2: Use them as helpers
Step 3: Reduce duplication

Example:

private void log() {
   System.out.println("log");
}

Purpose of Private Methods

Step 1: Code reuse inside interface
Step 2: Cleaner default method design
Step 3: Encapsulation of helper logic
```
```text
4️⃣ Does This Break 100% Abstraction?
Traditional Rule

Step 1: Interfaces were fully abstract
Step 2: No implementation allowed

After Java 8

Step 1: Interfaces can have default and static methods
Step 2: But implementing classes are NOT forced to use them
Step 3: Default methods can be overridden
Step 4: Interface still defines a contract

Conclusion:

Abstraction is relaxed, not broken
```
```text
5️⃣ Why Java Needed These Changes (Real Reason)

Step 1: Java 8 introduced Streams and Lambdas
Step 2: Collection interfaces needed new methods
Step 3: Millions of existing implementations existed
Step 4: Default methods allowed safe API evolution
```
```text
6️⃣ Quick Memory Summary
Default → backward compatibility
Static → utility methods in interface
Private → code reuse inside interface
```
```text
7️⃣ Interview One-Liner

Default methods were introduced for backward compatibility, static methods for better API design, and private methods for code reuse inside interfaces, while still preserving abstraction.
```
</details>

<details>
<summary>Optional Class</summary>
- Optional<T> is a container object that may or may not contain a value.

```text
Used to avoid NullPointerException.

Optional<String> name = Optional.ofNullable(value);

name.ifPresent(System.out::println);

```


```text
1. Value definitely present
    Optional<String> opt = Optional.of("Java");
    ⚠️ If null passed → NPE

2. Value may be null (most common)
    Optional<String> opt = Optional.ofNullable(name);
    Safe way.

3. Empty optional
    Optional<String> opt = Optional.empty();


Common Operations (Most Important)

1. ifPresent()
    opt.ifPresent(val -> System.out.println(val));
    Runs only if value exists.

2. orElse()
    String name = opt.orElse("Default");
    If empty → returns default.

3. orElseGet() (better in many cases)
    String name = opt.orElseGet(() -> getDefaultName());


Difference:
    orElse() always evaluates (value present or null)
    orElseGet() evaluates only when value is null

4. orElseThrow()
    User user = opt.orElseThrow(() -> new RuntimeException("Not found"));
    Good for service layer.

5. map() (Very Important)
    Transforms value safely:
    Optional<String> name =userOptional.map(User::getName);
    No null checks needed.

6. flatMap()
    Used when method already returns Optional.
    Optional<String> city =
        userOptional.flatMap(User::getAddress)
                    .map(Address::getCity); 
Avoids Optional<Optional<T>>.
===================================================================================================
Real Example (Good Practice)

Without Optional:

        if (user != null) {
            Address addr = user.getAddress();
            if (addr != null) {
                System.out.println(addr.getCity());
            }
        }


With Optional:

        Optional.ofNullable(user)
                .map(User::getAddress)
                .map(Address::getCity)
                .ifPresent(System.out::println);


Cleaner and safer.
```
</details>

<details>
<summary>Date and Time</summary>

```text
Java 8 introduced a new Date/Time API in the package:
java.time

Goal:
    Immutable
    Thread-safe
    Clearer API than Date and Calendar
    
 | Class               | What it Represents                  | Example Creation                               | Common Methods                                  | Typical Use Case          |
| ------------------- | ----------------------------------- | ---------------------------------------------- | ----------------------------------------------- | ------------------------- |
| `LocalDate`         | Date only (no time)                 | `LocalDate.now()`                              | `plusDays()`, `minusMonths()`, `getDayOfWeek()` | Birthdays, business dates |
| `LocalTime`         | Time only                           | `LocalTime.now()`                              | `plusHours()`, `minusMinutes()`                 | Store time of day         |
| `LocalDateTime`     | Date + Time                         | `LocalDateTime.now()`                          | `plusDays()`, `minusHours()`                    | Application timestamps    |
| `ZonedDateTime`     | Date + Time + Timezone              | `ZonedDateTime.now(ZoneId.of("Asia/Kolkata"))` | `withZoneSameInstant()`                         | Global apps               |
| `Instant`           | UTC timestamp (epoch-based)         | `Instant.now()`                                | `plusSeconds()`                                 | Logging, APIs, DB         |
| `Duration`          | Time difference (hours/min/sec)     | `Duration.between(t1,t2)`                      | `toMinutes()`, `toHours()`                      | Measure execution time    |
| `Period`            | Date difference (years/months/days) | `Period.between(d1,d2)`                        | `getDays()`                                     | Age, date gaps            |
| `DateTimeFormatter` | Formatting & parsing                | `DateTimeFormatter.ofPattern("dd-MM-yyyy")`    | `format()`, `parse()`                           | Display & parsing         |


```
</details>


<details>
<summary>CompletableFuture</summary>

```text
CompletableFuture in Java is a class introduced in Java 8 in the java.util.concurrent package that helps you write asynchronous, non-blocking code and easily chain multiple tasks.

Think of it as an improved version of Future with extra capabilities.

1. Why CompletableFuture was introduced
    Before Java 8 we had Future.
    Problems with Future:
        Cannot manually complete
        Cannot chain tasks
        Cannot combine multiple futures
        Blocking get() only way to retrieve result

CompletableFuture solves all of these.
```
```text
2. Basic Idea (Simple Analogy)

Imagine ordering food online:
    Order placed → Task started
    Food prepared → Processing
    Delivered → Result available

You don’t wait in the kitchen (non-blocking).
You get notified when ready.

That’s exactly how CompletableFuture works.
```
```text
3. Creating CompletableFuture

Method 1: runAsync (no return)
    CompletableFuture<Void> future =
        CompletableFuture.runAsync(() -> {
            System.out.println("Running async task");
        });

Used when:
    You don’t need a result

Method 2: supplyAsync (returns value)
    CompletableFuture<String> future =
        CompletableFuture.supplyAsync(() -> "Hello Vivek");

    System.out.println(future.get());

Used when:
    Task returns a value
```
```text
4. Getting Result
Blocking way
    future.get();   // checked exception
    future.join();  // unchecked exception


Interview point:
    join() preferred in modern code
```
```text
5. Chaining (Most Important Feature)

This is why CompletableFuture is powerful.

thenApply (transform result)
CompletableFuture<Integer> future =
    CompletableFuture.supplyAsync(() -> 10)
        .thenApply(x -> x * 2);

System.out.println(future.join()); // 20


Meaning:
Take result
Transform it
Return new future

thenAccept (consume result)
CompletableFuture.supplyAsync(() -> 10)
        .thenAccept(x -> System.out.println(x));


No return value.

thenRun (no input, no output)
CompletableFuture.supplyAsync(() -> 10)
        .thenRun(() -> System.out.println("Task finished"));
```
```text
6. Combining Futures
thenCombine
CompletableFuture<Integer> f1 =
    CompletableFuture.supplyAsync(() -> 10);

CompletableFuture<Integer> f2 =
    CompletableFuture.supplyAsync(() -> 20);

CompletableFuture<Integer> result =
    f1.thenCombine(f2, (a, b) -> a + b);

System.out.println(result.join()); // 30


Use case:
Calling 2 APIs
Combining results
```
```text
7. Running Multiple Tasks
allOf
CompletableFuture<Void> all =
    CompletableFuture.allOf(f1, f2);

all.join();


Wait for all tasks.

anyOf
CompletableFuture<Object> any =
    CompletableFuture.anyOf(f1, f2);


First completed task returns.
```
```text
8. Exception Handling (Very Important for Interviews)
exceptionally
CompletableFuture<Integer> future =
    CompletableFuture.supplyAsync(() -> 10 / 0)
        .exceptionally(ex -> {
            System.out.println("Error: " + ex);
            return 0;
        });

handle (better)
future.handle((result, ex) -> {
    if (ex != null) return 0;
    return result;
});
```
```text
9. Custom Thread Pool (Real Production Use)

By default uses:

ForkJoinPool.commonPool()
Better approach:

ExecutorService executor = Executors.newFixedThreadPool(5);

CompletableFuture.supplyAsync(() -> "Hello", executor);


Important for:
Spring Boot microservices
API calls
DB calls
```
```text
10. Internal Flow (How it works)

Steps:
Task submitted
Runs in ForkJoinPool thread
Result stored
Next stage triggered automatically
Non-blocking chaining is key.
```
```text

11. Real Use Case (Spring Boot Example)

Example:
Call User Service
Call Order Service

Combine result

CompletableFuture<User> userFuture = 
    CompletableFuture.supplyAsync(() -> getUser());

CompletableFuture<Order> orderFuture =
    CompletableFuture.supplyAsync(() -> getOrders());

CompletableFuture<Result> result =
    userFuture.thenCombine(orderFuture,
        (user, order) -> new Result(user, order));


Used heavily in:
Microservices
Aggregator APIs
Parallel API calls
```
```text
12. Future vs CompletableFuture (Interview Table)
Feature	Future	CompletableFuture
Blocking get	Yes	Yes
Non-blocking chaining	No	Yes
Combine tasks	No	Yes
Exception handling	Poor	Good
Manual completion	No	Yes
Functional style	No	Yes
```
```text
13. Important Interview Questions

Q1: Difference between thenApply and thenCompose

thenApply → Transform result
thenCompose → Flatten nested future

Example:

thenApply → Future<Future<T>>
thenCompose → Future<T>


Q2: Default thread pool used?
ForkJoinPool.commonPool()

Q3: Is CompletableFuture thread safe?
Yes.

Q4: Difference between get() and join()?

get()	join()
Checked exception	Runtime exception
Old style	Modern preferred
14. When to Use in Real Projects

Use CompletableFuture when:

Multiple API calls

Parallel processing

Microservices aggregation

Background tasks

Do NOT use when:

Simple sequential logic

15. Quick Memory Trick

Think of CompletableFuture as:

Async + Chain + Combine + Handle Errors
```
</details>


<details>
<summary>java 8 to 21</summary>


```text
| Java Version      | Important Features                                                                                                                                        | What to Remember for Interviews      |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| **Java 9**        | Module System (Project Jigsaw), JShell, `List.of()`, Stream improvements (`takeWhile`, `dropWhile`), Optional improvements, private methods in interfaces | Module system is the biggest feature |
| **Java 10**       | `var` (local variable type inference), `List.copyOf()`, Optional.orElseThrow(), G1 GC improvements                                                        | `var` is the key feature             |
| **Java 11 (LTS)** | HTTP Client API, String methods (`isBlank`, `lines`, `repeat`), `Files.readString()`, Lambda `var` parameters, Removed Java EE modules                    | Java 11 widely used in production    |
| **Java 12**       | Switch expression (preview), JVM improvements                                                                                                             | Switch expression introduced         |
| **Java 13**       | Text Blocks (preview), Switch improvements                                                                                                                | Multiline strings start here         |
| **Java 14**       | Records (preview), Switch expression (standard), Helpful NullPointerExceptions                                                                            | Better NPE messages                  |
| **Java 15**       | Text Blocks (standard), Sealed Classes (preview), Hidden classes                                                                                          | Text blocks finalized                |
| **Java 16**       | Records (standard), Pattern matching for instanceof, Stream.toList()                                                                                      | Records finalized                    |
| **Java 17 (LTS)** | Sealed Classes (standard), Pattern matching improvements, Strong encapsulation, New macOS rendering pipeline                                              | Very important LTS version           |
| **Java 18**       | Simple Web Server, UTF-8 default charset                                                                                                                  | Minor release                        |
| **Java 19**       | Virtual Threads (preview), Structured concurrency (preview)                                                                                               | Start of Project Loom                |
| **Java 20**       | Virtual Threads improvements, Scoped values (preview)                                                                                                     | Loom refinements                     |
| **Java 21 (LTS)** | Virtual Threads (standard), Pattern Matching for switch (standard), Record Patterns, Sequenced Collections                                                | Virtual Threads = biggest feature    |

```


</details>