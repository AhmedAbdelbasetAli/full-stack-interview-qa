# 🧱 Software Design & Clean Code Deep Dive  
**Coupling, Cohesion, Magic Values, Imports, and Layering**

This document provides a **comprehensive, in-depth explanation** of essential **software design principles** — critical for writing **maintainable, clean, and scalable code**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Code examples (Java)
- ✅ Best practices
- ✅ Interview-ready answers

---

## 1. What is the difference between coupling and cohesion?

> **Coupling** and **cohesion** are two **fundamental design principles** that measure **how well a system is structured**.

| Concept | Definition | Goal |
|--------|-----------|------|
| **Coupling** | How **dependent** modules are on each other | ✅ **Low coupling** |
| **Cohesion** | How **related** the responsibilities of a single module are | ✅ **High cohesion** |

---

### 🔹 1.1 Coupling – "How Connected Are Components?"

> **Tight coupling** = One class **directly depends** on another’s implementation.

❌ Bad: Hard to change, test, or reuse.

```java
// ❌ Tightly coupled
class OrderProcessor {
    private PaymentGateway gateway = new PayPalGateway(); // Direct dependency

    public void process(Order order) {
        gateway.charge(order.getAmount());
    }
}
```

✅ **Loose coupling** = Dependencies are **abstracted** (e.g., via interfaces).

```java
// ✅ Loosely coupled
interface PaymentGateway {
    void charge(double amount);
}

class OrderProcessor {
    private PaymentGateway gateway; // Abstract dependency

    public OrderProcessor(PaymentGateway gateway) {
        this.gateway = gateway;
    }

    public void process(Order order) {
        gateway.charge(order.getAmount());
    }
}
```

✅ Now you can swap `PayPalGateway` with `StripeGateway` easily.

---

### 🔹 1.2 Cohesion – "Does This Class Have a Single Purpose?"

> **High cohesion** = A class has **one, well-defined responsibility**.

✅ Good: Easier to understand, test, and maintain.

```java
// ✅ High cohesion
class UserService {
    public User createUser(String name, String email) { ... }
    public User findById(Long id) { ... }
}

class EmailService {
    public void sendWelcomeEmail(User user) { ... }
}
```

❌ **Low cohesion** = A class does **many unrelated things**.

```java
// ❌ Low cohesion
class GodClass {
    public void createUser() { ... }
    public void sendEmail() { ... }
    public void generateReport() { ... }
    public void connectToDatabase() { ... } // Mix of concerns
}
```

---

### 📌 Interview Answer

> _"Coupling measures how connected components are — low coupling is better for flexibility. Cohesion measures how focused a component is — high cohesion means single responsibility. I design classes with high cohesion (one job) and low coupling (dependencies via interfaces), making the system modular and maintainable."_  

---

## 2. Why is tight coupling problematic?

> **Tight coupling** creates **rigid, fragile, and untestable code**.

---

### 🔹 2.1 Problems with Tight Coupling

| Problem | Explanation |
|--------|-------------|
| ❌ **Hard to Test** | Can't easily mock dependencies (e.g., real DB, external API) |
| ❌ **Hard to Change** | Change in one class forces changes in others |
| ❌ **Hard to Reuse** | Can't reuse a class without dragging its dependencies |
| ❌ **Fragile System** | One change breaks multiple parts |
| ❌ **Violates Open/Closed Principle** | Not open for extension, closed for modification |

---

### 🔹 2.2 Example: Tight Coupling in Tests

```java
// ❌ Hard to test
class OrderService {
    private Database db = new MySQLDatabase(); // Direct instantiation

    public Order createOrder(Order order) {
        db.save(order);
        return order;
    }
}

// Test must use real DB!
@Test
void testCreateOrder() {
    OrderService service = new OrderService(); // Uses real MySQL!
    // Can't inject mock DB
}
```

✅ Fix with **Dependency Injection**:

```java
class OrderService {
    private Database db;

    public OrderService(Database db) {
        this.db = db;
    }

    public Order createOrder(Order order) {
        db.save(order);
        return order;
    }
}

@Test
void testCreateOrder() {
    Database mockDb = mock(Database.class);
    OrderService service = new OrderService(mockDb);
    // ✅ Easy to test
}
```

---

### 📌 Interview Answer

> _"Tight coupling makes code hard to test, change, and reuse. If a class directly creates its dependencies, you can't replace them with mocks or alternatives. I avoid it using dependency injection and programming to interfaces. This makes the system flexible and testable."_  

---

## 3. What are magic numbers and magic strings? How do you avoid them?

> **Magic numbers/strings** are **hardcoded values** with **no explanation** — they make code **unreadable and error-prone**.

---

### 🔹 3.1 Examples

```java
// ❌ Magic number
if (user.getRole() == 3) { ... } // What is 3?

// ❌ Magic string
String status = "ACTV"; // What does "ACTV" mean?

// ❌ Magic number in loop
for (int i = 0; i < 50; i++) { ... } // Why 50?
```

---

### 🔹 3.2 How to Avoid Them

✅ Use **named constants**:

```java
public class UserRole {
    public static final int ADMIN = 1;
    public static final int USER = 2;
    public static final int MODERATOR = 3;
}

public class UserStatus {
    public static final String ACTIVE = "ACTV";
    public static final String INACTIVE = "INACT";
}

// ✅ Now readable
if (user.getRole() == UserRole.MODERATOR) { ... }

String status = UserStatus.ACTIVE;
```

✅ Or use **enums** (better):

```java
public enum UserRole {
    USER, MODERATOR, ADMIN
}

public enum UserStatus {
    ACTIVE, INACTIVE
}

// ✅ Type-safe and self-documenting
if (user.getRole() == UserRole.MODERATOR) { ... }
```

✅ For loop limits:

```java
private static final int MAX_LOGIN_ATTEMPTS = 5;

for (int i = 0; i < MAX_LOGIN_ATTEMPTS; i++) { ... }
```

---

### 📌 Interview Answer

> _"Magic numbers and strings are hardcoded values like '3' or 'ACTV' that lack context. They make code hard to read and maintain. I avoid them by using named constants or enums — they make intent clear and centralize changes. For example, `UserRole.ADMIN` is better than `3`."_  

---

## 4. How do you organize imports and dependencies in a clean way?

> Clean import and dependency management improves **readability**, **maintainability**, and **build reliability**.

---

### 🔹 4.1 Organizing Imports (Java)

✅ **Group imports** in this order:
1. `java.*`
2. `javax.*`
3. Third-party (e.g., `org.springframework`)
4. Your project packages

```java
import java.util.List;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;

import com.bank.app.service.UserService;
import com.bank.app.model.User;
```

✅ Use **IDE shortcuts** to sort and remove unused imports.

❌ Avoid wildcard imports:
```java
import java.util.*; // ❌ Can cause naming conflicts
```

---

### 🔹 4.2 Managing Dependencies (Maven/Gradle)

✅ **Group by purpose** in `pom.xml`:

```xml
<!-- Core -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>

<!-- Web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- Data -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- Test -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

✅ Use **dependency management** (`dependencyManagement`) to control versions.

---

### 🔹 4.3 Best Practices

| Rule | Why |
|-----|-----|
| ✅ Remove unused imports | Cleaner code, faster compile |
| ✅ Use `scope` (compile, test, provided) | Correct dependency lifecycle |
| ✅ Avoid circular dependencies | Break modules cleanly |
| ✅ Use BOMs (Bill of Materials) | Version consistency |

---

### 📌 Interview Answer

> _"I organize imports by grouping: java, javax, third-party, then my packages. I avoid wildcards and remove unused imports. For dependencies, I group them by function (web, data, test) in pom.xml and use BOMs for version control. This keeps builds clean, predictable, and maintainable."_  

---

## 5. What is the purpose of layers in an application (e.g., controller, service, repository)?

> **Layering** (or **n-tier architecture**) separates concerns to make the system **modular, testable, and maintainable**.

---

### 🔹 5.1 Common Layers in Spring Boot

```
┌─────────────────┐
│   Controller    │ ← Handles HTTP requests (REST)
└─────────────────┘
         ↓
┌─────────────────┐
│     Service     │ ← Business logic
└─────────────────┘
         ↓
┌─────────────────┐
│   Repository    │ ← Data access (DB)
└─────────────────┘
         ↓
┌─────────────────┐
│     Database    │
└─────────────────┘
```

---

### 🔹 5.2 Responsibilities

| Layer | Purpose | Example |
|------|--------|--------|
| **Controller** | Handle HTTP requests/responses | `@GetMapping("/users")` |
| **Service** | Encapsulate business logic | Validate, calculate, orchestrate |
| **Repository** | Abstract data access | `JpaRepository`, `@Query` |
| **Model/Entity** | Represent data | `User`, `Order` classes |

---

### 🔹 5.3 Example: User Registration

```java
// Controller
@RestController
public class UserController {
    @Autowired
    private UserService userService;

    @PostMapping("/register")
    public ResponseEntity<User> register(@RequestBody UserRequest req) {
        User user = userService.register(req);
        return ResponseEntity.ok(user);
    }
}

// Service
@Service
public class UserService {
    @Autowired
    private UserRepository repo;
    @Autowired
    private EmailService emailService;

    @Transactional
    public User register(UserRequest req) {
        if (repo.existsByEmail(req.getEmail())) {
            throw new IllegalArgumentException("Email exists");
        }
        User user = new User(req.getName(), req.getEmail());
        User saved = repo.save(user);
        emailService.sendWelcome(saved);
        return saved;
    }
}

// Repository
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    boolean existsByEmail(String email);
}
```

✅ Each layer has **one responsibility**.

---

### 📌 Interview Answer

> _"Layers separate concerns: Controller handles HTTP, Service holds business logic, Repository manages data access. This makes code modular, testable, and maintainable. I can test the service without HTTP, or swap databases without changing business logic. It’s a proven pattern for clean, scalable applications."_  

---

## ✅ Final Tip

> 🎯 In interviews, **draw the layer diagram** and say:
> _"I follow separation of concerns — each layer has a single job. This makes the system easy to test, change, and scale."_  

That shows **maturity in software design**.



Just say: _"Let’s do [topic]"_

You're mastering **clean code and design** like a senior engineer. 💪
