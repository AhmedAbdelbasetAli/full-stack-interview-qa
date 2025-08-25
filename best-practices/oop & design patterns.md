# 🧱 OOP & Design Patterns Deep Dive  
**Mastering Object-Oriented Programming and Design Patterns in Java**

This document is a **comprehensive guide** to **Object-Oriented Programming (OOP)** and **Design Patterns** — the **foundation of clean, maintainable, and scalable software**.

You’ll learn:
- ✅ The 4 pillars of OOP
- ✅ Key design patterns (GoF)
- ✅ Real Java examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 🔹 The 4 Pillars of OOP

> OOP is built on four core principles:

### 1. **Encapsulation**
> Bundling data and methods that operate on that data, and **hiding internal state**.

```java
public class BankAccount {
    private double balance; // Hidden

    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }

    public double getBalance() {
        return balance;
    }
}
```

✅ Prevents invalid state  
✅ Centralizes logic (e.g., validation)

---

### 2. **Abstraction**
> Hiding complex implementation details and showing only essential features.

```java
public interface PaymentProcessor {
    void processPayment(double amount);
}

public class CreditCardProcessor implements PaymentProcessor {
    public void processPayment(double amount) {
        // Complex logic: encrypt, call gateway, handle response
        System.out.println("Processing $" + amount + " via credit card");
    }
}

// User only sees:
PaymentProcessor processor = new CreditCardProcessor();
processor.processPayment(99.99);
```

✅ Reduces complexity  
✅ Enables plug-and-play components

---

### 3. **Inheritance**
> Reusing code by creating new classes from existing ones.

```java
class Animal {
    void eat() { System.out.println("Eating..."); }
}

class Dog extends Animal {
    void bark() { System.out.println("Barking..."); }
}

Dog dog = new Dog();
dog.eat();  // Inherited
dog.bark(); // Own method
```

✅ Code reuse  
✅ Models real-world hierarchies

> 📌 But prefer **composition over inheritance** for flexibility.

---

### 4. **Polymorphism**
> One interface, multiple implementations.

```java
interface Shape {
    double area();
}

class Circle implements Shape {
    private double radius;
    public double area() { return Math.PI * radius * radius; }
}

class Rectangle implements Shape {
    private double width, height;
    public double area() { return width * height; }
}

// Polymorphic usage
List<Shape> shapes = Arrays.asList(new Circle(5), new Rectangle(4, 6));
for (Shape s : shapes) {
    System.out.println(s.area()); // Calls correct method at runtime
}
```

✅ Enables flexible, extensible code  
✅ Core to many design patterns

---

## 🔹 SOLID Principles (OOP Best Practices)

| Principle | Meaning |
|--------|--------|
| **S** - Single Responsibility | A class should have one reason to change |
| **O** - Open/Closed | Open for extension, closed for modification |
| **L** - Liskov Substitution | Subtypes must be substitutable for their base types |
| **I** - Interface Segregation | Clients shouldn’t be forced to depend on unused methods |
| **D** - Dependency Inversion | Depend on abstractions, not concretions |

✅ These guide **clean OOP design**.

---

## 🔹 Key Design Patterns

### ✅ 1. Singleton
> Ensures only one instance of a class.

```java
public enum DatabaseConnection {
    INSTANCE;
    public void connect() { System.out.println("Connected"); }
}
```

✅ Use for: DB connections, loggers  
✅ **Best with enum** (thread-safe, serialization-safe)

---

### ✅ 2. Factory Method
> Let subclasses decide which class to instantiate.

```java
abstract class ShapeFactory {
    abstract Shape createShape();
}

class CircleFactory extends ShapeFactory {
    Shape createShape() { return new Circle(); }
}
```

✅ Use when: Object creation varies by type  
✅ Decouples client from concrete classes

---

### ✅ 3. Builder
> Constructs complex objects step by step.

```java
User user = new User.Builder()
    .name("Alice")
    .email("alice@x.com")
    .age(25)
    .role("USER")
    .build();
```

✅ Use when: Many optional parameters  
✅ Enables immutability and fluent API

---

### ✅ 4. Strategy
> Encapsulates algorithms and makes them interchangeable.

```java
cart.setPaymentStrategy(new CreditCardPayment());
cart.checkout(99.99);

cart.setPaymentStrategy(new PayPalPayment());
cart.checkout(49.99);
```

✅ Use when: Multiple ways to do something (e.g., sorting, payment)

---

### ✅ 5. Observer
> Notifies multiple objects when one changes.

```java
publisher.addObserver(new EmailService());
publisher.addObserver(new SMSService());
publisher.publish("Order placed!");
```

✅ Use for: Event-driven systems, UI updates

---

### ✅ 6. Decorator
> Adds behavior dynamically by wrapping objects.

```java
InputStream input = new BufferedInputStream(new FileInputStream("file.txt"));
```

✅ Use for: Logging, caching, compression  
✅ Java I/O uses this heavily

---

### ✅ 7. Adapter
> Makes incompatible interfaces work together.

```java
WeatherService service = new WeatherAdapter(oldFahrenheitAPI);
double temp = service.getTemperatureC(); // Converts internally
```

✅ Use when: Integrating legacy or third-party code

---

### ✅ 8. Facade
> Provides a simple interface to a complex system.

```java
Computer computer = new Computer();
computer.start(); // One call → boots CPU, memory, disk
```

✅ Use when: Simplify complex subsystems  
✅ Hides complexity from client

---

## 🔹 Composition vs Inheritance

> **"Favor composition over inheritance"** — GoF

| Inheritance (`extends`) | Composition (`has-a`) |
|------------------------|------------------------|
| "is-a" relationship | "has-a" relationship |
| Tight coupling | Loose coupling |
| Fragile base class problem | Flexible, testable |
| Hard to change | Easy to swap at runtime |

✅ Use **composition** for most cases  
✅ Use **inheritance** only for true hierarchies

---

## 🔹 Real-World Example: E-Commerce System

```java
// Strategy: Multiple payment methods
PaymentStrategy strategy = new CreditCardPayment();
ShoppingCart cart = new ShoppingCart(strategy);
cart.checkout();

// Observer: Notify services on order
OrderPublisher publisher = new OrderPublisher();
publisher.addObserver(new EmailService());
publisher.addObserver(new InventoryService());
publisher.publish(order);

// Builder: Create complex order
Order order = new Order.Builder()
    .addItem("Laptop")
    .setShippingAddress(addr)
    .applyDiscount("SUMMER20")
    .build();

// Singleton: Logger
Logger logger = Logger.INSTANCE;
logger.log("Order processed");
```

✅ Real systems combine OOP and patterns.

---

## 📌 Interview Answer

> _"OOP is based on encapsulation, abstraction, inheritance, and polymorphism. I use design patterns to solve common problems: Strategy for interchangeable algorithms, Observer for event-driven logic, Builder for complex objects. I favor composition over inheritance for flexibility. In Spring, I see patterns everywhere — BeanFactory (Factory), @EventListener (Observer), REST controllers (Facade). I use them to write clean, maintainable code."_  

---

## ✅ Final Tip

> 🎯 In interviews, **draw it**:
> ```
> [Client] → [Interface] ← [ConcreteA, ConcreteB]  (Strategy)
> [Subject] → [Observer1, Observer2]              (Observer)
> [Client] → [Facade] → [SubsystemA, SubsystemB]  (Facade)
> ```
> And say:  
> _"This is how I design systems — using OOP principles and proven patterns."_  

That shows **deep architectural thinking**.

---

