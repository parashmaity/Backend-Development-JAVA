# Java 8+ Features

## 73. What is the significance of default methods in interfaces?

Before Java 8, interfaces could only have abstract methods, and adding a new method to an interface required updating all implementing classes. Default methods solve this problem by allowing methods with a default implementation inside interfaces.

### Example:
```java
interface Vehicle {
    default void start() {
        System.out.println("Vehicle is starting");
    }
}
class Car implements Vehicle {
    // No need to override start() unless we want a custom implementation
}
```

### Significance:
* Allows adding new methods to interfaces without breaking existing implementations.
* Supports backward compatibility.

---

## 74. What is the purpose of the `java.time` package?

The `java.time` package, introduced in Java 8, provides a modern API for handling date and time. It replaces the old `java.util.Date` and `java.util.Calendar` which were error-prone.

### Key Classes in `java.time`:
* `LocalDate` – Represents a date without time.
* `LocalTime` – Represents a time without a date.
* `LocalDateTime` – Represents both date and time.
* `ZonedDateTime` – Represents date/time with timezone.
* `Duration` and `Period` – Used for measuring time differences.

### Example:
```java
import java.time.LocalDate;
import java.time.LocalTime;
import java.time.LocalDateTime;

public class TimeExample {
    public static void main(String[] args) {
        LocalDate date = LocalDate.now();
        LocalTime time = LocalTime.now();
        LocalDateTime dateTime = LocalDateTime.now();

        System.out.println("Date: " + date);
        System.out.println("Time: " + time);
        System.out.println("DateTime: " + dateTime);
    }
}
```

### Benefits:
* Thread-safe and immutable.
* More readable and powerful API.

---

## 75. Explain `CompletableFuture` in Java 8.

`CompletableFuture` is part of `java.util.concurrent` and allows asynchronous, non-blocking programming.

### Example:
```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureExample {
    public static void main(String[] args) {
        CompletableFuture.supplyAsync(() -> "Hello")
                         .thenApply(str -> str + " World")
                         .thenAccept(System.out::println);
    }
}
```

### Key Features:
* Supports asynchronous programming.
* Allows chaining operations (`thenApply`, `thenAccept`, etc.).
* Supports parallel execution.

---

## 76. How do you implement asynchronous programming in Java?

Asynchronous programming in Java can be achieved using:
* `CompletableFuture`
* `ExecutorService`
* Reactive programming (Project Reactor / RxJava)

### Example using `CompletableFuture`:
```java
import java.util.concurrent.CompletableFuture;

public class AsyncExample {
    public static void main(String[] args) {
        CompletableFuture.runAsync(() -> {
            System.out.println("Running in background: " + Thread.currentThread().getName());
        });
    }
}
```

---

## 77. What is the `@FunctionalInterface` annotation?

The `@FunctionalInterface` annotation ensures that an interface has exactly one abstract method, making it a functional interface.

### Example:
```java
@FunctionalInterface
interface MyFunctionalInterface {
    void doSomething();
}
```

### Why is it useful?
* Helps in using lambda expressions.
* Ensures that the interface has only one abstract method.

---

## 78. What are the new features in Java 9?

Some key Java 9 features:
* **JShell (REPL)**: Interactive command-line for Java.
* **Module System (Project Jigsaw)**: Allows modular development (`module-info.java`).
* **Improved Streams API**: New methods like `takeWhile()`, `dropWhile()`.
* **Private Methods in Interfaces**: Allows private helper methods inside interfaces.

---

## 79. What is the difference between `var` and explicit type declarations?

Java 10 introduced `var`, which allows type inference for local variables.

### Example:
```java
var message = "Hello"; // Java infers it as String
var num = 100; // Java infers it as int
```

### Key Differences:
* `var` cannot be used for class fields, method parameters, or return types.
* Improves readability but makes it harder to determine types in complex code.

---

## 80. What are Records in Java?

Introduced in Java 14, **Records** are immutable data classes that reduce boilerplate code.

### Example:
```java
public record Person(String name, int age) {}
```

### This automatically generates:
* `toString()`
* `equals()`
* `hashCode()`
* Constructor and Getters.

---

## 81. What is the Text Blocks feature introduced in Java?

Java 13 introduced **Text Blocks** to handle multi-line strings easily using `"""`.

### Example:
```java
String json = """
{
  "name": "John",
  "age": 30
}
""";
```

### Benefits:
* No need for `\n` for new lines.
* Improves code readability.

---

## 82. Explain the Sealed Classes feature in Java.

Introduced in Java 15, **sealed classes** allow restricting which classes can extend them.

### Example:
```java
sealed class Vehicle permits Car, Bike {}

final class Car extends Vehicle {}
final class Bike extends Vehicle {}
```

### Key Benefits:
* Controls inheritance.
* Improves security by limiting subclasses.

---

## Summary of Java 8+ Features

| Feature               | Description                                      |
|-----------------------|--------------------------------------------------|
| **Default Methods**   | Allows interface methods with implementations.  |
| **java.time Package** | Modern Date & Time API.                         |
| **CompletableFuture** | Supports asynchronous programming.               |
| **Functional Interfaces** | Enables Lambda expressions.                  |
| **JShell**           | Interactive REPL for Java 9+.                     |
| **var**              | Local variable type inference (Java 10).          |
| **Records**          | Immutable data classes (Java 14).                 |
| **Text Blocks**      | Multi-line Strings using `"""` (Java 13).         |
| **Sealed Classes**   | Restricts inheritance (Java 15).                  |

