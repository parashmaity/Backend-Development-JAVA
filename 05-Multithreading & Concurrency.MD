# Multithreading & Concurrency

## 43. What is multithreading in Java?

Multithreading is a Java feature that allows concurrent execution of two or more parts of a program to maximize CPU utilization. Each part is called a thread.

### Benefits of Multithreading:
- Efficient CPU utilization
- Improved performance for concurrent tasks
- Faster response time in applications
- Parallel execution of independent tasks

## 44. How do you create a thread in Java?

There are two ways to create a thread in Java:

### 1. By extending Thread class
```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread is running...");
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start(); // Starts the thread
    }
}
```

### 2. By implementing the Runnable interface
```java
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Thread is running...");
    }
}

public class Main {
    public static void main(String[] args) {
        Thread t1 = new Thread(new MyRunnable());
        t1.start();
    }
}
```
💡 **Best Practice:** Use `Runnable` when possible, as it allows extending other classes.

## 45. What is the difference between Runnable and Callable?

| Feature          | Runnable | Callable<T> |
|----------------|---------|------------|
| Return Type     | void    | T (generic) |
| Exception Handling | Cannot throw checked exceptions | Can throw checked exceptions |
| Method         | run()   | call() |
| Usage         | Used for simple tasks | Used for tasks that return results |

### Example of Callable:
```java
import java.util.concurrent.*;

class MyTask implements Callable<Integer> {
    public Integer call() throws Exception {
        return 10 + 20;
    }
}

public class Main {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        Future<Integer> future = executor.submit(new MyTask());
        System.out.println("Result: " + future.get()); // 30
        executor.shutdown();
    }
}
```

## 46. Explain the lifecycle of a thread in Java.

A thread goes through the following states:
1. **New** – Created but not started (`new Thread()`).
2. **Runnable** – Ready to run, waiting for CPU time.
3. **Blocked** – Waiting for a lock to be released.
4. **Waiting** – Indefinitely waiting (`wait()`).
5. **Timed Waiting** – Waiting for a specific time (`sleep()`, `join()`).
6. **Terminated** – Execution completed or aborted.

## 47. What is a daemon thread?

A daemon thread is a low-priority thread that runs in the background (e.g., Garbage Collector).

### Example:
```java
class MyThread extends Thread {
    public void run() {
        while (true) {
            System.out.println("Daemon running...");
        }
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.setDaemon(true);
        t.start();
        System.out.println("Main thread finished.");
    }
}
```
💡 If all non-daemon threads finish, daemon threads terminate automatically.

## 48. What are thread priorities in Java?

Thread priority determines which thread gets preference for execution.

- **Range:** 1 (`MIN_PRIORITY`) to 10 (`MAX_PRIORITY`), default is 5 (`NORM_PRIORITY`).
- **Methods:**
```java
thread.setPriority(Thread.MAX_PRIORITY);
int priority = thread.getPriority();
```

## 49. What is the synchronized keyword, and how does it work?

The `synchronized` keyword prevents multiple threads from accessing a critical section simultaneously, avoiding race conditions.

### Example:
```java
class SharedResource {
    synchronized void display() {
        System.out.println(Thread.currentThread().getName() + " is executing");
    }
}

class MyThread extends Thread {
    SharedResource obj;
    MyThread(SharedResource obj) { this.obj = obj; }

    public void run() { obj.display(); }
}

public class Main {
    public static void main(String[] args) {
        SharedResource obj = new SharedResource();
        Thread t1 = new MyThread(obj);
        Thread t2 = new MyThread(obj);
        t1.start();
        t2.start();
    }
}
```

💡 **`synchronized` can be used:**
- On methods (`synchronized void method() {}`)
- On blocks (`synchronized(obj) { }`)

## 50. What are deadlocks, and how do you avoid them?

A **deadlock** occurs when two threads are waiting for each other to release a lock.

### Example:
```java
class A {
    synchronized void methodA(B b) {
        System.out.println("Thread 1: Locked A");
        b.last();
    }
    synchronized void last() { System.out.println("Inside A"); }
}

class B {
    synchronized void methodB(A a) {
        System.out.println("Thread 2: Locked B");
        a.last();
    }
    synchronized void last() { System.out.println("Inside B"); }
}

public class DeadlockExample {
    public static void main(String[] args) {
        A a = new A();
        B b = new B();

        new Thread(() -> a.methodA(b)).start();
        new Thread(() -> b.methodB(a)).start();
    }
}
```

💡 **Avoiding Deadlocks:**
- Acquire locks in a consistent order.
- Use timeouts when acquiring locks.
- Use `ReentrantLock` instead of `synchronized` blocks.

## 51. What is a thread pool, and why is it used?

A **thread pool** reuses a fixed number of threads for executing multiple tasks, reducing thread creation overhead.

### Example using `ExecutorService`:
```java
import java.util.concurrent.*;

public class ThreadPoolExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(3);

        for (int i = 1; i <= 5; i++) {
            int taskId = i;
            executor.execute(() -> System.out.println("Executing task " + taskId));
        }

        executor.shutdown();
    }
}
```

## 52. What is the volatile keyword in Java?

The `volatile` keyword ensures that changes made to a variable by one thread are visible to other threads immediately.

### Example:
```java
class SharedResource {
    volatile boolean flag = true;
    void stop() { flag = false; }
}
```
💡 **Use `volatile` for flags and shared variables.** It does not provide atomicity like `synchronized`.

---

# User and Daemon Threads in Java

## 1. Introduction to Threads in Java
Threads in Java are lightweight processes that enable concurrent execution of tasks. Java provides two types of threads:

- **User Threads**
- **Daemon Threads**

## 2. User Threads
User threads are high-priority threads that run indefinitely unless the program terminates or they are explicitly stopped. The Java Virtual Machine (JVM) waits for all user threads to complete before shutting down.

### Characteristics of User Threads:
- They execute independently and keep the application alive.
- JVM does not terminate until all user threads have completed execution.
- Suitable for tasks requiring continuous execution, such as GUI applications, background calculations, etc.

### Example of a User Thread:
```java
class UserThreadExample extends Thread {
    public void run() {
        System.out.println("User thread is running...");
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("User thread execution completed.");
    }
    
    public static void main(String[] args) {
        UserThreadExample userThread = new UserThreadExample();
        userThread.start();
        System.out.println("Main method execution completed.");
    }
}
```

### Output:
```
Main method execution completed.
User thread is running...
User thread execution completed.
```
Even if the `main` method finishes execution, the program waits for the user thread to complete before terminating.

## 3. Daemon Threads
Daemon threads are low-priority background threads that support user threads. The JVM does not wait for daemon threads to finish execution before shutting down.

### Characteristics of Daemon Threads:
- Used for background tasks (e.g., garbage collection, logging, monitoring, etc.).
- They automatically terminate when all user threads finish execution.
- Can be set using `thread.setDaemon(true)` before starting the thread.

### Example of a Daemon Thread:
```java
class DaemonThreadExample extends Thread {
    public void run() {
        while (true) {
            System.out.println("Daemon thread is running...");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    
    public static void main(String[] args) {
        DaemonThreadExample daemonThread = new DaemonThreadExample();
        daemonThread.setDaemon(true); // Setting as daemon before starting
        daemonThread.start();
        
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Main method execution completed.");
    }
}
```

### Output:
```
Daemon thread is running...
Daemon thread is running...
Daemon thread is running...
Main method execution completed.
```
Once the main thread finishes execution, the daemon thread is automatically terminated.

## 4. Key Differences Between User and Daemon Threads
| Feature | User Thread | Daemon Thread |
|---------|------------|--------------|
| Priority | High | Low |
| JVM Behavior | JVM waits for completion | JVM terminates it when all user threads are done |
| Use Case | Important tasks | Background tasks |
| Termination | Must be explicitly stopped | Terminates automatically |

## 5. When to Use User vs. Daemon Threads
### Use User Threads When:
- The task is critical for program execution (e.g., handling user requests, computation, GUI, etc.).
- The thread needs to run until explicitly stopped.

### Use Daemon Threads When:
- The task is only needed to support other threads (e.g., garbage collection, monitoring, logging, etc.).
- The thread should terminate when no user threads remain.

## 6. Conclusion
Understanding user and daemon threads helps in writing efficient multi-threaded applications. User threads ensure critical operations complete, while daemon threads efficiently manage background tasks without blocking application termination.

# Java Thread Creation Methods

## 1. Creating Threads Using `Thread` Class
The `Thread` class in Java provides a way to create and start a new thread by extending it.

### Example:
```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread is running: " + Thread.currentThread().getName());
    }
}

public class ThreadExample {
    public static void main(String[] args) {
        MyThread thread = new MyThread();
        thread.start(); // Starts the new thread
    }
}
```

### Explanation:
- The `run()` method contains the code that runs in the new thread.
- `start()` method is used to start the thread, calling `run()` implicitly in a separate execution path.

## 2. Creating Threads Using `Runnable` Interface
Implementing `Runnable` is preferred over extending `Thread` as it allows the class to extend another class.

### Example:
```java
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Runnable Thread is running: " + Thread.currentThread().getName());
    }
}

public class RunnableExample {
    public static void main(String[] args) {
        Thread thread = new Thread(new MyRunnable());
        thread.start();
    }
}
```

### Explanation:
- The `Runnable` interface is implemented by `MyRunnable`.
- A `Thread` object is created by passing an instance of `MyRunnable`.
- `start()` method initiates the new thread.

## 3. Creating Threads Using `Callable` and `Future`
The `Callable` interface allows returning a result and throwing exceptions.

### Example:
```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

class MyCallable implements Callable<String> {
    public String call() throws Exception {
        return "Hello from Callable";
    }
}

public class CallableExample {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        Future<String> future = executor.submit(new MyCallable());
        
        System.out.println(future.get()); // Blocks until the result is available
        executor.shutdown();
    }
}
```

### Explanation:
- `Callable<String>` returns a result (`String` in this case).
- `ExecutorService` is used to manage the thread execution.
- `Future.get()` retrieves the result, blocking until available.

## 4. Using `ExecutorService` for Thread Management
Using `ExecutorService` provides better thread management compared to manually creating threads.

### Example:
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

class Task implements Runnable {
    public void run() {
        System.out.println("Task executed by: " + Thread.currentThread().getName());
    }
}

public class ExecutorExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(3);
        for (int i = 0; i < 5; i++) {
            executor.execute(new Task());
        }
        executor.shutdown();
    }
}
```

### Explanation:
- `ExecutorService` manages a pool of threads.
- `execute()` schedules tasks without handling thread creation manually.
- `shutdown()` stops accepting new tasks and allows running tasks to finish.

## Conclusion
| Method        | Return Value | Exception Handling | Thread Pool Support |
|--------------|-------------|--------------------|---------------------|
| `Thread`     | No          | No                 | No                  |
| `Runnable`   | No          | No                 | No                  |
| `Callable`   | Yes         | Yes                | Yes                 |
| `Executor`   | N/A         | N/A                | Yes                 |

Each method has its use case, and `ExecutorService` is recommended for real-world applications where thread management is crucial.

# Java Thread States and Lifecycle

## Thread Lifecycle in Java
A thread in Java goes through multiple states during its execution. These states are defined in the `Thread.State` enum. The lifecycle of a thread consists of the following states:

1. **NEW**
2. **RUNNABLE**
3. **BLOCKED**
4. **WAITING**
5. **TIMED_WAITING**
6. **TERMINATED**

Let's explore each state in detail with examples.

---

## 1. NEW State
A thread is in the NEW state when it is created but has not yet started.

```java
class NewStateExample {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> System.out.println("Thread is running"));
        System.out.println("Thread state: " + thread.getState()); // Output: NEW
    }
}
```

---

## 2. RUNNABLE State
A thread is in the RUNNABLE state when it is ready to run but waiting for CPU allocation.

```java
class RunnableStateExample {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            System.out.println("Thread is running");
        });
        thread.start();
        System.out.println("Thread state: " + thread.getState()); // Output: RUNNABLE or TERMINATED
    }
}
```

---

## 3. BLOCKED State
A thread enters the BLOCKED state when it tries to access a synchronized block that is locked by another thread.

```java
class BlockedStateExample {
    private static final Object LOCK = new Object();

    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            synchronized (LOCK) {
                try {
                    Thread.sleep(5000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        Thread t2 = new Thread(() -> {
            synchronized (LOCK) {
                System.out.println("Thread 2 acquired lock");
            }
        });

        t1.start();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        t2.start();
        System.out.println("Thread 2 state: " + t2.getState()); // Output: BLOCKED
    }
}
```

---

## 4. WAITING State
A thread enters the WAITING state when it waits indefinitely for another thread to notify it.

```java
class WaitingStateExample {
    private static final Object LOCK = new Object();

    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            synchronized (LOCK) {
                try {
                    LOCK.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        t1.start();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Thread state: " + t1.getState()); // Output: WAITING
    }
}
```

---

## 5. TIMED_WAITING State
A thread enters the TIMED_WAITING state when it waits for a specific time, such as when calling `Thread.sleep()` or `wait(timeout)`.

```java
class TimedWaitingStateExample {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        thread.start();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Thread state: " + thread.getState()); // Output: TIMED_WAITING
    }
}
```

---

## 6. TERMINATED State
A thread is in the TERMINATED state when it has finished execution.

```java
class TerminatedStateExample {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> System.out.println("Thread is running"));
        thread.start();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Thread state: " + thread.getState()); // Output: TERMINATED
    }
}
```

---

## Thread Lifecycle Diagram
```plaintext
NEW → RUNNABLE → BLOCKED/WAITING/TIMED_WAITING → RUNNABLE → TERMINATED
```

---

# Thread Synchronization in Java

## 1. Introduction to Thread Synchronization
Thread synchronization in Java is crucial when multiple threads access shared resources. Without proper synchronization, race conditions, inconsistent data, and unexpected behaviors can occur.

Java provides multiple mechanisms for thread synchronization:
- `synchronized` keyword
- Explicit Locks (`ReentrantLock`)
- `volatile` keyword
- Atomic variables (`AtomicInteger`, `AtomicLong`, etc.)

---

## 2. Using `synchronized`
The `synchronized` keyword ensures that only one thread can execute a block of code at a time for a particular object or method.

### 2.1 Synchronized Method
```java
class SharedResource {
    private int count = 0;
    
    public synchronized void increment() {
        count++;
    }
    
    public synchronized int getCount() {
        return count;
    }
}
```

### 2.2 Synchronized Block
```java
class SharedResource {
    private int count = 0;
    private final Object lock = new Object();
    
    public void increment() {
        synchronized (lock) {
            count++;
        }
    }
}
```

**Pros:** Simple and easy to use.  
**Cons:** Can cause performance bottlenecks if overused.

---

## 3. Using `ReentrantLock`
The `ReentrantLock` class from `java.util.concurrent.locks` provides more control than `synchronized`.

```java
import java.util.concurrent.locks.ReentrantLock;

class SharedResource {
    private int count = 0;
    private final ReentrantLock lock = new ReentrantLock();
    
    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}
```

**Pros:** 
- Supports fairness policy (`new ReentrantLock(true)`) to prevent starvation.  
- Provides `tryLock()` to attempt acquiring a lock without blocking.  

**Cons:** Requires explicit `lock()` and `unlock()`, making it error-prone.

---

## 4. Using `volatile`
The `volatile` keyword ensures visibility of changes to variables across threads.

```java
class SharedResource {
    private volatile boolean flag = false;
    
    public void setFlag() {
        flag = true;
    }
    
    public boolean getFlag() {
        return flag;
    }
}
```

**Pros:**
- Ensures visibility of variable updates across threads.
- Lighter than locks as it does not enforce mutual exclusion.

**Cons:**
- Does not prevent race conditions.
- Not suitable for compound actions (`count++`).

---

## 5. Using Atomic Variables
The `java.util.concurrent.atomic` package provides atomic classes that handle synchronization internally.

```java
import java.util.concurrent.atomic.AtomicInteger;

class SharedResource {
    private AtomicInteger count = new AtomicInteger(0);
    
    public void increment() {
        count.incrementAndGet();
    }
}
```

**Pros:**
- Faster than synchronized methods.
- Avoids explicit locking.

**Cons:**
- Not suitable for complex operations involving multiple variables.

---

## 6. Choosing the Right Synchronization Mechanism
| Mechanism          | Use Case |
|--------------------|----------|
| `synchronized`    | Simple locking needs |
| `ReentrantLock`   | Fine-grained control over locks |
| `volatile`        | Ensuring visibility without atomicity |
| `Atomic Variables` | Simple atomic operations |

---


# Thread Communication in Java (wait, notify, notifyAll)

## Introduction
Thread communication in Java is crucial for synchronizing threads when working with shared resources. Java provides three key methods from the `Object` class to facilitate inter-thread communication:

- `wait()`: Makes a thread wait until it is notified.
- `notify()`: Wakes up a single thread that is waiting on the object's monitor.
- `notifyAll()`: Wakes up all threads that are waiting on the object's monitor.

These methods must be called within a synchronized block or method to prevent concurrency issues.

---

## 1. `wait()` Method
The `wait()` method causes the current thread to wait indefinitely until another thread calls `notify()` or `notifyAll()` on the same object.

### Syntax:
```java
synchronized(obj) {
    obj.wait();
}
```

### Example:
```java
class SharedResource {
    void waitMethod() {
        synchronized (this) {
            try {
                System.out.println(Thread.currentThread().getName() + " is waiting...");
                wait();
                System.out.println(Thread.currentThread().getName() + " resumed execution.");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

public class WaitExample {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource();

        new Thread(resource::waitMethod, "Thread-1").start();
    }
}
```

---

## 2. `notify()` Method
The `notify()` method wakes up one of the threads waiting on the object's monitor. If multiple threads are waiting, only one (chosen arbitrarily) is notified.

### Example:
```java
class SharedResource {
    void waitMethod() {
        synchronized (this) {
            try {
                System.out.println(Thread.currentThread().getName() + " is waiting...");
                wait();
                System.out.println(Thread.currentThread().getName() + " resumed execution.");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    void notifyMethod() {
        synchronized (this) {
            System.out.println("Notifier thread is notifying one thread.");
            notify();
        }
    }
}

public class NotifyExample {
    public static void main(String[] args) throws InterruptedException {
        SharedResource resource = new SharedResource();

        Thread t1 = new Thread(resource::waitMethod, "Thread-1");
        t1.start();

        Thread.sleep(2000); // Ensuring wait() is called first

        new Thread(resource::notifyMethod, "Notifier-Thread").start();
    }
}
```

---

## 3. `notifyAll()` Method
The `notifyAll()` method wakes up all threads waiting on the object's monitor. The awakened threads will still need to reacquire the lock before continuing.

### Example:
```java
class SharedResource {
    void waitMethod() {
        synchronized (this) {
            try {
                System.out.println(Thread.currentThread().getName() + " is waiting...");
                wait();
                System.out.println(Thread.currentThread().getName() + " resumed execution.");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    void notifyAllMethod() {
        synchronized (this) {
            System.out.println("Notifier thread is notifying all threads.");
            notifyAll();
        }
    }
}

public class NotifyAllExample {
    public static void main(String[] args) throws InterruptedException {
        SharedResource resource = new SharedResource();

        Thread t1 = new Thread(resource::waitMethod, "Thread-1");
        Thread t2 = new Thread(resource::waitMethod, "Thread-2");
        t1.start();
        t2.start();

        Thread.sleep(2000); // Ensuring wait() is called first

        new Thread(resource::notifyAllMethod, "Notifier-Thread").start();
    }
}
```

---

## Key Points:
1. **Must be inside a synchronized block:** `wait()`, `notify()`, and `notifyAll()` must be called within a synchronized block or method to avoid `IllegalMonitorStateException`.
2. **wait() releases the lock:** Unlike `sleep()`, `wait()` releases the lock, allowing other threads to acquire it.
3. **notify() wakes up only one thread:** The awakened thread must reacquire the lock before proceeding.
4. **notifyAll() wakes up all waiting threads:** But they will still need to compete for the lock.
5. **Use Condition Variables with Locks in Modern Java:** Instead of `wait()`, `notify()`, and `notifyAll()`, Java provides `Condition` objects in `java.util.concurrent.locks`, which are more flexible.

---


# Thread Pooling in Java

Thread pooling is a mechanism to manage multiple threads efficiently by reusing a fixed number of threads rather than creating new ones for every task. Java provides various thread pooling mechanisms through the `Executor` framework and `ForkJoinPool`.

---

## 1. Executor Framework

The `Executor` framework in Java provides a higher-level replacement for managing threads compared to `Thread` and `Runnable`. The core component of this framework is `ExecutorService`, which helps manage a pool of worker threads.

### 1.1 Creating a Fixed Thread Pool

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class FixedThreadPoolExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(3);

        for (int i = 1; i <= 5; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("Executing Task " + taskId + " by " + Thread.currentThread().getName());
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        executor.shutdown();
    }
}
```

### 1.2 Types of Thread Pools

- **FixedThreadPool**: A pool with a fixed number of threads (`Executors.newFixedThreadPool(n)`).
- **CachedThreadPool**: A pool with dynamically growing/shrinking threads (`Executors.newCachedThreadPool()`).
- **SingleThreadExecutor**: A single-threaded executor (`Executors.newSingleThreadExecutor()`).
- **ScheduledThreadPool**: A thread pool for scheduled tasks (`Executors.newScheduledThreadPool(n)`).

---

## 2. ForkJoinPool

`ForkJoinPool` is designed for parallel computing and efficiently executing recursive divide-and-conquer tasks. It extends the `ExecutorService` and uses the *work-stealing algorithm*.

### 2.1 Example of ForkJoinPool

```java
import java.util.concurrent.RecursiveTask;
import java.util.concurrent.ForkJoinPool;

class SumTask extends RecursiveTask<Integer> {
    private final int[] numbers;
    private final int start, end;

    public SumTask(int[] numbers, int start, int end) {
        this.numbers = numbers;
        this.start = start;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        if (end - start <= 10) {  // Base case
            int sum = 0;
            for (int i = start; i < end; i++) sum += numbers[i];
            return sum;
        }
        int mid = (start + end) / 2;
        SumTask leftTask = new SumTask(numbers, start, mid);
        SumTask rightTask = new SumTask(numbers, mid, end);

        leftTask.fork();
        int rightResult = rightTask.compute();
        int leftResult = leftTask.join();
        return leftResult + rightResult;
    }
}

public class ForkJoinExample {
    public static void main(String[] args) {
        int[] numbers = new int[100];
        for (int i = 0; i < 100; i++) numbers[i] = i + 1;
        
        ForkJoinPool pool = new ForkJoinPool();
        SumTask task = new SumTask(numbers, 0, numbers.length);
        int result = pool.invoke(task);
        System.out.println("Sum: " + result);
    }
}
```

### 2.2 Advantages of ForkJoinPool

- Efficient for tasks that can be broken down into subtasks.
- Uses work-stealing for optimal performance.
- Better performance for recursive parallel computations.

---

## 3. Comparison: Executor vs ForkJoinPool

| Feature          | ExecutorService | ForkJoinPool |
|-----------------|----------------|-------------|
| Best for        | General thread management | Divide-and-conquer tasks |
| Work Stealing   | No | Yes |
| Parallelism     | Limited | High |
| Use Case       | Independent tasks | Recursive and parallel tasks |

---

## 4. Best Practices for Thread Pooling

- **Choose the right pool size**: Too many threads can lead to overhead; too few can lead to inefficiency.
- **Use `shutdown()`**: Always shut down an executor after use to release resources.
- **Handle exceptions**: Use proper exception handling inside threads to prevent unexpected failures.
- **Avoid long-running tasks in cached pools**: Cached thread pools are ideal for short-lived tasks.
- **Use ForkJoinPool for recursive tasks**: It is optimized for breaking tasks into subtasks.

---

# Java Concurrency Utilities: CountDownLatch, CyclicBarrier, Semaphore, and More

## 1. CountDownLatch

### Overview
`CountDownLatch` is used to block a set of threads until a specific number of operations are completed. It is useful in scenarios where you need to ensure that a certain number of tasks finish before proceeding.

### Example
```java
import java.util.concurrent.CountDownLatch;

class Worker extends Thread {
    private CountDownLatch latch;
    
    public Worker(CountDownLatch latch) {
        this.latch = latch;
    }
    
    @Override
    public void run() {
        try {
            System.out.println(Thread.currentThread().getName() + " is working");
            Thread.sleep(1000); // Simulate work
            latch.countDown();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class CountDownLatchExample {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch latch = new CountDownLatch(3);
        
        new Worker(latch).start();
        new Worker(latch).start();
        new Worker(latch).start();
        
        latch.await(); // Wait until all workers have finished
        System.out.println("All workers have finished. Proceeding...");
    }
}
```

### Key Points
- The `await()` method blocks until the count reaches zero.
- The `countDown()` method decreases the count.

## 2. CyclicBarrier

### Overview
`CyclicBarrier` is used when multiple threads must reach a common barrier point before proceeding further. Unlike `CountDownLatch`, it can be reused.

### Example
```java
import java.util.concurrent.CyclicBarrier;

class Task implements Runnable {
    private CyclicBarrier barrier;
    
    public Task(CyclicBarrier barrier) {
        this.barrier = barrier;
    }
    
    @Override
    public void run() {
        try {
            System.out.println(Thread.currentThread().getName() + " is waiting at the barrier");
            barrier.await();
            System.out.println(Thread.currentThread().getName() + " has crossed the barrier");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

public class CyclicBarrierExample {
    public static void main(String[] args) {
        CyclicBarrier barrier = new CyclicBarrier(3, () -> System.out.println("All parties have arrived, proceeding!"));
        
        new Thread(new Task(barrier)).start();
        new Thread(new Task(barrier)).start();
        new Thread(new Task(barrier)).start();
    }
}
```

### Key Points
- `await()` makes threads wait until all parties reach the barrier.
- A barrier action (optional) runs when all threads arrive.
- The barrier can be reused after all threads have crossed it.

## 3. Semaphore

### Overview
A `Semaphore` is used to control access to a resource with a limited number of permits.

### Example
```java
import java.util.concurrent.Semaphore;

class SharedResource {
    private Semaphore semaphore;
    
    public SharedResource(int permits) {
        semaphore = new Semaphore(permits);
    }
    
    public void accessResource() {
        try {
            semaphore.acquire();
            System.out.println(Thread.currentThread().getName() + " is accessing the resource");
            Thread.sleep(1000);
            semaphore.release();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class SemaphoreExample {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource(2);
        
        for (int i = 0; i < 5; i++) {
            new Thread(resource::accessResource).start();
        }
    }
}
```

### Key Points
- `acquire()` decreases the permit count; blocks if none are available.
- `release()` increases the permit count, allowing other threads to proceed.
- Can be used to manage concurrent access to a limited resource.

## 4. Exchanger

### Overview
`Exchanger<T>` allows two threads to exchange objects at a synchronization point.

### Example
```java
import java.util.concurrent.Exchanger;

class Producer implements Runnable {
    private Exchanger<String> exchanger;
    
    public Producer(Exchanger<String> exchanger) {
        this.exchanger = exchanger;
    }
    
    @Override
    public void run() {
        try {
            String data = "Produced Data";
            System.out.println("Producer sending: " + data);
            String received = exchanger.exchange(data);
            System.out.println("Producer received: " + received);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

class Consumer implements Runnable {
    private Exchanger<String> exchanger;
    
    public Consumer(Exchanger<String> exchanger) {
        this.exchanger = exchanger;
    }
    
    @Override
    public void run() {
        try {
            String data = "Consumed Data";
            System.out.println("Consumer sending: " + data);
            String received = exchanger.exchange(data);
            System.out.println("Consumer received: " + received);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class ExchangerExample {
    public static void main(String[] args) {
        Exchanger<String> exchanger = new Exchanger<>();
        new Thread(new Producer(exchanger)).start();
        new Thread(new Consumer(exchanger)).start();
    }
}
```

### Key Points
- Used for thread communication by exchanging objects.
- Both threads must call `exchange()` to proceed.

## Conclusion
- `CountDownLatch`: Wait for multiple threads to complete.
- `CyclicBarrier`: Synchronize threads at a common point and reuse it.
- `Semaphore`: Control access to limited resources.
- `Exchanger`: Exchange data between two threads.


# Parallel Streams and CompletableFuture in Java

## Parallel Streams

Parallel Streams in Java utilize the `ForkJoinPool` to execute stream operations concurrently. They divide data into multiple chunks and process them in parallel, improving performance for large datasets.

### Example of Parallel Streams
```java
import java.util.List;
import java.util.stream.IntStream;
import java.util.stream.Collectors;

public class ParallelStreamExample {
    public static void main(String[] args) {
        List<Integer> numbers = IntStream.rangeClosed(1, 10)
                                         .boxed()
                                         .collect(Collectors.toList());

        System.out.println("Processing in parallel:");
        numbers.parallelStream()
               .forEach(num -> System.out.println(Thread.currentThread().getName() + " - " + num));
    }
}
```

### Key Points of Parallel Streams
- **Implicit Multithreading**: Java automatically decides how many threads to use.
- **Uses ForkJoinPool**: The default common pool handles parallel execution.
- **Order is not guaranteed**: Unlike sequential streams, parallel streams may not maintain element order.
- **Best for large datasets**: Not recommended for small datasets due to overhead.

## CompletableFuture

`CompletableFuture` is part of `java.util.concurrent` and allows asynchronous, non-blocking programming. It provides a powerful way to manage asynchronous tasks, dependencies, and exception handling.

### Example of CompletableFuture
```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;

public class CompletableFutureExample {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(2000); // Simulate long-running task
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Hello, World!";
        });

        System.out.println("Waiting for result...");
        System.out.println(future.get()); // Blocking call
    }
}
```

### Key Features of CompletableFuture
- **Asynchronous Execution**: Tasks run independently on separate threads.
- **Chaining Operations**: `thenApply()`, `thenAccept()`, `thenCompose()` allow sequencing.
- **Exception Handling**: `exceptionally()` helps handle errors.
- **Combining Futures**: `thenCombine()`, `allOf()`, `anyOf()` allow running multiple tasks in parallel.

### Chaining Example
```java
CompletableFuture.supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")
    .thenAccept(System.out::println);
```

## Parallel Streams vs CompletableFuture
| Feature               | Parallel Streams | CompletableFuture |
|----------------------|----------------|----------------|
| Execution Type      | Data-parallel   | Task-parallel  |
| Thread Control     | Implicit (ForkJoinPool) | Explicit (Custom Executor) |
| Best Use Case      | Processing large collections | Running independent tasks |
| Exception Handling | Limited        | Supports exception handling |

### Choosing Between the Two
- **Use Parallel Streams** for processing large datasets where order is not crucial.
- **Use CompletableFuture** for executing independent tasks asynchronously, handling dependencies, and exception management.

By leveraging both effectively, Java developers can write high-performance concurrent applications.

# Deadlocks and Race Conditions in Java

## 1. Deadlocks

### What is a Deadlock?
A deadlock occurs in Java when two or more threads are waiting indefinitely for each other to release locks. This happens when multiple threads hold some shared resources and try to acquire resources held by others, creating a circular waiting condition.

### Example of Deadlock in Java
```java
class Resource {
    void methodA(Resource other) {
        synchronized (this) {
            System.out.println(Thread.currentThread().getName() + " locked " + this);
            try { Thread.sleep(100); } catch (InterruptedException e) {}
            synchronized (other) {
                System.out.println(Thread.currentThread().getName() + " locked " + other);
            }
        }
    }
}

public class DeadlockExample {
    public static void main(String[] args) {
        Resource r1 = new Resource();
        Resource r2 = new Resource();
        
        Thread t1 = new Thread(() -> r1.methodA(r2), "Thread-1");
        Thread t2 = new Thread(() -> r2.methodA(r1), "Thread-2");
        
        t1.start();
        t2.start();
    }
}
```
### How to Prevent Deadlocks?
- **Lock ordering**: Always acquire locks in a consistent order.
- **Try-and-lock with timeout**: Use `tryLock()` from `ReentrantLock`.
- **Avoid nested locks**: Minimize the use of multiple locks.

---

## 2. Race Conditions

### What is a Race Condition?
A race condition occurs when multiple threads access shared data and try to modify it simultaneously, leading to inconsistent results.

### Example of Race Condition in Java
```java
class Counter {
    private int count = 0;
    
    void increment() {
        count++;
    }
    
    int getCount() {
        return count;
    }
}

public class RaceConditionExample {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) counter.increment();
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) counter.increment();
        });
        
        t1.start();
        t2.start();
        
        t1.join();
        t2.join();
        
        System.out.println("Final count: " + counter.getCount());
    }
}
```
### How to Prevent Race Conditions?
- **Use synchronized blocks**
- **Use Atomic Variables (e.g., `AtomicInteger`)**
- **Use Locks (`ReentrantLock`)**
- **Use Concurrent Data Structures (`ConcurrentHashMap`, etc.)**

### Fixing the Above Example with `synchronized`
```java
class SafeCounter {
    private int count = 0;
    
    synchronized void increment() {
        count++;
    }
    
    synchronized int getCount() {
        return count;
    }
}
```

By using `synchronized`, only one thread can execute `increment()` at a time, preventing race conditions.

---

# Advanced Java Thread Concepts

## 1. ThreadLocal
ThreadLocal provides thread-local variables. Each thread accessing a ThreadLocal variable has its own isolated instance.

### Example:
```java
public class ThreadLocalExample {
    private static ThreadLocal<Integer> threadLocal = ThreadLocal.withInitial(() -> 0);

    public static void main(String[] args) {
        Runnable task = () -> {
            threadLocal.set(threadLocal.get() + 1);
            System.out.println(Thread.currentThread().getName() + " : " + threadLocal.get());
        };
        
        Thread t1 = new Thread(task);
        Thread t2 = new Thread(task);
        t1.start();
        t2.start();
    }
}
```
Each thread gets its own separate copy of the ThreadLocal variable, preventing data sharing issues.

## 2. ReentrantLock
ReentrantLock is an alternative to synchronized blocks. It provides better control over locking mechanisms, including tryLock() and lockInterruptibly().

### Example:
```java
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockExample {
    private static final ReentrantLock lock = new ReentrantLock();
    
    public static void main(String[] args) {
        Runnable task = () -> {
            lock.lock();
            try {
                System.out.println(Thread.currentThread().getName() + " acquired lock");
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
                System.out.println(Thread.currentThread().getName() + " released lock");
            }
        };
        
        Thread t1 = new Thread(task);
        Thread t2 = new Thread(task);
        t1.start();
        t2.start();
    }
}
```
The lock ensures only one thread executes the critical section at a time.

## 3. Compare-And-Swap (CAS)
CAS is an atomic instruction used in lock-free algorithms. It checks if a value matches an expected value and updates it atomically.

### Example with `AtomicInteger`:
```java
import java.util.concurrent.atomic.AtomicInteger;

public class CASExample {
    private static AtomicInteger counter = new AtomicInteger(0);

    public static void main(String[] args) {
        Runnable task = () -> {
            int oldValue, newValue;
            do {
                oldValue = counter.get();
                newValue = oldValue + 1;
            } while (!counter.compareAndSet(oldValue, newValue));
            System.out.println(Thread.currentThread().getName() + " updated value to " + counter.get());
        };
        
        Thread t1 = new Thread(task);
        Thread t2 = new Thread(task);
        t1.start();
        t2.start();
    }
}
```
CAS prevents race conditions without requiring locks, improving performance in high-concurrency environments.
