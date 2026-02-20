# Java 8 Streams -- Complete Interview Notes

(With Collections, Arrays, and Strings)

------------------------------------------------------------------------

# PART 1 -- Stream Basics

## Stream Pipeline

Source → Intermediate Operations → Terminal Operation

Example:

``` java
list.stream()
    .filter(x -> x > 5)
    .map(x -> x * 2)
    .collect(Collectors.toList());
```

------------------------------------------------------------------------

# PART 2 -- Important Stream Operations

## filter()

Removes elements based on condition.

``` java
.filter(x -> x % 2 == 0)
```

## map()

Transforms elements.

``` java
.map(String::toUpperCase)
```

## sorted()

``` java
.sorted()
.sorted(Comparator.reverseOrder())
.sorted(Comparator.comparing(Employee::getSalary))
```

## distinct()

Removes duplicates.

## limit() / skip()

``` java
.limit(5)
.skip(1)
```

## reduce()

``` java
.reduce(0, Integer::sum)
.reduce(1, (a,b) -> a*b)
```

## max() / min()

``` java
.max(Comparator.comparing(Employee::getSalary))
```

## groupingBy()

``` java
Collectors.groupingBy(Employee::getDept)
```

## partitioningBy()

``` java
Collectors.partitioningBy(x -> x % 2 == 0)
```

## counting()

``` java
Collectors.counting()
```

## summingInt()

``` java
Collectors.summingInt(Integer::intValue)
```

## summaryStatistics()

``` java
IntSummaryStatistics stats = list.stream()
    .mapToInt(Integer::intValue)
    .summaryStatistics();
```

------------------------------------------------------------------------

# PART 3 -- Arrays with Streams

## Convert Array to Stream

``` java
int[] arr = {1,2,3,4};
Arrays.stream(arr)
      .filter(x -> x % 2 == 0)
      .forEach(System.out::println);
```

## Find Sum of Array

``` java
int sum = Arrays.stream(arr).sum();
```

## Find Max in Array

``` java
int max = Arrays.stream(arr).max().orElse(0);
```

## Find Second Highest in Array

``` java
int second = Arrays.stream(arr)
        .distinct()
        .boxed()   // IntStream  →  Stream<Integer>
        .sorted(Comparator.reverseOrder())
        .skip(1)
        .findFirst()
        .orElse(0);
```

------------------------------------------------------------------------

# PART 4 -- Strings with Streams

## Convert String to Character Stream

``` java
String input = "stress";

input.chars()
     .mapToObj(c -> (char) c)
     .forEach(System.out::println);
```

## First Non-Repeated Character

``` java
Character ch = input.chars()
        .mapToObj(c -> (char) c)
        .collect(Collectors.groupingBy(
            Function.identity(),
            LinkedHashMap::new,
            Collectors.counting()
        ))
        .entrySet()
        .stream()
        .filter(e -> e.getValue() == 1)
        .map(Map.Entry::getKey)
        .findFirst()
        .orElse(null);
```

## Reverse a String

``` java
String reversed = new StringBuilder(input)
        .reverse()
        .toString();
```

## Count Vowels

``` java
long count = input.chars()
        .mapToObj(c -> (char) c)
        .filter(c -> "aeiou".indexOf(c) != -1)
        .count();
```

## Find Duplicate Characters

``` java
Set<Character> seen = new HashSet<>();

List<Character> duplicates = input.chars()
        .mapToObj(c -> (char) c)
        .filter(c -> !seen.add(c))
        .collect(Collectors.toList());
```

------------------------------------------------------------------------

# PART 5 -- Most Asked Collection Questions

## Find Even Numbers

``` java
list.stream()
    .filter(x -> x % 2 == 0)
    .collect(Collectors.toList());
```

## Find Duplicates

``` java
Set<Integer> seen = new HashSet<>();

list.stream()
    .filter(x -> !seen.add(x))
    .collect(Collectors.toList());
```

## Count Frequency

``` java
list.stream()
    .collect(Collectors.groupingBy(
        Function.identity(),
        Collectors.counting()
    ));
```

## Second Highest Number

``` java
list.stream()
    .distinct()
    .sorted(Comparator.reverseOrder())
    .skip(1)
    .findFirst();
```

## Highest Salary Employee

``` java
employees.stream()
    .max(Comparator.comparing(Employee::getSalary));
```

## Group Employees by Department

``` java
employees.stream()
    .collect(Collectors.groupingBy(Employee::getDept));
```

------------------------------------------------------------------------

# PART 6 -- Interview Important Concepts

## Lazy Evaluation

Intermediate operations execute only after terminal operation.

## map vs flatMap

map → one-to-one\
flatMap → one-to-many

## groupingBy vs partitioningBy

groupingBy → multiple keys\
partitioningBy → boolean only

## Why mapToInt?

Avoid boxing/unboxing overhead.

## Why Optional?

Avoid NullPointerException.

------------------------------------------------------------------------

END OF NOTES
