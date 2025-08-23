# 🧱 Inheritance vs Composition  
**The Golden Rule of Object-Oriented Design**

This document explains the **critical difference** between **Inheritance** and **Composition**, why **composition is generally preferred**, and when to use each.

You’ll learn:
- ✅ Clear definitions
- ✅ Real code examples (Java)
- ✅ Pros and cons
- ✅ Best practices
- ✅ Interview-ready answers

---

## 🔹 What is Inheritance?

> **Inheritance** is an **"is-a" relationship** where a class **inherits** fields and methods from a parent class.

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

✅ `Dog` **is an** `Animal`

---

### ✅ When Inheritance Works Well

| Use Case | Why |
|--------|-----|
| ✅ **True "is-a" relationship** | `Car` is a `Vehicle` |
| ✅ **Code reuse** | Avoid duplicating common logic |
| ✅ **Polymorphism** | Treat `Dog`, `Cat` as `Animal` |
| ✅ **Template Method Pattern** | Base class defines algorithm, subclasses override steps |

---

### ❌ Problems with Inheritance

| Issue | Explanation |
|------|-------------|
| ❌ **Fragile Base Class Problem** | Changes in parent break children |
| ❌ **Tight Coupling** | Child depends on parent’s implementation |
| ❌ **Inflexible Hierarchy** | Hard to change later (e.g., `FlyingDog`?) |
| ❌ **Multiple Inheritance Not Allowed** | Java doesn’t support it (no `extends A, B`) |
| ❌ **Overuse Leads to Deep Hierarchies** | `Animal → Mammal → Pet → Dog → Labrador` |

---

## 🔹 What is Composition?

> **Composition** is a **"has-a" relationship** where a class **contains an instance of another class**.

```java
class Engine {
    void start() { System.out.println("Engine started"); }
}

class Car {
    private Engine engine; // Car has an Engine

    public Car() {
        this.engine = new Engine();
    }

    void start() {
        engine.start(); // Delegation
    }
}

Car car = new Car();
car.start(); // "Engine started"
```

✅ `Car` **has an** `Engine`

---

### ✅ Why Composition is Preferred

| Benefit | Explanation |
|--------|-------------|
| ✅ **Loose Coupling** | `Car` depends on `Engine` interface, not implementation |
| ✅ **Flexibility** | Swap `ElectricEngine` for `GasEngine` at runtime |
| ✅ **No Hierarchy Issues** | No deep inheritance trees |
| ✅ **Easier Testing** | Mock `Engine` in unit tests |
| ✅ **Follows SOLID Principles** | Open/Closed, Dependency Inversion |

---

### ✅ Example: Swappable Engines

```java
interface Engine {
    void start();
}

class GasEngine implements Engine {
    public void start() { System.out.println("Gas engine started"); }
}

class ElectricEngine implements Engine {
    public void start() { System.out.println("Electric engine started"); }
}

class Car {
    private Engine engine;

    public Car(Engine engine) {
        this.engine = engine; // Injected dependency
    }

    void start() {
        engine.start();
    }
}

// Usage
Car gasCar = new Car(new GasEngine());
Car electricCar = new Car(new ElectricEngine());

gasCar.start();      // "Gas engine started"
electricCar.start(); // "Electric engine started"
```

✅ No code change needed — just inject a different engine.

---

## 🔹 Inheritance vs Composition: Side-by-Side

| Feature | Inheritance (`extends`) | Composition (`has-a`) |
|--------|--------------------------|------------------------|
| **Relationship** | "is-a" | "has-a" |
| **Coupling** | Tight | Loose |
| **Flexibility** | Low (fixed at compile time) | High (can swap at runtime) |
| **Code Reuse** | Yes | Yes |
| **Polymorphism** | Yes | Yes (via interfaces) |
| **Testing** | Harder (can’t mock parent) | Easier (mock dependencies) |
| **Best For** | True hierarchies, template logic | Most cases, especially business logic |

---

## 🔹 The Golden Rule

> 🟩 **"Favor composition over inheritance"** — *Design Patterns: Elements of Reusable Object-Oriented Software* (GoF)

This is one of the most important principles in OOP.

---

## 🔹 When to Use Inheritance

✅ Use inheritance **only** when:
- There is a **true "is-a" relationship**
- You want **polymorphic behavior**
- The parent class is **stable** (won’t change often)
- You’re using **template method** or **framework hooks**

```java
abstract class Task {
    public final void execute() {
        startTime = System.currentTimeMillis();
        doExecute(); // Subclass implements
        logTime();
    }
    protected abstract void doExecute();
}
```

✅ Template method: base class controls flow, subclass provides logic.

---

## 🔹 When to Use Composition

✅ Use composition when:
- One class **uses** another (e.g., `Car` uses `Engine`)
- You want **runtime flexibility**
- You’re building **business logic**
- You want **testable, maintainable code**

✅ Always prefer composition for **application logic**.

---

## 📌 Real-World Analogy

| | Inheritance | Composition |
|--|------------|-------------|
| **Car** | `Car extends Vehicle` | `Car has-a Engine, has-a GPS` |
| **UI** | `Button extends Component` | `Window has-a Button, has-a Menu` |
| **Game** | `Zombie extends Enemy` | `Player has-a Weapon, has-a Inventory` |

✅ Inheritance for **types**, composition for **behavior**.

---

## 📌 Interview Answer

> _"Inheritance is an 'is-a' relationship — like Dog is an Animal. Composition is a 'has-a' relationship — like Car has an Engine. I favor composition because it’s more flexible, loosely coupled, and easier to test. I can inject different engines at runtime. Inheritance is fragile — changes in parent break children. I use inheritance only for true hierarchies or template methods."_  

---

## ✅ Final Tip

> 🎯 In interviews, **draw it**:
> ```
> [Car] ──► [Engine]  (composition)
> [Dog] ──┐
>         ▼
>     [Animal]       (inheritance)
> ```

That shows **deep understanding of OOP design**.

---

