# Java 8 Streams -- 1 Page Rapid Revision Sheet

------------------------------------------------------------------------

## 🔹 Stream Structure

Source → Intermediate → Terminal

Example:

``` java
list.stream()
    .filter(x -> x > 5)
    .map(x -> x * 2)
    .collect(Collectors.toList());
```

------------------------------------------------------------------------

# 🔥 Most Used Intermediate Operations

  Operation    Purpose             Example
  ------------ ------------------- -----------------------------
  filter()     Remove elements     `.filter(x -> x % 2 == 0)`
  map()        Transform           `.map(String::toUpperCase)`
  sorted()     Sort                `.sorted()`
  distinct()   Remove duplicates   `.distinct()`
  limit()      First N             `.limit(3)`
  skip()       Skip N              `.skip(1)`

------------------------------------------------------------------------

# 🔥 Most Used Terminal Operations

  Operation             Purpose
  --------------------- -----------------------
  collect()             Convert to List/Map
  count()               Count elements
  reduce()              Combine elements
  max()/min()           Find max/min
  findFirst()           First element
  anyMatch()            Condition check
  summaryStatistics()   All stats in one pass

------------------------------------------------------------------------

# 🚀 Common Patterns

## ✅ Even Numbers

``` java
list.stream()
    .filter(x -> x % 2 == 0)
    .collect(Collectors.toList());
```

## ✅ Find Duplicates

``` java
Set<Integer> seen = new HashSet<>();
list.stream()
    .filter(x -> !seen.add(x))
    .collect(Collectors.toList());
```

## ✅ Frequency Count

``` java
list.stream()
    .collect(Collectors.groupingBy(
        Function.identity(),
        Collectors.counting()
    ));
```

## ✅ Second Highest

``` java
list.stream()
    .distinct()
    .sorted(Comparator.reverseOrder())
    .skip(1)
    .findFirst();
```

## ✅ Highest Salary

``` java
employees.stream()
    .max(Comparator.comparing(Employee::getSalary));
```

## ✅ Group By Field

``` java
employees.stream()
    .collect(Collectors.groupingBy(Employee::getDept));
```

------------------------------------------------------------------------

# 📦 Arrays Quick Tricks

``` java
Arrays.stream(arr).sum();
Arrays.stream(arr).max().orElse(0);
Arrays.stream(arr).distinct().count();
```

Second Highest:

``` java
Arrays.stream(arr)
      .distinct()
      .boxed()
      .sorted(Comparator.reverseOrder())
      .skip(1)
      .findFirst();
```

------------------------------------------------------------------------

# 🔤 Strings Quick Tricks

Convert to Char Stream:

``` java
input.chars().mapToObj(c -> (char) c);
```

First Non-Repeated:

``` java
input.chars()
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
     .findFirst();
```

------------------------------------------------------------------------

# 🧠 Interview Quick Concepts

✔ Streams are lazy\
✔ Intermediate ops don't run without terminal\
✔ map → 1-to-1\
✔ flatMap → 1-to-many\
✔ groupingBy → multiple keys\
✔ partitioningBy → boolean only\
✔ counting() returns Long\
✔ Primitive streams avoid boxing

------------------------------------------------------------------------

# 🎯 Remember

Most service interviews test: - filter + map - sorting - groupingBy -
duplicates - second highest - frequency count

Keep syntax clean. Avoid spelling mistakes.

------------------------------------------------------------------------

END OF RAPID REVISION SHEET
