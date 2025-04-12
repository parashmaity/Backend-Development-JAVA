# Functional Programming & Streams API

## 63. What is functional programming?

Functional programming is a a typical example or pattern in which programs are built using pure functions, immutability, and declarative programming. It treats computation as the evaluation of mathematical functions and avoids changing state and mutable data.

### Key principles:
- **Pure functions** (no side effects, same input → same output)
- **Immutability** (data does not change after creation)
- **First-class functions** (functions can be assigned to variables, passed as arguments, or returned)
- **Higher-order functions** (functions that take functions as parameters or return them)
- **Declarative programming** (focus on "what to do" rather than "how to do it")

In Java, functional programming is enabled through lambda expressions, functional interfaces, and the Streams API.

## 64. What are lambda expressions in Java?

A lambda expression is an anonymous function that provides a concise way to represent functional interfaces (interfaces with a single abstract method).

### Syntax:
```java
(parameters) -> expression
(parameters) -> { statements; }
```

### Example:
```java
// Without Lambda (Anonymous class)
Comparator<Integer> comp = new Comparator<Integer>() {
    public int compare(Integer a, Integer b) {
        return a - b;
    }
};

// With Lambda
Comparator<Integer> compLambda = (a, b) -> a - b;
```

## 65. Explain method references in Java.

Method references are shorthand for lambda expressions where a method is directly referred to.

### Syntax:
```java
ClassName::methodName
```

### Types of method references:
1. **Static method reference** → `ClassName::staticMethod`
2. **Instance method reference** → `object::instanceMethod`
3. **Constructor reference** → `ClassName::new`

### Example:
```java
// Using Lambda
Function<String, Integer> func = s -> Integer.parseInt(s);

// Using Method Reference
Function<String, Integer> funcRef = Integer::parseInt;
```

## 66. What are functional interfaces?

A functional interface is an interface with exactly one abstract method (though it may have multiple default/static methods). It is used in lambda expressions.

### Examples:
- **Runnable** → `void run()`
- **Callable<T>** → `T call()`
- **Predicate<T>** → `boolean test(T t)`
- **Function<T, R>** → `R apply(T t)`
- **Consumer<T>** → `void accept(T t)`

### Example:
```java
@FunctionalInterface
interface MyFunction {
    void execute();
}

// Lambda using functional interface
MyFunction fn = () -> System.out.println("Executing...");
fn.execute();
```

## 67. How does the Stream API work in Java?

The Stream API is used for processing sequences of elements in a functional and declarative manner. It operates on Collections, Arrays, or I/O channels.

### Characteristics:
- Supports filtering, mapping, reducing, sorting, collecting.
- Uses lazy evaluation (only processes elements when necessary).
- Supports parallel processing.

### Example:
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// Convert names to uppercase using streams
List<String> upperCaseNames = names.stream()
                                   .map(String::toUpperCase)
                                   .collect(Collectors.toList());
System.out.println(upperCaseNames);
```

## 68. What is the difference between `map()` and `flatMap()`?

Both `map()` and `flatMap()` are used for transforming elements in a stream, but `flatMap()` is used when each element produces multiple values (nested structures).

| Feature | `map()` | `flatMap()` |
|---------|--------|------------|
| Input | `Stream<T>` | `Stream<Stream<T>>` |
| Output | `Stream<R>` | `Stream<T>` (flattened) |
| Used for | One-to-one transformation | One-to-many transformation |

### Example:
```java
List<String> words = Arrays.asList("Hello", "World");

// map() - Converts each word to a Stream of characters
List<List<Character>> mapped = words.stream()
                                    .map(word -> word.chars()
                                                     .mapToObj(c -> (char) c)
                                                     .toList())
                                    .toList();

// flatMap() - Flattens all characters into a single stream
List<Character> flatMapped = words.stream()
                                  .flatMap(word -> word.chars()
                                                       .mapToObj(c -> (char) c))
                                  .toList();
```

## 69. What are intermediate and terminal operations in Streams?

Stream operations are either intermediate or terminal:

### Intermediate operations → return another Stream (lazy execution).
- `map()`, `filter()`, `flatMap()`, `sorted()`, `distinct()`, `limit()`

### Terminal operations → trigger processing and return a result.
- `forEach()`, `collect()`, `reduce()`, `count()`, `findAny()`, `findFirst()`, `allMatch()`

### Example:
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

long count = names.stream()  // Intermediate
                  .filter(s -> s.length() > 3)
                  .count();   // Terminal
System.out.println(count);  // Output: 2
```

## 70. Explain `Optional` and its use cases.

`Optional<T>` is a wrapper introduced in Java 8 to handle null values safely and avoid `NullPointerException`s.

### Common methods:
- `isPresent()`
- `orElse(defaultValue)`
- `orElseGet(Supplier)`
- `orElseThrow()`
- `map()`
- `flatMap()`

### Example:
```java
Optional<String> opt = Optional.ofNullable(null);
System.out.println(opt.orElse("Default"));  // Output: Default
```

## 71. How can you perform parallel processing with Streams?

Java Streams support parallel processing using `parallelStream()` or `parallel()`.

### Example:
```java
List<Integer> numbers = IntStream.rangeClosed(1, 100)
                                 .boxed()
                                 .collect(Collectors.toList());

// Sequential
long start = System.currentTimeMillis();
numbers.stream().map(Math::sqrt).count();
long end = System.currentTimeMillis();
System.out.println("Sequential Time: " + (end - start));

// Parallel
start = System.currentTimeMillis();
numbers.parallelStream().map(Math::sqrt).count();
end = System.currentTimeMillis();
System.out.println("Parallel Time: " + (end - start));
```

## 72. What is the difference between `forEach()` and `collect()` in Streams?

| Feature | `forEach()` | `collect()` |
|---------|------------|------------|
| Type | Terminal operation | Terminal operatio
