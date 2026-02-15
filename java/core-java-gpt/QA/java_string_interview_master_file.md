# Java String Interview Master File (7+ Years Experience)

---

## 📌 PART 1: CORE + TRICKY QUESTIONS

### 🔹 String Internals & JVM (Advanced)

1. Why is `String` immutable in Java? Explain beyond textbook reasons.
2. What exact problems would arise if `String` were mutable?
3. How is `String` stored internally in Java 8 vs Java 11+?
4. What are Compact Strings and how do they save memory?
5. Where are String literals stored in Java 6, 7+, and Java 17?
6. What is the String Constant Pool (SCP)?
7. Why was SCP moved from PermGen to Heap?
8. When does a String enter the SCP?
9. Explain memory allocation for:

   ```java
   String s = new String("abc");
   ```
10. How many objects are created?

---

### 🔹 Tricky & Edge Case Questions

11. Will this be true? Why?

```java
"a" + "b" == "ab"
```

12. Will this be true?

```java
String a = "a";
String b = a + "b";
b == "ab";
```

13. Difference between `equals()`, `==`, and `Objects.equals()`?
14. Why does `String` override `hashCode()`?
15. When is `hashCode()` calculated for a String?
16. Is cached `hashCode()` thread-safe?
17. What happens if hashCode was not cached?
18. Why are Strings safe HashMap keys?
19. What happens if a mutable object is used as a HashMap key?
20. Can String immutability be broken using reflection?

---

### 🔹 Concurrency & Security

21. Is String thread-safe? Explain practically.
22. How does immutability help concurrency?
23. Explain TOCTOU attack and how String prevents it.
24. Why are Strings used for file paths and URLs?
25. Why should passwords not be stored as Strings?
26. Does `static String` break immutability?
27. Can two threads modify the same String object?
28. How does String behave in multi-threaded HashMap usage?

---

### 🔹 Performance & Memory

29. Why is String immutable but StringBuilder mutable?
30. Difference between StringBuilder and StringBuffer?
31. Which one should be used in multi-threaded apps?
32. Why is String concatenation slow in loops?
33. How does compiler optimize String concatenation?
34. What happens internally for:

```java
s = s + "x";
```

35. Substring behavior in Java 6 vs Java 8+?
36. Why was substring implementation changed?
37. Can Strings cause memory leaks?
38. How does excessive interning affect memory?
39. Are interned Strings GC eligible?
40. How does GC treat String Pool?

---

## 📌 PART 2: TRICKY CODING QUESTIONS (INTERVIEW FAVORITES)

### 🔹 Coding Problems (Concept + Code)

41. Reverse a String without using `StringBuilder`.
42. Reverse words in a sentence.
43. Check if two Strings are anagrams.
44. Find first non-repeating character.
45. Find duplicate characters in a String.
46. Count character occurrences efficiently.
47. Check if String is palindrome (ignore case & spaces).
48. Find longest substring without repeating characters.
49. Find longest common prefix.
50. String compression (aabccccc → a2b1c5).

---

### 🔹 Advanced / Real-World Coding

51. Why is this code slow?

```java
String s = "";
for(int i=0;i<10000;i++){
  s += i;
}
```

52. Fix the above code.
53. Implement custom `equals()` for immutable class.
54. Implement custom `hashCode()`.
55. Convert String to int without `Integer.parseInt()`.
56. Print all permutations of a String.
57. Check if rotation of String exists.
58. Remove duplicate characters while preserving order.
59. Find maximum occurring character.
60. Split String without using `split()`.

---

## 📌 PART 3: ANSWERS & EXPLANATIONS (ALL QUESTIONS)

---

### 1. Why is `String` immutable in Java?

Immutability provides:

* **Security**: prevents tampering with validated inputs (paths, URLs, ClassLoader).
* **Thread-safety**: no synchronization needed.
* **Performance**: cached hashCode, JVM optimizations.
* **Memory efficiency**: enables String Pool reuse.

---

### 2. What problems if String were mutable?

* HashMap keys break
* Security vulnerabilities (TOCTOU attacks)
* Thread-safety issues
* String Pool becomes unsafe

---

### 3. Internal storage (Java 8 vs 11+)

* Java 8: `char[] value`
* Java 11+: `byte[] value` + `coder` (LATIN1 / UTF16)

---

### 4. Compact Strings

Uses 1 byte per character when possible → ~50% memory saving.

---

### 5. Where literals stored?

* Java 6: PermGen
* Java 7+: Heap
* Java 17: Heap (GC-managed)

---

### 6. String Constant Pool

Special heap area storing unique String literals.

---

### 7. Why SCP moved to Heap?

Avoid PermGen OOM, allow GC, dynamic resizing.

---

### 8. When does String enter SCP?

* At class loading (literals)
* Via `intern()`

---

### 9–10. `new String("abc")`

* Creates 2 objects if literal not present
* 1 in SCP, 1 in Heap

---

### 11. "a"+"b"=="ab"

True – compile-time constant folding.

---

### 12. Runtime concat example

False – happens via StringBuilder at runtime.

---

### 13. equals vs == vs Objects.equals

* `==`: reference
* `equals`: content
* `Objects.equals`: null-safe

---

### 14. Why override hashCode?

To ensure content-based hashing.

---

### 15. When hashCode calculated?

Lazy – first call, then cached.

---

### 16. Is cached hashCode thread-safe?

Yes – value never changes.

---

### 17. If not cached?

Repeated computation → performance hit.

---

### 18. Why String safe as HashMap key?

Immutability + stable hashCode.

---

### 19. Mutable key issue

Hash changes → entry becomes unreachable.

---

### 20. Reflection breaking immutability?

Possible pre-Java 12, unsafe and restricted now.

---

### 21. Is String thread-safe?

Yes – immutable objects are inherently thread-safe.

---

### 22. Concurrency benefit

No locks, no race conditions.

---

### 23. TOCTOU prevention

Data cannot change after validation.

---

### 24. Strings in paths & URLs

Guarantees consistency after security checks.

---

### 25. Passwords as Strings?

Bad – immutable, stays in memory longer.

---

### 26. Static String immutability

Static affects lifetime, not mutability.

---

### 27. Two threads modifying String?

They create new objects; original unchanged.

---

### 28. String in concurrent HashMap usage

Safe due to immutability.

---

### 29. Why StringBuilder mutable?

Efficient in-place modification.

---

### 30. StringBuilder vs StringBuffer

Buffer is synchronized; Builder is not.

---

### 31. Which to use when?

* Single-thread: StringBuilder
* Multi-thread: StringBuffer or external sync

---

### 32. Why concat slow in loops?

Creates new object each iteration (O(n²)).

---

### 33. Compiler optimization

Uses constant folding and StringBuilder.

---

### 34. `s = s + "x"` internals

Creates new StringBuilder, new String.

---

### 35. Substring Java 6 vs 8

* Java 6: shared char[] (leak risk)
* Java 8+: copies array

---

### 36. Why substring changed?

To prevent memory leaks.

---

### 37. Can Strings cause leaks?

Yes – long-lived references, interning misuse.

---

### 38. Excessive interning impact

Heap pressure, GC overhead.

---

### 39. Are interned Strings GC’d?

Yes (since Java 7).

---

### 40. GC & String Pool

Managed like normal heap objects.

---

### 41. Reverse String (no StringBuilder)

Uses char array swap.
two pointers
```java
 String str= "kodepaka-vivek";
        char[] ch = str.toCharArray();
        int start = 0;
        int end = str.length()-1;

        while(start<end){
            char temp = ch[start];
            ch[start] = ch[end];
            ch[end] = temp;

            start++;
            end--;
        }
        System.out.println(new String(ch));
```

---

### 42. Reverse words

Split + reverse or manual scan.

```java
public static void main(String[] args) {
        String str ="My name is vivek kodepaka";

        char[] ch = str.toCharArray();
        int start=0;
        for(int i=0;i<=ch.length;i++){
            if((i==ch.length)||(ch[i]==' ') ){
                reverseString(ch,start,i-1);
                start=i+1;
            }
        }
        System.out.println(new String(ch));
    }
```

---

### 43. Anagram check

Frequency map or sorting.

---

### 44. First non-repeating char

LinkedHashMap or array count.

---

### 45. Duplicate characters

Frequency count.

---

### 46. Character count

int[256] or Map.

---

### 47. Palindrome check

Two-pointer technique.

---

### 48. Longest substring without repeat

Sliding window + HashSet/Map.

---

### 49. Longest common prefix

Horizontal scanning.

---

### 50. String compression

Count consecutive characters.

---

### 51. Why loop concat slow?

Immutability → many temporary objects.

---

### 52. Fix

Use StringBuilder.

---

### 53. Custom equals

Compare fields, check type & null.

---

### 54. Custom hashCode

Use prime multiplication.

---

### 55. String to int

Manual digit parsing.

---

### 56. Permutations

Recursion + swapping.

---

### 57. Rotation check

(s+s).contains(rot).

---

### 58. Remove duplicates (order preserved)

LinkedHashSet.

---

### 59. Max occurring char

Frequency array.

---

### 60. Split without split()

Manual parsing using loop.

---

✅ END OF COMPLETE ANSWERS
