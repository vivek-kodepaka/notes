## Collections




<details>
<summary>List comparison</summary>

```text
| Way to Create List               | Example                                           | Mutable?          | Thread Safe?            | Limitations                                 | Interview Traps                                                         |
| -------------------------------- | ------------------------------------------------- | ----------------- | ----------------------- | ------------------------------------------- | ----------------------------------------------------------------------- |
| **ArrayList**                    | `new ArrayList<>()`                               | Yes               | No                      | Resize cost, not thread-safe                | Why add() is amortized O(1)? Why faster than LinkedList in practice?    |
| **LinkedList**                   | `new LinkedList<>()`                              | Yes               | No                      | High memory, slow random access             | Why LinkedList rarely faster than ArrayList? removeLast() O(1) or O(n)? |
| **Vector** (legacy)              | `new Vector<>()`                                  | Yes               | Yes                     | Global synchronization → slow               | Difference between Vector and synchronizedList?                         |
| **Stack** (legacy)               | `new Stack<>()`                                   | Yes               | Yes                     | Legacy, slower than Deque                   | Why Deque preferred over Stack?                                         |
| **Collections.synchronizedList** | `Collections.synchronizedList(new ArrayList<>())` | Yes               | Yes                     | Iterator still needs manual synchronization | Why still fail-fast? How to safely iterate?                             |
| **CopyOnWriteArrayList**         | `new CopyOnWriteArrayList<>()`                    | Yes               | Yes                     | Expensive writes (copies array)             | When is CopyOnWrite useful? Why not for write-heavy systems?            |
| **Arrays.asList**                | `Arrays.asList(1,2,3)`                            | Partially         | No                      | Fixed size, backed by array                 | Why add/remove throws exception? Difference from ArrayList?             |-- implemented using  array (fixed size) -> so add/remove blocked --> set/update works fine
| **List.of (Java 9+)**            | `List.of(1,2,3)`                                  | No (immutable)    | Yes                     | No nulls, cannot modify                     | Difference between immutable and unmodifiable list?                     |-- cant chnage state
| **Collections.unmodifiableList** | `Collections.unmodifiableList(list)`              | No (view only)    | Depends on backing list | Underlying list can still change            | Difference between List.of() and unmodifiableList()?                    |-- unmodified(list of any type) --> waraps my list --> orignal list can change but wrapped one cannot change
| **Stream → collect()**           | `stream.collect(Collectors.toList())`             | Yes               | No                      | Type may vary, not guaranteed ArrayList     | Why prefer `toCollection(ArrayList::new)` sometimes?                    |
| **Stream → toList() (Java 16+)** | `stream.toList()`                                 | No (unmodifiable) | Yes                     | Cannot modify result                        | Difference between collect(toList()) and toList()?                      |
| **subList() (view)**             | `list.subList(0,3)`                               | Yes               | Depends                 | Structural changes affect parent            | Why ConcurrentModificationException occurs?                             |
| **Custom List (AbstractList)**   | extend `AbstractList`                             | Depends           | Depends                 | Rare, complex                               | When would you implement your own List?                                 |

```

```text
| Collection                       | Internal DS         | Lock Type          | Thread-safe | Best For               | Can Do Well              | Cannot Do                 |
| ---------------------------------| ------------------- | ------------------ | ----------- | ---------------------- | ------------------------ | ------------------------- |
| **Java ArrayList**               | Dynamic array       | none               | ❌          | fast reads             | O(1) get, cache friendly | slow middle insert/delete |
| **LinkedList**                   | Doubly linked list  | none               | ❌          | frequent insert/remove | O(1) ends                | slow random access        |
| **Vector**                       | Dynamic array       | synchronized(this) | ✔           | legacy thread-safe     | simple thread safety     | slow (full lock)          |
| **CopyOnWriteArrayList**         | Array copy-on-write | write lock only    | ✔           | read-heavy             | lock-free reads          | very slow writes          |
|**Collections.synchronizedList**  | wrapper             | single lock        | ✔           | basic safety           | easy                     | blocking                  |
```    

</details>



<details>
<summary>Set Comparison</summary>


```text
| Implementation                    | Order Maintained           | Sorted   | Null Allowed | Thread Safe   | Internal Structure                     | Iterator Type        | When to Use                   | Interview Traps / Notes                              |
| --------------------------------- | -------------------------  | -------- | ------------ | ------------- | -------------------------------------- | -------------------- | ----------------------------- | ---------------------------------------------------- |
| **HashSet**                       | ❌ No order                | ❌ No     | ✔ One null   | ❌             | HashMap (buckets + hashing)            | Fail-fast            | General purpose, fastest set  | Order may appear sorted sometimes but not guaranteed |
| **LinkedHashSet**                 | ✔ Insertion order          | ❌ No     | ✔ One null   | ❌             | HashMap + Doubly Linked List           | Fail-fast            | When insertion order matters  | Many people confuse it with HashSet                  |
| **TreeSet**                       | ❌ Insertion order         | ✔ Sorted | ❌ No null    | ❌             | Red-Black Tree (BBST)                  | Fail-fast            | Sorted data, range queries    | Uses compareTo / Comparator                          |
| **EnumSet**                       | ✔ Natural order of enum    | ✔ Sorted | ❌ No null    | ❌             | Bit vector (very compact)              | Fail-fast            | Best performance for enums    | Fastest Set implementation                           |
| **Collections.synchronizedSet**   | Depends on backing set     | Depends  | Depends      | ✔             | Wrapper over another Set               | Fail-fast            | Quick thread safety           | Iteration must be manually synchronized              |
| **CopyOnWriteArraySet**           | ✔ Insertion order          | ❌ No     | ❌ No null    | ✔             | CopyOnWriteArrayList                   | Fail-safe (snapshot) | Read-heavy concurrent systems | Writes are expensive                                 |
| **ConcurrentHashMap.newKeySet()** | ❌ No order                | ❌ No     | ❌ No null    | ✔             | ConcurrentHashMap (CAS + fine locking) | Weakly consistent    | High concurrency              | Faster than synchronizedSet                          |
| **ConcurrentSkipListSet**         | ❌ Insertion order         | ✔ Sorted | ❌ No null    | ✔             | Skip List                              | Weakly consistent    | Concurrent sorted data        | Alternative to TreeSet in concurrency                |
| **Set.of() (Java 9+)**            | Preserves insertion order  | ❌ No     | ❌ No null    | ✔ (immutable) | Compact immutable structure            | Immutable            | Constants, config             | Immutable vs unmodifiable confusion                  |
| **Collections.unmodifiableSet**   | Depends on backing set     | Depends  | Depends      | Depends       | Wrapper                                | Fail-fast            | Read-only view                | Underlying set can still change                      |

```

```text
| Collection                           | Internal DS         | Lock Type        | Thread-safe | Best For         | Can Do Well           | Cannot Do      |
| ------------------------------------ | ------------------- | ---------------- | ----------- | ---------------- | --------------------- | -------------- |
| **HashSet**                          | HashMap             | none             | ❌           | unique items     | O(1) search           | no order       |
| **LinkedHashSet**                    | Hash + linked list  | none             | ❌           | insertion order  | predictable iteration | extra memory   |
| **TreeSet**                          | Red-Black Tree      | none             | ❌           | sorted set       | range queries         | slower (log n) |
| **Collections.synchronizedSet**      | wrapper             | single lock      | ✔           | basic safety     | easy                  | blocking       |
| **CopyOnWriteArraySet**              | copy-on-write array | write lock       | ✔           | read-heavy       | safe iteration        | heavy writes   |
| **ConcurrentHashMap `.newKeySet()`** | hash + CAS          | fine-grained/CAS | ✔           | high concurrency | fastest concurrent    | no order       |
```
</details>


<details>
<summary>Queue Comparison</summary>

```text
| Implementation                      | Ordering Rule                  | Thread Safe | Null Allowed | Internal Structure           | Blocking? | Iterator Type     | When to Use                             | Interview Traps / Notes                      |
| ----------------------------------- | ------------------------------ | ----------- | ------------ | ---------------------------- | --------- | ----------------- | --------------------------------------- | -------------------------------------------- |
| **LinkedList (as Queue)**           | FIFO                           | ❌           | ✔            | Doubly Linked List           | ❌         | Fail-fast         | Simple FIFO queue                       | Slower than ArrayDeque in most cases         |
| **ArrayDeque**                      | FIFO / LIFO (Deque)            | ❌           | ❌            | Resizable circular array     | ❌         | Fail-fast         | Best general-purpose queue              | Faster than LinkedList due to cache locality |
| **PriorityQueue**                   | Priority (min-heap by default) | ❌           | ❌            | Binary Heap (array)          | ❌         | Fail-fast         | Scheduling, top-K problems              | Printing does NOT show sorted order          |
| **ConcurrentLinkedQueue**           | FIFO                           | ✔           | ❌            | Lock-free linked nodes (CAS) | ❌         | Weakly consistent | High concurrency, non-blocking          | Size() is costly (traverses)                 |
| **ArrayBlockingQueue**              | FIFO                           | ✔           | ❌            | Fixed-size array             | ✔         | Weakly consistent | Producer–consumer with bounded capacity | Capacity cannot grow                         |
| **LinkedBlockingQueue**             | FIFO                           | ✔           | ❌            | Linked nodes                 | ✔         | Weakly consistent | Producer–consumer with large capacity   | Higher memory usage                          |
| **PriorityBlockingQueue**           | Priority                       | ✔           | ❌            | Heap                         | ✔         | Weakly consistent | Concurrent scheduling                   | Not bounded by default                       |
| **DelayQueue**                      | Delay-based ordering           | ✔           | ❌            | Priority queue internally    | ✔         | Weakly consistent | Task scheduling                         | Elements must implement Delayed              |
| **SynchronousQueue**                | Direct handoff                 | ✔           | ❌            | No storage                   | ✔         | Weakly consistent | Thread handoff, executors               | Queue size always zero                       |
| **Deque (ArrayDeque / LinkedList)** | FIFO or LIFO                   | Depends     | Depends      | Array or linked list         | ❌         | Fail-fast         | Stack or queue replacement              | Deque preferred over Stack                   |

```

```text
Queue
 ├── Deque
 │     ├── ArrayDeque
 │     └── LinkedList
 ├── PriorityQueue
 └── BlockingQueue
       ├── ArrayBlockingQueue
       ├── LinkedBlockingQueue
       ├── PriorityBlockingQueue
       ├── DelayQueue
       └── SynchronousQueue

```

```text

| Collection                | Internal DS    | Lock Type   | Blocking | Thread-safe | Best For                  | Cannot Do       |
| ------------------------- | -------------- | ----------- | -------- | ----------- | ------------------------- | --------------- |
| **ArrayDeque**            | Circular array | none        | ❌       | ❌          | fastest single thread     | not thread-safe |
| **LinkedList**            | Doubly list    | none        | ❌       | ❌          | simple queue              | slow            |
| **ArrayBlockingQueue**    | Circular array | single lock | ✔        | ✔           | bounded producer-consumer | contention      |
| **LinkedBlockingQueue**   | Linked nodes   | two locks   | ✔        | ✔           | thread pools              | more memory     |
| **PriorityBlockingQueue** | Heap           | single lock | ✔        | ✔           | priority tasks            | not FIFO        |
| **DelayQueue**            | Heap + time    | single lock | ✔        | ✔           | scheduling                | time-based only |
| **ConcurrentLinkedQueue** | Linked list    | CAS         | ❌       | ✔           | high throughput           | no blocking     |
| **ConcurrentLinkedDeque** | Doubly list    | CAS         | ❌       | ✔           | work stealing             | no indexing     |

```
</details>


<details>
<summary>Map Comparison</summary>

```text
| Implementation                  | Order Maintained                     | Sorted                   | Null Keys / Values             | Thread Safe | Internal Structure                                 | Iterator Type     | When to Use                                    | Interview Traps / Notes                                       |
| ------------------------------- | -----------------------------------  | ------------------------ | ------------------------------ | ----------- | -------------------------------------------------- | ----------------- | ---------------------------------------------- | ------------------------------------------------------------- |
| **HashMap**                     | ❌ No order                          | ❌                        | ✔ 1 null key, many null values | ❌           | Hash table (array + buckets, tree after threshold) | Fail-fast         | General-purpose map                            | Order may appear sorted for small integers but not guaranteed |
| **LinkedHashMap**               | ✔ Insertion order (or access order)  | ❌                        | ✔ 1 null key                   | ❌           | HashMap + Doubly Linked List                       | Fail-fast         | Cache, LRU implementations                     | Can maintain access order using constructor                   |
| **TreeMap**                     | ❌ Insertion order                   | ✔ Sorted                 | ❌ No null keys                 | ❌           | Red-Black Tree                                     | Fail-fast         | Sorted keys, range queries                     | Sorting based on compareTo / Comparator                       |
| **Hashtable** (legacy)          | ❌ No order                          | ❌                        | ❌ No null key or value         | ✔           | Hash table                                         | Fail-fast         | Legacy code only                               | Entire map synchronized → slow                                |
| **Collections.synchronizedMap** | Depends on backing map               | Depends                  | Depends                        | ✔           | Wrapper                                            | Fail-fast         | Quick thread safety                            | Iteration must be manually synchronized                       |
| **ConcurrentHashMap**           | ❌ No order                          | ❌                        | ❌ No null key/value            | ✔           | Bucket array + CAS + fine-grained locking          | Weakly consistent | High concurrency systems                       | Reads mostly lock-free                                        |
| **WeakHashMap**                 | ❌ No order                          | ❌                        | ✔ 1 null key                   | ❌           | Hash table + weak references                       | Fail-fast         | Caches, memory-sensitive maps                  | Keys removed automatically by GC                              |
  ((week key reference,use objs only -> if key ==null gc removes it, dont use ->string pool X , interger caches -127 to 127 , strong refrence)  ) 
| **IdentityHashMap**             | ❌ No order                          | ❌                        | ✔ null allowed                 | ❌           | Hash table (reference equality)                    | Fail-fast         | Special cases using `==` instead of `equals()` | Very common interview trap                                    |
  (use == instead of .equals
| **EnumMap**                     | ✔ Natural order of enum              | ✔ Sorted by enum ordinal | ❌ Null keys                    | ❌           | Array indexed by enum ordinal                      | Fail-fast         | Best performance for enum keys                 | Faster than HashMap                                           |
   (keys used as enum)
| **Map.of() (Java 9+)**          | Preserves insertion order            | ❌                        | ❌ No nulls                     | ✔ Immutable | Compact immutable structure                        | Immutable         | Constants, configs                             | Immutable vs unmodifiable confusion                           |
| **Collections.unmodifiableMap** | Depends on backing map               | Depends                  | Depends                        | Depends     | Wrapper                                            | Fail-fast         | Read-only view                                 | Underlying map can still change                               |

```

```text
| Collection            | Internal DS            | Lock Type          | Thread-safe  | Best For         | Cannot Do |
| --------------------- | ---------------------- | ------------------ | -----------  | ---------------- | --------- |
| **HashMap**           | hash table + tree bins | none               | ❌           | general use      | not safe  |
| **LinkedHashMap**     | hash + linked list     | none               | ❌           | ordered/LRU      | slower    |
| **TreeMap**           | Red-Black tree         | none               | ❌           | sorted keys      | slower    |
| **Hashtable**         | hash                   | synchronized(this) | ✔            | legacy           | slow      |
| **ConcurrentHashMap** | hash + CAS + buckets   | fine-grained/CAS   | ✔            | high concurrency | no nulls  |

```
</details>


<details>
<summary>algo comparison</summary>

```text
| DS/Algo        | Used By                     | Why         |
| -------------- | --------------------------- | ----------- |
| Dynamic Array  | ArrayList, ArrayDeque       | fast index  |
| Linked List    | LinkedList, Blocking queues | fast insert |
| Hashing        | HashMap/Set                 | O(1) lookup |
| Binary Heap    | PriorityQueue, DelayQueue   | priority    |
| Red-Black Tree | TreeMap/TreeSet             | sorted      |
| Locks          | Blocking queues             | safety      |
| CAS            | Concurrent collections      | scalability |

```
</details>

<details>
<summary>lock comparison</summary>

```text
No Lock        → ArrayList, HashMap
Single Lock    → Vector, ArrayBlockingQueue
Two Locks      → LinkedBlockingQueue
CAS (lock-free)→ ConcurrentHashMap, ConcurrentLinkedQueue
Copy-on-write  → COW lists/sets


```
</details>

<details>
<summary>complexity comparison</summary>

```text
Hash → O(1)
Array → O(1) read
Linked → O(1) insert
Heap → O(log n)
Tree → O(log n)

```


```
</details>



