# 🧩 Design Patterns in Java  
**The Ultimate Guide to 23 Classic Patterns (GoF) + Modern Best Practices**

This document covers **design patterns in Java** — essential for writing **clean**, **maintainable**, and **scalable** code.

You’ll learn:
- ✅ What design patterns are
- ✅ The 3 main categories (Creational, Structural, Behavioral)
- ✅ Real Java examples for each key pattern
- ✅ When to use which
- ✅ Interview-ready answers

---

## 🔹 What is a Design Pattern?

> A **design pattern** is a **reusable solution** to a **common software design problem**.

It’s not code — it’s a **template** or **blueprint** for solving problems like:
- Creating objects
- Structuring classes
- Managing communication between objects

> 📚 Popularized by the **"Gang of Four" (GoF)** book: _"Design Patterns: Elements of Reusable Object-Oriented Software"_

---

## 🔹 The 3 Categories of Design Patterns

| Category | Purpose | Number of Patterns |
|--------|--------|-------------------|
| ✅ **Creational** | How objects are created | 5 |
| ✅ **Structural** | How classes and objects are structured | 7 |
| ✅ **Behavioral** | How objects communicate and behave | 11 |

Let’s explore the **most important patterns** in each category.

---

## 1️⃣ Creational Patterns  
**How to Create Objects Safely and Flexibly**

### ✅ 1. Singleton Pattern

> Ensures a class has **only one instance** and provides a **global point of access**.

```java
public class DatabaseConnection {
    private static DatabaseConnection instance;

    private DatabaseConnection() {} // Private constructor

    public static synchronized DatabaseConnection getInstance() {
        if (instance == null) {
            instance = new DatabaseConnection();
        }
        return instance;
    }

    public void connect() {
        System.out.println("Connected to DB");
    }
}

// Usage
DatabaseConnection db = DatabaseConnection.getInstance();
db.connect();
```

✅ **Use when**: One instance needed (e.g., DB connection, logger)  
❌ **Avoid** if it introduces global state

> 💡 **Thread-safe?** Use `synchronized` or **enum** (best way):
```java
public enum DatabaseConnection {
    INSTANCE;
    public void connect() { System.out.println("Connected"); }
}
```

---

### ✅ 2. Factory Method Pattern

> Defines an **interface** for creating an object, but lets subclasses decide which class to instantiate.

```java
abstract class Shape {
    abstract void draw();
}

class Circle extends Shape {
    void draw() { System.out.println("Drawing Circle"); }
}

class Square extends Shape {
    void draw() { System.out.println("Drawing Square"); }
}

abstract class ShapeFactory {
    abstract Shape createShape();
}

class CircleFactory extends ShapeFactory {
    Shape createShape() { return new Circle(); }
}

class SquareFactory extends ShapeFactory {
    Shape createShape() { return new Square(); }
}

// Usage
ShapeFactory factory = new CircleFactory();
Shape shape = factory.createShape();
shape.draw(); // "Drawing Circle"
```

✅ **Use when**: Object creation logic varies by type  
✅ **Spring** uses this in `BeanFactory`

---

### ✅ 3. Builder Pattern

> Constructs complex objects step by step — **separates construction from representation**.

```java
public class User {
    private final String name;
    private final String email;
    private final int age;
    private final String role;

    private User(Builder builder) {
        this.name = builder.name;
        this.email = builder.email;
        this.age = builder.age;
        this.role = builder.role;
    }

    public static class Builder {
        private String name;
        private String email;
        private int age;
        private String role;

        public Builder name(String name) { this.name = name; return this; }
        public Builder email(String email) { this.email = email; return this; }
        public Builder age(int age) { this.age = age; return this; }
        public Builder role(String role) { this.role = role; return this; }

        public User build() { return new User(this); }
    }
}

// Usage
User user = new User.Builder()
    .name("Alice")
    .email("alice@x.com")
    .age(25)
    .role("USER")
    .build();
```

✅ **Use when**: Object has many optional parameters  
✅ **Immutability**: Object is immutable after build  
✅ **Readable**: Fluent API

> 💡 Use **Lombok** to generate: `@Builder`

---

### ✅ 4. Prototype Pattern

> Creates new objects by **copying an existing instance**.

```java
public class User implements Cloneable {
    private String name;
    private String email;

    public User clone() {
        try {
            return (User) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new RuntimeException(e);
        }
    }

    // setters and getters
}

// Usage
User original = new User();
original.setName("Alice");

User copy = original.clone();
copy.setName("Bob");

System.out.println(original.getName()); // Alice
System.out.println(copy.getName());     // Bob
```

✅ **Use when**: Creating objects is expensive  
✅ Avoids subclassing for object creation

---

## 2️⃣ Structural Patterns  
**How to Structure Classes and Objects**

### ✅ 5. Adapter Pattern

> Allows incompatible interfaces to work together — **wraps an old interface to match a new one**.

```java
// Old interface
class WeatherAPI {
    public double getTemperatureF() { return 86.0; }
}

// New interface expected
interface WeatherService {
    double getTemperatureC();
}

// Adapter
class WeatherAdapter implements WeatherService {
    private WeatherAPI api;

    public WeatherAdapter(WeatherAPI api) {
        this.api = api;
    }

    public double getTemperatureC() {
        return (api.getTemperatureF() - 32) * 5/9;
    }
}

// Usage
WeatherAPI oldApi = new WeatherAPI();
WeatherService service = new WeatherAdapter(oldApi);
System.out.println(service.getTemperatureC()); // 30.0
```

✅ **Use when**: Integrating legacy code or third-party libraries

---

### ✅ 6. Decorator Pattern

> Adds behavior to objects **dynamically** — **wraps** them.

```java
interface Coffee {
    double cost();
    String description();
}

class SimpleCoffee implements Coffee {
    public double cost() { return 2.0; }
    public String description() { return "Simple Coffee"; }
}

abstract class CoffeeDecorator implements Coffee {
    protected Coffee coffee;
    public CoffeeDecorator(Coffee coffee) { this.coffee = coffee; }
}

class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) { super(coffee); }
    public double cost() { return coffee.cost() + 0.5; }
    public String description() { return coffee.description() + ", Milk"; }
}

class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) { super(coffee); }
    public double cost() { return coffee.cost() + 0.2; }
    public String description() { return coffee.description() + ", Sugar"; }
}

// Usage
Coffee coffee = new SimpleCoffee();
coffee = new MilkDecorator(coffee);
coffee = new SugarDecorator(coffee);

System.out.println(coffee.description()); // Simple Coffee, Milk, Sugar
System.out.println(coffee.cost());        // 2.7
```

✅ **Use when**: Add features dynamically (e.g., logging, caching)  
✅ Used in **Java I/O**: `BufferedInputStream(new FileInputStream(...))`

---

### ✅ 7. Facade Pattern

> Provides a **simple interface** to a complex system.

```java
class CPU {
    void start() { System.out.println("CPU started"); }
}

class Memory {
    void load() { System.out.println("Memory loaded"); }
}

class HardDrive {
    void read() { System.out.println("Hard drive read"); }
}

class ComputerFacade {
    private CPU cpu;
    private Memory memory;
    private HardDrive hd;

    public ComputerFacade() {
        this.cpu = new CPU();
        this.memory = new Memory();
        this.hd = new HardDrive();
    }

    public void start() {
        cpu.start();
        memory.load();
        hd.read();
        System.out.println("Computer booted!");
    }
}

// Usage
ComputerFacade computer = new ComputerFacade();
computer.start(); // One call → boots entire system
```

✅ **Use when**: Simplify interaction with complex subsystems  
✅ Hides complexity from client

---

### ✅ 8. Proxy Pattern

> Controls access to an object — acts as a **placeholder**.

```java
interface Image {
    void display();
}

class RealImage implements Image {
    private String filename;
    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk();
    }
    private void loadFromDisk() {
        System.out.println("Loading " + filename);
    }
    public void display() {
        System.out.println("Displaying " + filename);
    }
}

class ImageProxy implements Image {
    private String filename;
    private RealImage realImage;

    public ImageProxy(String filename) {
        this.filename = filename;
    }

    public void display() {
        if (realImage == null) {
            realImage = new RealImage(filename);
        }
        realImage.display();
    }
}

// Usage
Image image = new ImageProxy("photo.jpg");
image.display(); // Loads only when needed (lazy loading)
```

✅ **Use when**: Lazy loading, access control, logging, caching

---

## 3️⃣ Behavioral Patterns  
**How Objects Communicate and Behave**

### ✅ 9. Strategy Pattern

> Defines a family of algorithms, makes them **interchangeable**.

```java
interface PaymentStrategy {
    void pay(double amount);
}

class CreditCardPayment implements PaymentStrategy {
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " by Credit Card");
    }
}

class PayPalPayment implements PaymentStrategy {
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " via PayPal");
    }
}

class ShoppingCart {
    private PaymentStrategy strategy;

    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.strategy = strategy;
    }

    public void checkout(double amount) {
        strategy.pay(amount);
    }
}

// Usage
ShoppingCart cart = new ShoppingCart();
cart.setPaymentStrategy(new CreditCardPayment());
cart.checkout(99.99);

cart.setPaymentStrategy(new PayPalPayment());
cart.checkout(49.99);
```

✅ **Use when**: Multiple ways to do something (e.g., sorting, payment)  
✅ Easy to add new strategies

---

### ✅ 10. Observer Pattern

> Defines a **one-to-many dependency** — when one object changes, all dependents are notified.

```java
import java.util.*;

interface Observer {
    void update(String message);
}

class EmailService implements Observer {
    public void update(String message) {
        System.out.println("Email: " + message);
    }
}

class SMSService implements Observer {
    public void update(String message) {
        System.out.println("SMS: " + message);
    }
}

class NewsPublisher {
    private List<Observer> observers = new ArrayList<>();

    public void addObserver(Observer o) { observers.add(o); }
    public void removeObserver(Observer o) { observers.remove(o); }

    public void publish(String message) {
        for (Observer o : observers) {
            o.update(message);
        }
    }
}

// Usage
NewsPublisher publisher = new NewsPublisher();
publisher.addObserver(new EmailService());
publisher.addObserver(new SMSService());

publisher.publish("Breaking news!"); 
// Output: Email: Breaking news!
//         SMS: Breaking news!
```

✅ **Use when**: Event-driven systems, UI updates  
✅ Built into Java: `java.util.Observable` (deprecated), **better to use custom or Spring events**

---

### ✅ 11. Command Pattern

> Encapsulates a request as an object, allowing parameterization of clients with queues, requests, and operations.

```java
interface Command {
    void execute();
}

class Light {
    void on() { System.out.println("Light ON"); }
    void off() { System.out.println("Light OFF"); }
}

class LightOnCommand implements Command {
    private Light light;
    public LightOnCommand(Light light) { this.light = light; }
    public void execute() { light.on(); }
}

class LightOffCommand implements Command {
    private Light light;
    public LightOffCommand(Light light) { this.light = light; }
    public void execute() { light.off(); }
}

class RemoteControl {
    private Command command;
    public void setCommand(Command command) { this.command = command; }
    public void pressButton() { command.execute(); }
}

// Usage
Light light = new Light();
RemoteControl remote = new RemoteControl();

remote.setCommand(new LightOnCommand(light));
remote.pressButton(); // Light ON

remote.setCommand(new LightOffCommand(light));
remote.pressButton(); // Light OFF
```

✅ **Use when**: Undo/redo, queuing requests, remote execution

---

## 📌 Interview Answer (Summary)

> _"Design patterns are reusable solutions to common problems. I use Singleton for single instances, Factory for flexible object creation, Builder for complex objects, Strategy for interchangeable algorithms, and Observer for event-driven systems. In Spring, I see patterns everywhere: BeanFactory (Factory), @EventListener (Observer), REST controllers (Facade). I don’t force patterns — I use them when they solve a real problem."_  

---

## ✅ Final Tip

> 🎯 In interviews, **give real examples**:
> _"I used the Strategy pattern to support multiple payment methods. I used Builder to create immutable user objects with many fields."_  

That shows **practical experience**.

---
