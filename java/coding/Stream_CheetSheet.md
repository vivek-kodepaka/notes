# 🚀 JAVA STREAM + COLLECTORS -- ULTIMATE INTERVIEW REVISION SHEET  

============================================================  
🧠 MASTER FLOW (REMEMBER FOREVER)  
============================================================  

SOURCE → INTERMEDIATE → TERMINAL → COLLECTORS  

Create → Transform → Finish → Aggregate  

============================================================  
1️⃣ STREAM SOURCE OPERATIONS  
============================================================  

```textmate


| Operation         | What it Does      | Usage                             |
| ----------------- | ----------------- | --------------------------------- |
| stream()          | Sequential stream | `list.stream()`                   |
| parallelStream()  | Parallel stream   | `list.parallelStream()`           |
| Stream.of()       | From values       | `Stream.of(1,2,3)`                |
| Arrays.stream()   | From array        | `Arrays.stream(arr)`              |
| Stream.builder()  | Manual build      | `Stream.builder().add(1).build()` |
| Stream.generate() | Infinite supplier | `Stream.generate(() -> 1)`        |
| Stream.iterate()  | Infinite sequence | `Stream.iterate(1, n -> n+1)`     |
| Stream.empty()    | Empty stream      | `Stream.empty()`                  |
```
============================================================  
🔹 2️⃣ INTERMEDIATE OPERATIONS (LAZY)  
============================================================  

🧠 Pattern: FILTER → MAP → FLATMAP → SORT → PEEK → LIMIT

```textmate
| Operation           | Purpose                  | Usage                              |
|--------------------|--------------------------|------------------------------------|
| filter()           | Conditional selection     | `filter(x -> x > 5)`              |
| distinct()         | Remove duplicates         | `distinct()`                      |
| map()              | Transform data            | `map(x -> x * 2)`                 |
| flatMap()          | Flatten nested            | `flatMap(List::stream)`           |
| sorted()           | Natural sort              | `sorted()`                        |
| sorted(Comparator) | Custom sort               | `sorted(Comparator.reverseOrder())` |
| peek()             | Debugging                 | `peek(System.out::println)`       |
| limit()            | Limit size                | `limit(5)`                        |
| skip()             | Skip elements             | `skip(2)`                         |
| takeWhile()        | Take while true           | `takeWhile(x -> x < 10)`          |
| dropWhile()        | Drop while true           | `dropWhile(x -> x < 10)`          |
| dropWhile()        | Drop while true           |  `dropWhile(x->x<10)`             |
```


============================================================  
🔹 3️⃣ TERMINAL OPERATIONS  
============================================================  

🧠 Pattern: COLLECT → COUNT → MATCH → FIND → REDUCE → LOOP

```textmate
| Operation         | Purpose                | Usage                                   |
|------------------|------------------------|-----------------------------------------|
| collect()        | Convert to collection  | `collect(Collectors.toList())`         |
| toArray()        | Convert to array       | `toArray()`                             |
| count()          | Count elements         | `count()`                               |
| anyMatch()       | Any matches            | `anyMatch(x -> x > 5)`                  |
| allMatch()       | All match              | `allMatch(x -> x > 5)`                  |
| noneMatch()      | None match             | `noneMatch(x -> x > 5)`                 |
| findFirst()      | First element          | `findFirst()`                           |
| findAny()        | Any element            | `findAny()`                             |
| reduce()         | Aggregate              | `reduce(0, Integer::sum)`              |
| min()            | Minimum                | `min(Integer::compareTo)`              |
| max()            | Maximum                | `max(Integer::compareTo)`              |
| forEach()        | Iterate                | `forEach(System.out::println)`         |
| forEachOrdered() | Ordered iteration      | `forEachOrdered(System.out::println)`  |
```

============================================================  
🔹 4️⃣ COLLECTORS (MOST IMPORTANT FOR INTERVIEWS)  
============================================================  

🧠 Pattern: GROUP → PARTITION → MAP → REDUCE → SUM → JOIN → MAX/MIN  
---------------- BASIC ----------------  


```textmate
| Collector   | Purpose            | Usage                              |
|-------------|--------------------|------------------------------------|
| toList()    | Collect list       | `toList()`                         |
| toSet()     | Collect set        | `toSet()`                          |
| toMap()     | Key-value mapping  | `toMap(k -> k.getId(), v -> v)`    |
| counting()  | Count elements     | `counting()`                       |
| joining()   | Join strings       | `joining(",")`                     |
```

---------------- GROUPING ----------------  

```textmate
| Collector                     | Usage                                                                 |
|--------------------------------|----------------------------------------------------------------------|
| groupingBy()                  | `groupingBy(Employee::getDept)`                                      |
| groupingBy + counting         | `groupingBy(Employee::getDept, counting())`                          |
| groupingBy + summingInt       | `groupingBy(Employee::getDept, summingInt(Employee::getSalary))`     |
| groupingBy + mapping          | `groupingBy(Employee::getDept, mapping(Employee::getName, toList()))`|
| groupingByConcurrent()        | Parallel grouping                                                    |
```


---------------- PARTITIONING ----------------  

```textmate
| Collector                 | Usage                                      |
|--------------------------|--------------------------------------------|
| partitioningBy()         | `partitioningBy(x -> x > 10)`              |
| partitioningBy + counting| `partitioningBy(x -> x > 10, counting())`  |
```

---------------- SUM / AVG / STATS ----------------
```textmate
| Collector        | Usage                                      |
|------------------|--------------------------------------------|
| summingInt()     | `summingInt(Employee::getSalary)`          |
| averagingInt()   | `averagingInt(Employee::getSalary)`        |
| summarizingInt() | `summarizingInt(Employee::getSalary)`      |
```
---------------- MAX / MIN ----------------  

```textmate
| Collector | Usage                                                       |
|-----------|-------------------------------------------------------------|
| maxBy()   | `maxBy(Comparator.comparing(Employee::getSalary))`         |
| minBy()   | `minBy(Comparator.comparing(Employee::getSalary))`         |
```

---------------- ADVANCED ----------------

```textmate
| Collector            | Usage                                                                 |
|---------------------|-----------------------------------------------------------------------|
| reducing()          | `reducing(Integer::sum)`                                              |
| collectingAndThen() | `collectingAndThen(toList(), Collections::unmodifiableList)`         |
| flatMapping()       | Nested grouping flatten                                               |
| filtering()         | Filter inside grouping                                                |
```

============================================================  
🔥 FINAL MEMORY CODE  
============================================================

F F M F S P L → C C M F R L → G P M R S J M  

Filter → Map → Flatten → Sort → Limit  
Collect → Count → Match → Find → Reduce → Loop  
Group → Partition → Map → Reduce → Sum → Join → Max/Min  

============================================================  
🎯INTERVIEW STRATEGY  
============================================================  

- 80% questions = groupingBy + mapping + reducing\
- Salary problems = summarizingInt\
- Frequency problems = groupingBy + counting\
- Duplicate handling = toMap with merge function\
- Boolean split = partitioningBy\
- String problems = joining

============================================================  
END OF ULTIMATE REVISION SHEET  
============================================================  
