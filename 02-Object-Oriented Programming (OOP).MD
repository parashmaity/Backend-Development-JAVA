## Object-Oriented Programming (OOP)

### 11. What are the four main principles of OOP?

- **Encapsulation** – Hiding internal data and exposing only necessary functionalities.
- **Abstraction** – Hiding implementation details and showing only the essential features.
- **Inheritance** – Acquiring properties and behaviors from a parent class.
- **Polymorphism** – Allowing objects to take multiple forms (method overloading and overriding).

### 12. What is method overloading and method overriding?

- **Method Overloading** – Defining multiple methods with the same name but different parameter lists in the same class.
- **Method Overriding** – Redefining a method in a subclass that already exists in the parent class.

#### Example:
```java
class Example {
    void show(int a) { System.out.println("Integer: " + a); }  // Overloaded method
    void show(String b) { System.out.println("String: " + b); }  // Overloaded method
}

class Parent {
    void display() { System.out.println("Parent class method"); }
}

class Child extends Parent {
    @Override
    void display() { System.out.println("Child class method"); }  // Overridden method
}
```

### 13. Can we override a static method? Why or why not?

No, static methods cannot be overridden because they belong to the class, not an instance. However, they can be redeclared in a subclass, which is called **method hiding**.

#### Example:
```java
class Parent {
    static void show() { System.out.println("Parent static method"); }
}

class Child extends Parent {
    static void show() { System.out.println("Child static method"); }  // Method hiding
}
```
Calling `Child.show();` will call the child's method, not the parent's.

### 14. What is the use of the `super` keyword in Java?

The `super` keyword refers to the parent class and is used to:
1. Call the parent class constructor.
2. Access parent class methods.
3. Access parent class fields.

#### Example:
```java
class Parent {
    int x = 10;
    void display() { System.out.println("Parent class method"); }
}

class Child extends Parent {
    void show() {
        super.display(); // Calls parent method
        System.out.println("Parent x = " + super.x); // Access parent variable
    }
}
```

### 15. What is the difference between an interface and an abstract class?

| Feature | Interface | Abstract Class |
|---------|----------|---------------|
| **Methods** | Only abstract methods (until Java 8) | Can have abstract & concrete methods |
| **Fields** | Only public, static, and final | Can have instance variables |
| **Constructors** | Not allowed | Can have constructors |
| **Inheritance** | Supports multiple inheritance | Supports single inheritance |

#### Example:
```java
interface Animal { void makeSound(); }  // Interface

abstract class Bird {
    abstract void fly();  // Abstract method
    void eat() { System.out.println("Bird is eating"); } // Concrete method
}
```

### 16. What is multiple inheritance, and does Java support it?

**Multiple Inheritance** means inheriting from multiple classes. Java does not support multiple class inheritance due to ambiguity issues but allows multiple inheritance via **interfaces**.

#### Example:
```java
interface A { void methodA(); }
interface B { void methodB(); }

class C implements A, B {
    public void methodA() { System.out.println("Method A"); }
    public void methodB() { System.out.println("Method B"); }
}
```

### 17. How do you achieve abstraction in Java?

**Abstraction** is achieved using **abstract classes** and **interfaces**.

#### 1. Using Abstract Class:
```java
abstract class Vehicle {
    abstract void start();  // Abstract method
}
class Car extends Vehicle {
    void start() { System.out.println("Car is starting..."); }
}
```

#### 2. Using Interface:
```java
interface Shape {
    void draw();
}
class Circle implements Shape {
    public void draw() { System.out.println("Drawing a circle"); }
}
```

### 18. What is polymorphism? Provide an example.

**Polymorphism** allows the same method to perform different behaviors. It is of two types:
1. **Compile-time polymorphism** (Method Overloading)
2. **Runtime polymorphism** (Method Overriding)

#### Example:
```java
class Animal {
    void makeSound() { System.out.println("Animal makes a sound"); }
}
class Dog extends Animal {
    @Override
    void makeSound() { System.out.println("Dog barks"); }  // Runtime polymorphism
}
```

### 19. Explain the use of the `this` keyword in Java.

The `this` keyword refers to the current instance of a class. It is used for:
1. Differentiating instance variables from local variables.
2. Calling another constructor in the same class.
3. Returning the current object.

#### Example:
```java
class Example {
    int x;
    Example(int x) { this.x = x; }  // `this` refers to the instance variable
}
```

### 20. How does Java implement encapsulation?

**Encapsulation** is data hiding and is implemented using:
1. **Private variables**
2. **Public getter and setter methods**

#### Example:
```java
class Employee {
    private int salary;

    public int getSalary() { return salary; }
    public void setSalary(int salary) { this.salary = salary; }
}
```

