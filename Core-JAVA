# Java Fundamentals

## 1. What are the key features of Java?

Java has several key features:

- **Platform Independence** – Java uses bytecode and the JVM (Java Virtual Machine) to run on any platform.
- **Object-Oriented** – Java follows OOP principles such as encapsulation, inheritance, and polymorphism.
- **Automatic Memory Management** – Java has garbage collection to manage memory automatically.
- **Multi-threading** – Java supports concurrent execution of multiple threads.
- **Robust and Secure** – Java has strong memory management, exception handling, and security features like the security manager.
- **High Performance** – Uses Just-In-Time (JIT) compilation to optimize performance.
- **Interoperability** – Java can integrate with other languages using JNI (Java Native Interface).

## 2. How does Java achieve platform independence?

Java achieves platform independence through bytecode:

- Java source code (`.java` file) is compiled into bytecode (`.class` file).
- The bytecode is executed by the JVM (Java Virtual Machine), which is available for different operating systems.
- Since JVM abstracts the hardware differences, the same Java code runs on multiple platforms (**Write Once, Run Anywhere - WORA**).

## 3. Explain the difference between JDK, JRE, and JVM.

| Component                          | Description                                                                                                     |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **JDK (Java Development Kit)**     | Includes compiler, JRE, JVM, and development tools for writing Java programs.                                   |
| **JRE (Java Runtime Environment)** | Provides libraries, JVM, and other components needed to run Java applications.                                  |
| **JVM (Java Virtual Machine)**     | Converts Java bytecode into machine code and executes it. It provides memory management and garbage collection. |

- **JDK = JRE + development tools**
- **JRE = JVM + runtime libraries**

## 4. What is the difference between a compiled language and an interpreted language?

- **Compiled Language**: The source code is translated into machine code before execution (e.g., C, C++).
- **Interpreted Language**: The source code is executed line by line by an interpreter (e.g., Python, JavaScript).
- **Java is both**:
  - It compiles Java code into bytecode (`javac` creates `.class` files).
  - Then, the JVM interprets the bytecode or uses JIT compilation to optimize execution.

## 5. Explain Just-In-Time (JIT) Compilation.

- **JIT Compiler** is part of the JVM that converts bytecode into native machine code at runtime.
- Instead of interpreting bytecode line-by-line, JIT compiles frequently used methods into machine code for better performance.
- This results in faster execution after the first few runs.

## 6. What are wrapper classes in Java?

Wrapper classes convert primitive data types into objects (**Boxing**) and vice versa (**Unboxing**).

| Primitive Type | Wrapper Class |
| -------------- | ------------- |
| byte           | Byte          |
| short          | Short         |
| int            | Integer       |
| long           | Long          |
| float          | Float         |
| double         | Double        |
| char           | Character     |
| boolean        | Boolean       |

Example:

```java
int num = 10;
Integer obj = Integer.valueOf(num);  // Boxing
int newNum = obj.intValue();  // Unboxing
```

## 7. Explain the difference between `==` and `.equals()` in Java.

- `==` compares **memory addresses** (references).
- `.equals()` compares **actual values** (overridden in `String` and `Integer` classes).

Example:

```java
String s1 = new String("Java");
String s2 = new String("Java");
System.out.println(s1 == s2);        // false (different memory locations)
System.out.println(s1.equals(s2));   // true (same content)
```

## 8. What is the difference between `final`, `finally`, and `finalize()`?

| Keyword      | Purpose                                                                           |
| ------------ | --------------------------------------------------------------------------------- |
| `final`      | Used to declare constants, prevent method overriding, or prevent inheritance.     |
| `finally`    | Used in exception handling to execute code always, even if an exception occurs.   |
| `finalize()` | A method called by the garbage collector before an object is removed from memory. |

Example:

```java
final int x = 10;  // Final variable (cannot be changed)

try {
    int data = 5 / 0;
} catch (Exception e) {
    System.out.println("Exception caught");
} finally {
    System.out.println("Finally block executed");
}

@Override
protected void finalize() {
    System.out.println("Object is garbage collected");
}
```

## 9. What is the purpose of the `static` keyword in Java?

- **Static variables** – Shared across all instances of a class.
- **Static methods** – Can be called without creating an object.
- **Static blocks** – Execute before the `main` method (used for initialization).

Example:

```java
class Test {
    static int count = 0;  // Static variable

    static void show() {   // Static method
        System.out.println("Static method called");
    }

    static {   // Static block
        System.out.println("Static block executed");
    }
}

public class Main {
    public static void main(String[] args) {
        Test.show();
        System.out.println(Test.count);
    }
}
```

## 10. Explain the differences between `String`, `StringBuffer`, and `StringBuilder`.

| Feature       | `String`                              | `StringBuffer`             | `StringBuilder` |
| ------------- | ------------------------------------- | -------------------------- | --------------- |
| Mutability    | Immutable                             | Mutable                    | Mutable         |
| Thread Safety | Thread-safe                           | Thread-safe (synchronized) | Not thread-safe |
| Performance   | Slow (new object created for changes) | Moderate                   | Fast            |

Example:

```java
String s = "Hello";
s.concat(" World");  // New object created, original remains unchanged

StringBuffer sb = new StringBuffer("Hello");
sb.append(" World"); // Modifies existing object

StringBuilder sbuilder = new StringBuilder("Hello");
sbuilder.append(" World"); // Faster modification
```

