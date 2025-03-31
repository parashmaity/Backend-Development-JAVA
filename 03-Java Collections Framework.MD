# Java Collections Framework

## 32. What is the Java Collections Framework?

The Java Collections Framework (JCF) is a set of interfaces and classes in the `java.util` package that provide a standard way to handle and manipulate groups of objects. It includes data structures such as List, Set, Queue, and Map.

### Key Components of JCF:
- **Interfaces**: Collection, List, Set, Queue, Map
- **Implementations**: ArrayList, LinkedList, HashSet, TreeSet, HashMap, LinkedHashMap, PriorityQueue
- **Utility Classes**: Collections, Arrays

---

## 33. What are the differences between List, Set, and Map?

| Feature       | List                    | Set                      | Map                               |
|--------------|-------------------------|--------------------------|----------------------------------|
| Duplicates   | Allows duplicates       | No duplicates            | No duplicate keys (values can be duplicate) |
| Ordering     | Maintains insertion order | No guaranteed order (except LinkedHashSet & TreeSet) | No order guarantee (except LinkedHashMap & TreeMap) |
| Null values  | Allows multiple null values | Allows single null value | Allows one null key, multiple null values |
| Implementation | ArrayList, LinkedList  | HashSet, TreeSet, LinkedHashSet | HashMap, TreeMap, LinkedHashMap, ConcurrentHashMap |

---

## 34. Explain the difference between ArrayList and LinkedList.

| Feature           | ArrayList               | LinkedList               |
|------------------|------------------------|-------------------------|
| Implementation   | Uses a dynamic array   | Uses a doubly linked list |
| Insertion/Deletion | Slow (shifting elements needed) | Fast (just update pointers) |
| Random Access    | Fast (O(1))             | Slow (O(n))              |
| Memory Usage     | Less (only array)       | More (extra pointers for each node) |
| Best for         | Read-heavy applications | Write-heavy applications |

Use **ArrayList** when you need fast random access, and **LinkedList** when you need frequent insertions and deletions.

---

## 35. What is the difference between HashSet and TreeSet?

| Feature      | HashSet                  | TreeSet                 |
|-------------|-------------------------|------------------------|
| Ordering    | No ordering              | Sorted in natural order |
| Performance | O(1) for add, remove, contains | O(log n) for add, remove, contains |
| Null values | Allows one null value    | Does not allow null values |
| Best for    | Fast lookups             | Ordered and sorted elements |

Use **HashSet** for fast lookups and **TreeSet** when sorting is required.

---

## 36. How does a HashMap work internally?

A **HashMap** in Java stores key-value pairs using an array of buckets and a hash function:

1. **Hashing**: The key is hashed using `hashCode()`, and the index is calculated as:
   ```java
   index = hash(key) % array.length;
   ```
2. **Collision Handling**: If multiple keys hash to the same index, they form a linked list (Java 7) or a balanced tree (Java 8+).
3. **Resizing**: When the load factor (default 0.75) is exceeded, the map resizes and rehashes elements.
4. **Performance**: O(1) in average case but O(n) in worst case (if too many collisions occur).

---

## 37. What are weak references, and how do WeakHashMaps work?

- **Weak references** allow objects to be garbage collected when they are not strongly referenced.
- **WeakHashMap** is a special Map where keys are weak references.
- When the GC runs, it removes the entries whose keys are no longer referenced.

### Example:

```java
import java.util.WeakHashMap;

public class WeakHashMapExample {
    public static void main(String[] args) {
        WeakHashMap<Object, String> map = new WeakHashMap<>();
        Object key = new Object();
        map.put(key, "Value");

        key = null; // Remove strong reference
        System.gc(); // Trigger GC

        System.out.println(map); // May print {} if GC has removed entry
    }
}
```

---

## 38. What is the difference between ConcurrentHashMap and HashMap?

| Feature           | HashMap                   | ConcurrentHashMap       |
|------------------|-------------------------|-------------------------|
| Thread-safety    | Not thread-safe         | Thread-safe              |
| Performance      | Fast (single-threaded)  | Optimized for concurrency |
| Locking Mechanism | Entire map is locked   | Only specific segments are locked (since Java 8, uses CAS) |
| Best for         | Single-threaded apps    | Multi-threaded apps      |

Use **ConcurrentHashMap** when multiple threads modify the map simultaneously.

---

## 39. Explain the difference between Vector and ArrayList.

| Feature          | Vector                    | ArrayList               |
|-----------------|-------------------------|-------------------------|
| Synchronization | Thread-safe (synchronized methods) | Not synchronized |
| Performance     | Slower (synchronization overhead) | Faster |
| Growth         | Doubles in size          | Increases by 50% |

Use **ArrayList** in single-threaded applications and **Vector** when thread safety is needed.

---

## 41. What is fail-fast and fail-safe in Java Collections?

- **Fail-fast**: Throws `ConcurrentModificationException` if collection is modified while iterating.  
  🔹 Examples: `ArrayList`, `HashSet`, `HashMap`.

```java
List<Integer> list = new ArrayList<>(List.of(1, 2, 3));
for (Integer num : list) {
    list.add(4); // Throws ConcurrentModificationException
}
```

- **Fail-safe**: Uses a copy (`CopyOnWriteArrayList` or `ConcurrentHashMap`) to allow modifications without exceptions.  
  🔹 Examples: `CopyOnWriteArrayList`, `ConcurrentHashMap`.

```java
CopyOnWriteArrayList<Integer> list = new CopyOnWriteArrayList<>(List.of(1, 2, 3));
for (Integer num : list) {
    list.add(4); // No exception
}
```

---

## 42. How does a PriorityQueue work?

`PriorityQueue<E>` is a **min-heap** by default (smallest element at the top).

- **Insertion**: O(log n)
- **Removal (polling)**: O(log n)

### Example:

```java
import java.util.PriorityQueue;

public class PQExample {
    public static void main(String[] args) {
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        pq.add(30);
        pq.add(20);
        pq.add(10);

        System.out.println(pq.poll()); // Output: 10 (Smallest element)
    }
}
```

🔹 **Custom Comparator for Max Heap**:

```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);
```

