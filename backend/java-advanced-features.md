# 🚀 Java Advanced Features Deep Dive  
**`Optional`, Lambdas, Streams, Records, Var, Sealed Classes & More**

This document covers **advanced Java features** that every **senior developer** must master — with **real-world examples**, **best practices**, **tricky gotchas**, and **interview-ready answers**.

---

## 1. `Optional<T>` – Avoid `null` and `NullPointerException`

> `Optional` is a **container** that may or may not contain a non-null value.

It’s a **functional approach** to handle optional values.

---

### ✅ Why Use `Optional`?

| Problem | `Optional` Solution |
|--------|---------------------|
| ❌ `null` means "value missing" | ✅ `Optional.empty()` is explicit |
| ❌ `NullPointerException` risk | ✅ Forces safe handling |
| ❌ Ambiguous API: "Can this return null?" | ✅ Method signature says `Optional<T>` |

---

### ✅ How to Use `Optional`

```java
public Optional<User> findUserById(Long id) {
    User user = userRepository.findById(id);
    return Optional.ofNullable(user); // null → empty()
}

// Safe consumption
Optional<User> userOpt = service.findUserById(123);
userOpt.ifPresent(user -> System.out.println("Found: " + user.getName()));

// With default
User user = userOpt.orElse(new User("Guest"));

// Or throw
User user = userOpt.orElseThrow(() -> new UserNotFoundException("ID: " + id));
```

---

### 🔹 Tricky: Don’t Use `Optional` in Fields or Parameters

❌ **Bad**:
```java
class Person {
    private Optional<String> email; // ❌ Never do this
}

public void process(Optional<User> user) { ... } // ❌ Avoid in parameters
```

✅ **Good**:
```java
class Person {
    private String email; // Use null if needed
}

// Return Optional from methods
public Optional<User> findById(Long id) { ... }
```

> ✅ `Optional` is for **return types**, not fields or parameters.

---

### 🔹 Tricky: Don’t Call `get()` Without Checking

❌ **Bad**:
```java
Optional<User> opt = findUser();
User user = opt.get(); // ❌ Throws NoSuchElementException if empty
```

✅ **Good**:
```java
User user = opt.orElse(defaultUser);
// or
opt.ifPresent(this::sendWelcomeEmail);
```

---

### 📌 Interview Answer
> _"I use Optional to make APIs explicit about optional results. It prevents NPEs and forces callers to handle missing values. I use orElse, orElseThrow, or ifPresent — never get() without check. I don’t use it in fields or parameters — only as return types."_  

---

## 2. Lambda Expressions – Functional Programming in Java

> Lambda expressions allow you to pass **functions as arguments**.

```java
(String s) -> s.length()
```

---

### ✅ Functional Interfaces

| Interface | Method | Example |
|--------|--------|--------|
| `Predicate<T>` | `boolean test(T)` | `user -> user.isActive()` |
| `Function<T,R>` | `R apply(T)` | `user -> user.getName()` |
| `Supplier<T>` | `T get()` | `() -> new User()` |
| `Consumer<T>` | `void accept(T)` | `user -> log.info(user)` |
| `UnaryOperator<T>` | `T apply(T)` | `str -> str.toUpperCase()` |

---

### ✅ Real-World Use
```java
// Filter active users
List<User> active = users.stream()
    .filter(user -> user.isActive())
    .collect(Collectors.toList());

// Map to names
List<String> names = users.stream()
    .map(User::getName)
    .collect(Collectors.toList());
```

---

### 🔹 Tricky: Variable Capture – Must Be "Effectively Final"

❌ **Bad**:
```java
int count = 0;
users.forEach(user -> {
    count++; // ❌ Compilation error
});
```

✅ **Good**:
```java
AtomicInteger count = new AtomicInteger(0);
users.forEach(user -> count.incrementAndGet());
```

---

### 📌 Interview Answer
> _"Lambdas let me write concise, functional code. I use Predicate for filtering, Function for transformation. I avoid side effects and remember that captured variables must be effectively final. Lambdas work with functional interfaces and are key to streams."_  

---

## 3. Streams API – Functional Data Processing

> `Stream<T>` allows **functional-style operations** on collections: `filter`, `map`, `reduce`, `collect`.

---

### ✅ Example: Process Users
```java
List<String> topUserNames = users.stream()
    .filter(User::isActive)
    .filter(user -> user.getScore() > 90)
    .sorted(comparing(User::getScore).reversed())
    .map(User::getName)
    .limit(10)
    .collect(Collectors.toList());
```

✅ **Lazy evaluation**: Intermediate ops (`filter`, `map`) don’t run until terminal op (`collect`, `forEach`)

---

### 🔹 Tricky: Streams Are Not Reusable

❌ **Bad**:
```java
Stream<User> stream = users.stream().filter(User::isActive);
stream.forEach(...); // OK
stream.count();       // ❌ IllegalStateException: stream closed
```

✅ **Fix**: Recreate or collect early.

---

### 🔹 Tricky: Don’t Use Streams for Simple Loops

❌ **Overkill**:
```java
list.stream().forEach(System.out::println);
```

✅ **Better**:
```java
for (String s : list) System.out.println(s);
```

✅ Use streams when you chain **multiple operations**.

---

### 📌 Interview Answer
> _"I use streams for complex data transformations — filtering, mapping, sorting, reducing. I remember they’re lazy and single-use. I avoid them for simple loops. I prefer method references (User::getName) for readability. Streams make code declarative and less error-prone."_  

---

## 4. Records (Java 14+) – Immutable Data Classes

> `record` is a **shorthand for immutable data carriers**.

```java
public record User(Long id, String name, String email) {}
```

✅ Automatically generates:
- Constructor
- `equals()`, `hashCode()`, `toString()`
- Accessor methods (`id()`, `name()`, `email()`)

---

### ✅ When to Use Records

| Use Case | Why |
|--------|-----|
| ✅ DTOs | No boilerplate |
| ✅ Data transfer | Immutable, safe |
| ✅ Pattern matching (Java 17+) | `if (obj instanceof Point(int x, int y))` |

---

### 🔹 Tricky: Records Are Final and Immutable

```java
User user = new User(1L, "Alice", "a@x.com");
// user.name = "Bob"; // ❌ Compile error
```

✅ Can add methods:
```java
public record User(Long id, String name, String email) {
    public boolean isGmail() {
        return email.endsWith("@gmail.com");
    }
}
```

---

### 📌 Interview Answer
> _"Records are perfect for DTOs and data carriers. They reduce boilerplate, ensure immutability, and generate equals/hashCode. I use them when I need a simple, immutable class. They’re final, so no inheritance, but I can add helper methods."_  

---

## 5. `var` (Local Variable Type Inference) – Reduce Verbosity

> `var` lets the compiler **infer the type** of a local variable.

```java
var list = new ArrayList<String>(); // ArrayList<String>
var user = userRepository.findById(123); // User
```

---

### ✅ Best Practices

| Rule | Why |
|------|-----|
| ✅ Use when type is obvious | `var list = new ArrayList<String>();` |
| ❌ Don’t use with literals | `var x = 1` → `int`? `long`? |
| ❌ Don’t use in fields/parameters | Not allowed |
| ✅ Use in streams | `var names = users.stream().map(User::getName).toList();` |

---

### 🔹 Tricky: Overuse Hides Intent

❌ **Bad**:
```java
var result = service.process(data);
// What is result? Map? List? Object?
```

✅ **Good**:
```java
List<User> users = service.findAll();
```

---

### 📌 Interview Answer
> _"I use var to reduce verbosity when the type is clear from the right-hand side. I avoid it with literals or when it harms readability. It’s great for long generics and streams. But I don’t overuse it — code should still be self-documenting."_  

---

## 6. Sealed Classes (Java 17+) – Controlled Inheritance

> `sealed` classes **restrict which classes can extend them**.

```java
public sealed interface Shape permits Circle, Rectangle, Triangle {}

public final class Circle implements Shape { ... }
public final class Rectangle implements Shape { ... }
public non-sealed class Triangle implements Shape { ... } // Can be extended
```

---

### ✅ Why Use Sealed Classes?

| Benefit | How |
|--------|-----|
| ✅ **Exhaustive Pattern Matching** | `switch` can cover all cases |
| ✅ **Model Algebraic Data Types** | Like enums, but with data |
| ✅ **Secure Domain Models** | Prevent unexpected subclasses |

---

### ✅ With `switch` (Java 17+)
```java
double area = switch (shape) {
    case Circle c -> Math.PI * c.radius() * c.radius();
    case Rectangle r -> r.width() * r.height();
    case Triangle t -> 0.5 * t.base() * t.height();
};
```

✅ No `default` needed — compiler knows all cases.

---

### 📌 Interview Answer
> _"Sealed classes let me restrict inheritance to a known set of types. I use them with records and pattern matching to model domains like shapes or ASTs. The compiler can verify exhaustiveness in switches — no default needed. It’s powerful for DSLs and functional modeling."_  

---

## 7. Other Advanced Features

### ✅ `instanceof` Pattern Matching (Java 16+)
```java
if (obj instanceof String s) {
    System.out.println(s.toUpperCase());
}
```

✅ No cast needed.

---

### ✅ `switch` Expressions (Java 14+)
```java
String size = switch (n) {
    case 1, 2, 3 -> "small";
    case 4, 5, 6 -> "medium";
    default -> "large";
};
```

✅ No fall-through, returns value.

---

### ✅ Text Blocks (Java 15+)
```java
String json = """
    {
        "name": "Alice",
        "email": "a@x.com"
    }
    """;
```

✅ Multi-line strings without `\n`.

---

## ✅ Final Tips

| Rule | Why |
|------|-----|
| ✅ Use `Optional` for return types only | Not fields or parameters |
| ✅ Prefer method references over lambdas | `User::getName` vs `u -> u.getName()` |
| ✅ Use `var` wisely | Don’t sacrifice readability |
| ✅ Use `record` for DTOs | Less boilerplate, immutable |
| ✅ Use `sealed` + `switch` for domain modeling | Exhaustive, safe |

---
