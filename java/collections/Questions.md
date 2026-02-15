

### ArrayList

1. <details>
    <summary>What is ArrayList?</summary>
    
    ```text 
    ArrayList is a resizable array implementation of the List interface that allows:
     dynamic sizing
     index-based access
     duplicates
     null values
    ```
    </details>

2. <details>
    <summary>Difference between Array and ArrayList?</summary>

    ```text 
       | Feature     | Array                | ArrayList                |
       | ----------- | -------------------- | ------------------------ |
       | Size        | Fixed                | Dynamic                  |
       | Type        | primitives + objects | Objects only             |
       | Methods     | No                   | Many (add, remove, etc.) |
       | Performance | Slightly faster      | Slight overhead          |

    ```
    </details>

3.  <details>
    <summary>Default capacity of ArrayList?</summary>

    ```text 
       Java 8+ → 10 (when first element added)
       Before that → 10 initially
    ```
    </details>

4. <details>
    <summary>What is Amortized O(1)?</summary>

    ```text 
       Resizing occasionally costs O(n), but average cost becomes constant.
        when add elements --> when size is full
    ```
    </details>

5. <details>
   <summary>How does ArrayList grow internally?</summary>
   
   ```text
        newCapacity = oldCapacity + (oldCapacity >> 1); // 1.5x
        Create new array
        Copy old elements  --> Arrays.copy(ols,new)
        Replace reference
   ```
   </details>

6. <details>
   <summary>Is ArrayList thread-safe, how to make it</summary>
   
   ```textmate
      no
      options:
   
      1. Collections.synchronizedList()
      2. CopyOnWriteArrayList
   ```
   </details>
3.  --10
4. How to declare ArrayList?
5. Can ArrayList store null?
6. Time complexity of operations?
7. How does ArrayList grow internally?
8. Is ArrayList thread-safe? solutions
9. Difference between ArrayList and LinkedList






   
8. ```text
    Collections.synchronizedList()
     CopyOnWriteArrayList
     ```

9. ```text 
| Feature        | ArrayList | LinkedList |
| -------------- | --------- | ---------- |
| Access         | Fast O(1) | Slow O(n)  |
| Insert middle  | Slow      | Fast       |
| Memory         | Less      | More       |
| Cache friendly | Yes       | No         |

```




