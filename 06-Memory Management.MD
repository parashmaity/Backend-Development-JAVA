# Java Memory Management & Garbage Collection

## 53. How does Java manage memory?

Java memory management is based on heap and stack memory, where:
* **Heap Memory**: Stores objects and class metadata. It is shared among all threads.
* **Stack Memory**: Stores method call frames, local variables, and references to objects in the heap. It is thread-specific.

## 54. What are the different memory areas in JVM?

The JVM Memory Model is divided into multiple areas:

1. **Method Area (or MetaSpace from Java 8):**
    * Stores class metadata, method code, and static variables.
    * Uses native memory (not part of the heap) from Java 8 onwards.
2. **Heap Memory:**
    * Stores objects, instance variables, and JIT-optimized compiled code.
    * Divided into:
        * **Young Generation** (for short-lived objects, contains Eden & Survivor spaces)
        * **Old Generation (Tenured Space)** (for long-lived objects)
3. **Stack Memory:**
    * Stores method calls, local variables, and references to objects.
    * Each thread has its own stack.
4. **PC Register (Program Counter Register):**
    * Stores the address of the currently executing instruction for a thread.
5. **Native Method Stack:**
    * Stores references to native (C/C++) methods used by Java.

## 55. Explain heap memory vs. stack memory.

| Feature         | Heap Memory                 | Stack Memory                 |
|---------------|----------------------------|------------------------------|
| Stores        | Objects, class metadata    | Method calls, local variables |
| Scope        | Shared among all threads    | Thread-specific               |
| Lifetime     | Exists until garbage collected | Exists until the method finishes |
| Speed        | Slower (managed by GC)      | Faster (auto-allocated & deallocated) |
| Example      | `new Object()` creates an object in the heap | `int x = 10;` stored in the stack |

## 56. What is the role of the garbage collector in Java?

The Garbage Collector (GC) automatically reclaims memory by removing objects that are no longer reachable. It prevents memory leaks and `OutOfMemoryError`s.

### GC Process:
1. **Marking**: Identifies which objects are still in use.
2. **Sweeping**: Removes unreferenced objects.
3. **Compacting**: Rearranges memory to optimize allocation.

## 57. What are the different types of garbage collectors in Java?

Java provides several garbage collectors, selectable using JVM options:

1. **Serial GC (`-XX:+UseSerialGC`)**:
    * Uses a single thread for garbage collection.
    * Best for small applications or single-threaded environments.
2. **Parallel GC (`-XX:+UseParallelGC`)** (Default in Java 8):
    * Uses multiple threads for Young Gen collection.
    * Designed for high throughput.
3. **G1 (Garbage-First) GC (`-XX:+UseG1GC`)** (Default in Java 9+):
    * Divides the heap into regions and collects garbage incrementally.
    * Reduces stop-the-world pauses.
4. **ZGC (`-XX:+UseZGC`)**:
    * Low-latency, handles large heaps (up to terabytes).
    * Ideal for real-time applications.
5. **Shenandoah GC (`-XX:+UseShenandoahGC`)**:
    * Performs concurrent garbage collection.
    * Suitable for ultra-low-latency applications.

## 58. What is the difference between strong, weak, soft, and phantom references?

Java provides reference types to control object eligibility for garbage collection.

| Reference Type | Description | Garbage Collection Behavior |
|---------------|------------|----------------------------|
| **Strong Reference** | `Object obj = new Object();` | Never collected unless explicitly set to `null` |
| **Weak Reference** | `WeakReference<Object> wRef = new WeakReference<>(obj);` | Collected as soon as no strong references exist |
| **Soft Reference** | `SoftReference<Object> sRef = new SoftReference<>(obj);` | Collected only when memory is low |
| **Phantom Reference** | `PhantomReference<Object> pRef = new PhantomReference<>(obj, queue);` | Collected when finalized, used for resource cleanup |

## 59. What is memory leak in Java, and how can you prevent it?

A memory leak occurs when objects are no longer needed but still referenced, preventing garbage collection.

### Causes of Memory Leaks:
1. Unclosed resources (e.g., database connections, file streams).
2. Static references to large objects.
3. Memory-intensive caches without proper eviction strategies.
4. Inner classes holding references to outer class objects.
5. Listeners and event handlers not removed properly.

### How to prevent memory leaks?
* Use **try-with-resources** for closing resources.
* Remove unused listeners in GUI applications.
* Use **WeakReference** or **SoftReference** for caches.
* Use tools like **JVisualVM, Eclipse MAT, or YourKit** for memory profiling.

## 60. Explain the concept of reference counting in Java.

Java does not use reference counting for garbage collection (unlike Python). Instead, it uses **reachability analysis**, meaning:
* Objects are considered garbage if they are not reachable from **root references** (GC roots).
* This avoids **circular reference issues** common in reference counting.

## 61. How can you explicitly request garbage collection?

You can suggest garbage collection, but it is not guaranteed:

```java
System.gc(); // Suggests garbage collection
Runtime.getRuntime().gc(); // Alternative way
```

* JVM may ignore the request if it is unnecessary.

## 62. What are finalizers, and why are they discouraged?

Finalizers (`finalize()` method) were used to perform cleanup before an object is garbage collected.

### Why are finalizers discouraged?
1. **Unpredictable Execution**: GC timing is uncertain.
2. **Performance Overhead**: Slows down GC due to extra processing.
3. **Resource Leak Risk**: If GC does not run, resources remain open.

### Alternative to Finalizers:
Use **try-with-resources** and implement `AutoCloseable`:

```java
class MyResource implements AutoCloseable {
    @Override
    public void close() {
        System.out.println("Resource closed");
    }
}

try (MyResource res = new MyResource()) {
    // Use the resource
} // Automatically closed at the end of try-block
```

