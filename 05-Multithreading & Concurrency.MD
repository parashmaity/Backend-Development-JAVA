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

