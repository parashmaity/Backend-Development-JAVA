# Exception Handling in Java

## 21. What are checked and unchecked exceptions?

### Checked Exceptions:

- Exceptions that are checked at compile time.
- The programmer must handle these exceptions using try-catch or declare them using `throws`.
- **Examples:** `IOException`, `SQLException`.

### Unchecked Exceptions:

- Exceptions that occur at runtime and are not checked during compilation.
- Usually caused by programming errors (e.g., invalid input, null references).
- **Examples:** `NullPointerException`, `ArrayIndexOutOfBoundsException`.

---

## 22. What is the difference between throw and throws?

| Feature        | throw                                                  | throws                                                |
| -------------- | ------------------------------------------------------ | ----------------------------------------------------- |
| Purpose        | Used to explicitly throw an exception.                 | Used in method signatures to declare exceptions.      |
| Location       | Inside a method or block.                              | After method signature.                               |
| Exception Type | Can only throw a single exception at a time.           | Can declare multiple exceptions, separated by commas. |
| Example        | `throw new IllegalArgumentException("Invalid input");` | `void readFile() throws IOException {}`               |

---

## 23. Can we have multiple catch blocks for a single try block?

Yes, Java allows multiple catch blocks to handle different types of exceptions separately.

```java
try {
    int num = 5 / 0;
} catch (ArithmeticException e) {
    System.out.println("Arithmetic Exception: " + e.getMessage());
} catch (NullPointerException e) {
    System.out.println("NullPointerException: " + e.getMessage());
} finally {
    System.out.println("Execution completed.");
}
```

📌 The first matching catch block is executed.

---

## 24. What is the use of the finally block?

- The `finally` block always executes after the `try-catch` block, regardless of whether an exception was thrown or caught.
- It is mainly used for resource cleanup (e.g., closing files, database connections).

```java
try {
    FileReader file = new FileReader("test.txt");
} catch (FileNotFoundException e) {
    System.out.println("File not found.");
} finally {
    System.out.println("This will always execute.");
}
```

📌 Even if an exception occurs, the `finally` block will execute.

---

## 25. What happens if an exception is thrown inside a catch block?

- If an exception occurs inside a `catch` block, it can propagate to the caller method.
- To avoid this, wrap the code in another try-catch block inside the `catch` statement.

```java
try {
    int num = 5 / 0;
} catch (ArithmeticException e) {
    try {
        String s = null;
        s.length(); // This throws NullPointerException
    } catch (NullPointerException ex) {
        System.out.println("Handled inside catch block.");
    }
}
```

---

## 26. What are custom exceptions, and how do you create them?

- A custom exception is a user-defined exception that extends `Exception` or `RuntimeException`.

```java
class InvalidAgeException extends Exception {
    public InvalidAgeException(String message) {
        super(message);
    }
}

class Test {
    public static void main(String[] args) {
        try {
            validateAge(15);
        } catch (InvalidAgeException e) {
            System.out.println(e.getMessage());
        }
    }

    static void validateAge(int age) throws InvalidAgeException {
        if (age < 18) {
            throw new InvalidAgeException("Age must be 18 or above.");
        }
    }
}
```

📌 Extending `Exception` makes it a checked exception, while extending `RuntimeException` makes it unchecked.

---

## 27. Can a finally block override a return statement in Java?

- No, but if a return statement is in both the `try` and `finally` blocks, the `finally` block takes precedence.

```java
public static int test() {
    try {
        return 10;
    } finally {
        return 20;
    }
}

public static void main(String[] args) {
    System.out.println(test()); // Output: 20
}
```

📌 The return statement in `finally` overrides the one in `try`.

---

## 28. How does exception propagation work?

- If an exception is not handled in the current method, it propagates to the calling method.
- If no method handles it, the JVM terminates the program.

```java
void method1() throws IOException {
    throw new IOException("IO Error");
}

void method2() throws IOException {
    method1(); // Exception propagates
}

public static void main(String[] args) {
    try {
        new Test().method2();
    } catch (IOException e) {
        System.out.println("Exception caught: " + e.getMessage());
    }
}
```

📌 Exception bubbles up until it is caught.

---

## 29. What is the difference between Error and Exception?

| Feature    | Error                                                        | Exception                                                 |
| ---------- | ------------------------------------------------------------ | --------------------------------------------------------- |
| Definition | Represents serious problems that are beyond program control. | Represents conditions that can be handled by the program. |
| Recovery   | Not recoverable (e.g., `OutOfMemoryError`).                  | Recoverable if handled properly.                          |
| Examples   | `StackOverflowError`, `OutOfMemoryError`.                    | `IOException`, `NullPointerException`.                    |

---

## 30. How does Java handle OutOfMemoryError?

- `OutOfMemoryError` occurs when JVM runs out of memory.

### Causes:

- Large object allocations
- Memory leaks
- Too many threads

🔹 **Possible Solutions:**

1. Increase heap size using JVM options:
   ```sh
   java -Xmx1024m MyApp
   ```
2. Use profiling tools (e.g., JVisualVM, Eclipse MAT) to detect memory leaks.
3. Optimize code to release unused objects.
4. Use `SoftReference` and `WeakReference` for caching objects.

---

## 31. What is SoftReference and WeakReference?

Java provides reference types beyond strong references to help manage memory efficiently.

### SoftReference (Softly Reachable Objects)

- Cleared only when JVM is running out of memory.
- Useful for caching large objects.

```java
SoftReference<String> softRef = new SoftReference<>(new String("Large Data"));
```

### WeakReference (Weakly Reachable Objects)

- Removed as soon as GC detects them.
- Useful for memory-sensitive objects.

```java
WeakReference<String> weakRef = new WeakReference<>(new String("Weak Data"));
```

📌 **Key Differences:**

| Feature       | SoftReference                      | WeakReference                      |
| ------------- | ---------------------------------- | ---------------------------------- |
| GC Behavior   | Removed only when JVM needs memory | Removed as soon as GC detects them |
| Best Used For | Caching objects                    | Memory-sensitive objects           |

