# Coding Questions Practice Sheet (With Expandable Answers)

------------------------------------------------------------------------

# 🔹 Basic String Questions

## 1. Reverse a String

<details>
<summary>answer</summary>

```java
//app1

String str = "kodepaka";

String reversed = new StringBuilder(str)
        .reverse()
        .toString();
```

```java
String str = "kodepaka";
char[] arr = str.toCharArray();

int start = 0;
int end = arr.length - 1;

while (start < end) {
    char temp = arr[start];
    arr[start] = arr[end];
    arr[end] = temp;

    start++;
    end--;
}

String reversed = new String(arr);
System.out.println(reversed);

```

```java
String reversed = 
    IntStream.range(0, str.length())
             .mapToObj(i -> str.charAt(str.length() - 1 - i))
             .map(String::valueOf)
             .collect(Collectors.joining());

```
</details>


## 2. Reverse a String 
<details>
<summary>answer</summary>

```java
String str = "madam";

String reversed = new StringBuilder(str)
        .reverse()
        .toString();

boolean isPalindrome = str.equals(reversed);

System.out.println(isPalindrome);

```
```java
String str = "madam";

int left = 0;
int right = str.length() - 1;

boolean isPalindrome = true;

while (left < right) {
    if (str.charAt(left) != str.charAt(right)) {
        isPalindrome = false;
        break;
    }
    left++;
    right--;
}

System.out.println(isPalindrome);

```

```java
public static boolean isPalindrome(String str) {
    if (str.length() <= 1) return true;

    if (str.charAt(0) != str.charAt(str.length() - 1))
        return false;

    return isPalindrome(str.substring(1, str.length() - 1));
}

```
```java
String str = "madam";

boolean isPalindrome = 
    IntStream.range(0, str.length() / 2)
             .allMatch(i -> 
                 str.charAt(i) == str.charAt(str.length() - 1 - i)
             );

System.out.println(isPalindrome);

```
</details>

## 3. Count vowels and consonants

<details>
<summary>answer</summary>

```java
String s = "kodepaka";
s = s.toLowerCase();

String vowels = "aeiou";

int vowelCount = 0;
int consonantCount = 0;

for (int i = 0; i < s.length(); i++) {
    char ch = s.charAt(i);

    if (Character.isLetter(ch)) {  //imp
        if (vowels.indexOf(ch) != -1) {
            vowelCount++;
        } else {
            consonantCount++;
        }
    }
}

System.out.println("Vowels: " + vowelCount);
System.out.println("Consonants: " + consonantCount);

```

```java
String s = "kodepaka";

long vowelCount = s.toLowerCase()
        .chars()
        .filter(c -> Character.isLetter(c))
        .filter(c -> "aeiou".indexOf(c) != -1)
        .count();

long consonantCount = s.toLowerCase()
        .chars()
        .filter(c -> Character.isLetter(c))
        .count() - vowelCount;

```
</details>

## 4. Find length of string without using length()
<details>
<summary>answer</summary>

```java
String s = "kodepaka";

int count = 0;

for (char c : s.toCharArray()) {
    count++;
}

System.out.println(count);

```

```java
String s = "kodepaka";

long count = s.chars().count();

System.out.println(count);

```

</details>

## Convert string to uppercase / lowercase
<details>
<summary>answer</summary>

```java
String s = "Kodepaka";

System.out.println(s.toUpperCase());
System.out.println(s.toLowerCase());

```
```java

'a' = 97
'A' = 65
Difference = 32

Uppercase = lowercase - 32
Lowercase = uppercase + 32

```
```java
String s = "kodepaka";

char[] arr = s.toCharArray();

for (int i = 0; i < arr.length; i++) {
    if (arr[i] >= 'a' && arr[i] <= 'z') {
        arr[i] = (char)(arr[i] - 32);
    }
}

System.out.println(new String(arr));

```
```java
char[] arr = s.toCharArray();

for (int i = 0; i < arr.length; i++) {
    arr[i] = Character.toUpperCase(arr[i]);
}

System.out.println(new String(arr));

```

```java
String upper = s.chars()
        .map(c -> Character.toUpperCase(c))
        .mapToObj(c -> String.valueOf((char)c))
        .collect(Collectors.joining());

System.out.println(upper);

```
</details>

## Count occurrence of a character
<details>
<summary>answer</summary>

```java
Map<Character, Integer> hs = new HashMap<>();

String s = "kodepaka";

for (int i = 0; i < s.length(); i++) {
    char ch = s.charAt(i);
    hs.put(ch, hs.getOrDefault(ch, 0) + 1);
}

System.out.println(hs);

```

```java
Map<Character, Long> freq = s.chars()
        .mapToObj(c -> (char) c)
        .collect(Collectors.groupingBy(
                c -> c,
                Collectors.counting()
        ));

System.out.println(freq);

```
</details>

## Remove whitespaces from string
<details>
<summary>answer</summary>

```java
String s = "ko de pa ka";

String result = s.replace(" ", "");

System.out.println(result);

```
```java
String s = "ko de\tpa  ka";

String result = s.replaceAll("\\s", "");

System.out.println(result);

```
```java
String s = "ko de pa ka";

StringBuilder sb = new StringBuilder();

for (int i = 0; i < s.length(); i++) {
    if (s.charAt(i) != ' ') {
        sb.append(s.charAt(i));
    }
}

System.out.println(sb.toString());

```
```java
String result = s.chars()
        .filter(c -> !Character.isWhitespace(c))
        .mapToObj(c -> String.valueOf((char) c))
        .collect(Collectors.joining());

System.out.println(result);

```
</details>

## Compare two strings without equals()
<details>
<summary>answer</summary>

```java
String s1 = "kodepaka";
String s2 = "kodepaka";

if (s1.length() != s2.length()) {
    System.out.println(false);
    return;
}

boolean isEqual = true;

for (int i = 0; i < s1.length(); i++) {
    if (s1.charAt(i) != s2.charAt(i)) {
        isEqual = false;
        break;
    }
}

System.out.println(isEqual);

```
</details>

## Find ASCII value of a character 
<details>
<summary>answer</summary>

```java
char c = 'A';

int ascii = (int) c;

System.out.println(ascii);

```
</details>

## Replace a character in string
<details>
<summary>answer</summary>

```java
String s = "kodepaka";

String result = s.replace('a', 'x');

System.out.println(result);

```
```java
String s = "kodepaka";

String result = s.replaceFirst("a", "x");

System.out.println(result);

```

```java
String s = "kodepaka";
char[] arr = s.toCharArray();

for (int i = 0; i < arr.length; i++) {
    if (arr[i] == 'a') {
        arr[i] = 'x';
    }
}

String result = new String(arr);
System.out.println(result);

```
```java
String s = "kodepaka";

String result = s.chars()
        .mapToObj(c -> 
            (char)c == 'a' ? "x" : String.valueOf((char)c))
        .collect(Collectors.joining());

System.out.println(result);

```
</details>

## Find duplicate characters in a string
<details>
<summary>answer</summary>

```textmate
String str = "programming";

String result =
    str.chars()
       .mapToObj(c -> (char) c)
       .collect(Collectors.groupingBy(
               c -> c,
               Collectors.counting()))
       .entrySet()
       .stream()
       .filter(e -> e.getValue() > 1)
       .map(e -> String.valueOf(e.getKey()))
       .collect(Collectors.joining(","));

System.out.println(result);
```
```textmate
import java.util.*;

public class Main {
    public static void main(String[] args) {
        String str = "programming";

        Map<Character, Integer> map = new HashMap<>();

        for (char c : str.toCharArray()) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }

        for (Map.Entry<Character, Integer> entry : map.entrySet()) {
            if (entry.getValue() > 1) {
                System.out.println(entry.getKey());
            }
        }
    }
}
```
</details>

## 12.  Find first non-repeating character
<details>
<summary>answer</summary>

```textmate
String str = "programming";

Character result =
    str.chars()
       .mapToObj(c -> (char) c)
       .collect(Collectors.groupingBy(
               c -> c,
               LinkedHashMap::new,
               Collectors.counting()))
       .entrySet()
       .stream()
       .filter(e -> e.getValue() == 1)
       .map(Map.Entry::getKey)
       .findFirst()
       .orElse(null);

System.out.println(result);
```

```textmate
String str = "programming";

Map<Character, Integer> map = new LinkedHashMap<>();

for (char c : str.toCharArray()) {
    map.put(c, map.getOrDefault(c, 0) + 1);
}

for (Map.Entry<Character, Integer> entry : map.entrySet()) {
    if (entry.getValue() == 1) {
        System.out.println(entry.getKey());
        break;
    }
}
```
</details>

## 13.  Check if two strings are anagrams
<details>
<summary>answer</summary>

```java
public static boolean isAnagram(String s1, String s2) {

    if (s1 == null || s2 == null) return false;

    if (s1.length() != s2.length()) return false;

    Map<Character, Integer> map = new HashMap<>();

    for (int i = 0; i < s1.length(); i++) {

        char c1 = s1.charAt(i);
        char c2 = s2.charAt(i);

        map.put(c1, map.getOrDefault(c1, 0) + 1);
        map.put(c2, map.getOrDefault(c2, 0) - 1);
    }

    for (int value : map.values()) {
        if (value != 0) return false;
    }

    return true;
}
```

```java
public static boolean isAnagram(String s1, String s2) {

    if (s1.length() != s2.length()) return false;

    int[] freq = new int[26];

    for (int i = 0; i < s1.length(); i++) {
        freq[s1.charAt(i) - 'a']++;
        freq[s2.charAt(i) - 'a']--;
    }

    for (int count : freq) {
        if (count != 0) return false;
    }

    return true;
}
```
```java
public static boolean isAnagram(String s1, String s2) {

    if (s1.length() != s2.length()) return false;

    Map<Character, Long> map1 =
            s1.chars()
              .mapToObj(c -> (char) c)
              .collect(Collectors.groupingBy(
                      c -> c,
                      Collectors.counting()));

    Map<Character, Long> map2 =
            s2.chars()
              .mapToObj(c -> (char) c)
              .collect(Collectors.groupingBy(
                      c -> c,
                      Collectors.counting()));

    return map1.equals(map2);
}
```
</details>


## Count words in a string
<details>
<summary>answer</summary>

```java
public static int countWords(String str) {
    if (str == null || str.trim().isEmpty()) return 0;

    String[] words = str.trim().split("\\s+");
    return words.length;
}

/* Why \\s+?

        \\s → whitespace

+ → one or more  */
```

```java
public static int countWords(String str) {

    if (str == null || str.trim().isEmpty()) return 0;

    int count = 0;
    boolean inWord = false;

    for (int i = 0; i < str.length(); i++) {

        if (!Character.isWhitespace(str.charAt(i))) {
            if (!inWord) {
                count++;
                inWord = true;
            }
        } else {
            inWord = false;
        }
    }

    return count;
}
```

```java
public static long countWords(String str) {

    if (str == null || str.trim().isEmpty()) return 0;

    return Arrays.stream(str.trim().split("\\s+"))
                 .count();
}
```


</details>


## Reverse each word in a sentence
<details>
<summary>answer</summary>

```java
public static String reverseEachWord(String str) {

    if (str == null || str.isEmpty()) return str;

    String[] words = str.split(" ");
    StringBuilder result = new StringBuilder();

    for (String word : words) {
        result.append(new StringBuilder(word).reverse().toString())
              .append(" ");
    }

    return result.toString().trim();
}
```

```java
public static String reverseEachWord(String str) {

    if (str == null || str.isEmpty()) return str;

    StringBuilder result = new StringBuilder();
    StringBuilder word = new StringBuilder();

    for (int i = 0; i < str.length(); i++) {

        char ch = str.charAt(i);

        if (ch != ' ') {
            word.append(ch);
        } else {
            result.append(word.reverse()).append(" ");
            word.setLength(0); // clear
        }
    }

    // append last word
    result.append(word.reverse());

    return result.toString();
}
```

```java
public static String reverseEachWord(String str) {

    return Arrays.stream(str.split(" "))
                 .map(word -> new StringBuilder(word).reverse().toString())
                 .collect(Collectors.joining(" "));
}
```
</details>

## Find frequency of each character
<details>
<summary>answer</summary>

```java
public static void frequency(String str) {

    Map<Character, Integer> map = new HashMap<>();

    for (char c : str.toCharArray()) {
        map.put(c, map.getOrDefault(c, 0) + 1);
    }

    for (Map.Entry<Character, Integer> entry : map.entrySet()) {
        System.out.println(entry.getKey() + " -> " + entry.getValue());
    }
}
```
```java
public static void frequency(String str) {

    Map<Character, Long> map =
            str.chars()
               .mapToObj(c -> (char) c)
               .collect(Collectors.groupingBy(
                       c -> c,
                       Collectors.counting()));

    map.forEach((k, v) ->
        System.out.println(k + " -> " + v));
}
```
</details>

##  Remove duplicate characters
<details>
<summary>answer</summary>

```java
String result =
    str.chars()
       .mapToObj(c -> (char) c)
       .distinct()
       .map(String::valueOf)
       .collect(Collectors.joining());
```

```java
public static String removeDuplicates(String str) {

    if (str == null || str.isEmpty()) return str;

    Set<Character> seen = new HashSet<>();
    StringBuilder result = new StringBuilder();

    for (char c : str.toCharArray()) {
        if (!seen.contains(c)) {
            seen.add(c);
            result.append(c);
        }
    }

    return result.toString();
}
```
</details>


##  Check if string contains only digits
<details>
<summary>answer</summary>

```java
public static boolean containsOnlyDigits(String str) {

    if (str == null || str.isEmpty()) return false;

    for (int i = 0; i < str.length(); i++) {
        if (!Character.isDigit(str.charAt(i))) {
            return false;
        }
    }

    return true;
}
```
</details>


## Find longest word in a sentence
<details>
<summary>answer</summary>

```java
public static String findLongestWord(String str) {

    if (str == null || str.trim().isEmpty()) return "";

    String[] words = str.trim().split("\\s+");

    String longest = "";

    for (String word : words) {
        if (word.length() > longest.length()) {
            longest = word;
        }
    }

    return longest;
}
```

```java
public static String findLongestWord(String str) {

    if (str == null || str.trim().isEmpty()) return "";

    return Arrays.stream(str.trim().split("\\s+"))
                 .max(Comparator.comparingInt(String::length))   //s -> s.length()
                 .orElse("");
}
```
</details>


## Find substring inside a string
<details>
<summary>answer</summary>

```java
int index = str.indexOf("gram");
if (index != -1) {
    System.out.println("Found at: " + index);
}
```

```java
boolean found = str.contains(sub);
```
</details>

## 
<details>
<summary>answer</summary>

</details>

## 
<details>
<summary>answer</summary>

</details>




