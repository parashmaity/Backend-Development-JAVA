# Design Patterns & Best Practices

## 93. What are design patterns? Name some commonly used ones.

Design patterns are reusable solutions to common software design problems. They are best practices used by experienced developers to create robust, scalable, and maintainable code.

### Common Design Patterns in Java:
- **Creational Patterns:** Singleton, Factory Method, Abstract Factory, Builder, Prototype
- **Structural Patterns:** Adapter, Decorator, Proxy, Composite, Flyweight, Facade
- **Behavioral Patterns:** Observer, Strategy, Command, Chain of Responsibility, Template Method

---

## 94. Explain the Singleton pattern and how to implement it in Java.

The **Singleton Pattern** ensures that a class has only one instance and provides a global access point to it.

### Implementation using Lazy Initialization with Double-Checked Locking:
```java
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() {} // Private constructor

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```
**Key Points:**
- The `volatile` keyword prevents reordering of instructions.
- The `synchronized` block ensures thread safety.

---

## 95. What is the Factory Method pattern?

The **Factory Method Pattern** provides an interface for creating objects in a superclass but allows subclasses to alter the type of objects that will be created.

### Example:
```java
// Step 1: Define an interface
interface Product {
    void create();
}

// Step 2: Implement concrete classes
class ConcreteProductA implements Product {
    public void create() { System.out.println("Product A created."); }
}
class ConcreteProductB implements Product {
    public void create() { System.out.println("Product B created."); }
}

// Step 3: Factory Class
class ProductFactory {
    public static Product getProduct(String type) {
        if ("A".equalsIgnoreCase(type)) return new ConcreteProductA();
        else if ("B".equalsIgnoreCase(type)) return new ConcreteProductB();
        return null;
    }
}

// Step 4: Usage
public class FactoryMethodExample {
    public static void main(String[] args) {
        Product product = ProductFactory.getProduct("A");
        product.create();
    }
}
```
**Key Points:**
- Decouples object creation from the client code.
- Supports **Open/Closed Principle** (new product types can be added without modifying existing code).

---

## 96. Explain the Observer pattern with an example.

The **Observer Pattern** allows an object (the subject) to notify multiple observers automatically when its state changes.

### Example:
```java
import java.util.ArrayList;
import java.util.List;

// Subject (Observable)
class Subject {
    private List<Observer> observers = new ArrayList<>();
    
    public void addObserver(Observer observer) { observers.add(observer); }
    public void notifyObservers(String message) {
        for (Observer observer : observers) {
            observer.update(message);
        }
    }
}

// Observer Interface
interface Observer {
    void update(String message);
}

// Concrete Observers
class ConcreteObserverA implements Observer {
    public void update(String message) { System.out.println("Observer A: " + message); }
}
class ConcreteObserverB implements Observer {
    public void update(String message) { System.out.println("Observer B: " + message); }
}

// Usage
public class ObserverPatternExample {
    public static void main(String[] args) {
        Subject subject = new Subject();
        subject.addObserver(new ConcreteObserverA());
        subject.addObserver(new ConcreteObserverB());

        subject.notifyObservers("Event Occurred!");
    }
}
```
**Key Points:**
- Decouples objects (the subject doesn’t need to know observer details).
- Used in event handling, messaging systems, and MVC architectures.

---

## 97. How is the Dependency Injection (DI) pattern used in Java?

**Dependency Injection (DI)** is a design pattern where dependencies (objects) are injected into a class rather than being created inside it.

### Example using Constructor Injection:
```java
class Service {
    public void perform() { System.out.println("Service Executed."); }
}

class Client {
    private Service service;

    public Client(Service service) {  // Dependency Injected via constructor
        this.service = service;
    }

    public void doTask() {
        service.perform();
    }
}

public class DIExample {
    public static void main(String[] args) {
        Service service = new Service();
        Client client = new Client(service);  // Injecting dependency
        client.doTask();
    }
}
```
**Key Points:**
- Improves code testability and flexibility.
- **Spring Framework** is built around Dependency Injection.

---

## 98. What is the Builder pattern, and when should it be used?

The **Builder Pattern** is used to construct complex objects step by step. It helps avoid telescoping constructors.

### Example:
```java
class Car {
    private String engine;
    private int wheels;

    // Private constructor
    private Car(CarBuilder builder) {
        this.engine = builder.engine;
        this.wheels = builder.wheels;
    }

    public static class CarBuilder {
        private String engine;
        private int wheels;

        public CarBuilder setEngine(String engine) {
            this.engine = engine;
            return this;
        }

        public CarBuilder setWheels(int wheels) {
            this.wheels = wheels;
            return this;
        }

        public Car build() {
            return new Car(this);
        }
    }
}

// Usage
public class BuilderPatternExample {
    public static void main(String[] args) {
        Car car = new Car.CarBuilder()
                .setEngine("V8")
                .setWheels(4)
                .build();
        System.out.println("Car built with V8 Engine and 4 Wheels.");
    }
}
```
**Key Points:**
- Makes objects immutable and easier to construct.
- Avoids constructor overloading issues.

---

## 99. Explain the Proxy pattern in Java.

The **Proxy Pattern** provides a placeholder for another object to control access to it.

### Example (Logging Proxy):
```java
interface Service {
    void request();
}

// Real Service
class RealService implements Service {
    public void request() { System.out.println("Executing Real Service"); }
}

// Proxy Class
class ServiceProxy implements Service {
    private RealService realService;

    public void request() {
        if (realService == null) realService = new RealService();
        System.out.println("Logging: Service is being accessed.");
        realService.request();
    }
}

// Usage
public class ProxyPatternExample {
    public static void main(String[] args) {
        Service service = new ServiceProxy();
        service.request();
    }
}
```
**Key Points:**
- Used for lazy initialization, security, and logging.
- Example: **Spring AOP Proxies**.

