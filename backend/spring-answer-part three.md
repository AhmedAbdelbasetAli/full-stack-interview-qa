# 🌱 Spring Boot Deep Dive (Part 3)  
**Exception Handling, Request Mapping, and Spring Data JPA**

This document continues the deep dive into **Spring Boot** — covering **global exception handling**, **request mapping**, and **data access**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 11. How do you handle exceptions globally in Spring Boot?

> Use **`@ControllerAdvice`** and **`@ExceptionHandler`** to create a **global exception handler** — no need to handle exceptions in every controller.

---

### 🔹 11.1 Why Global Exception Handling?

Without it:
```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    if (id <= 0) {
        throw new IllegalArgumentException("Invalid ID");
    }
    // ...
}
```

✅ You’d have to wrap every method in try-catch.

With global handling:
- All exceptions are caught in **one place**
- Return **consistent error responses**
- Clean controller code

---

### 🔹 11.2 Example: Global Exception Handler

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(
            "NOT_FOUND", 
            ex.getMessage(), 
            System.currentTimeMillis()
        );
        return ResponseEntity.status(404).body(error);
    }

    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<ErrorResponse> handleBadRequest(IllegalArgumentException ex) {
        ErrorResponse error = new ErrorResponse(
            "BAD_REQUEST", 
            ex.getMessage(), 
            System.currentTimeMillis()
        );
        return ResponseEntity.status(400).body(error);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(Exception ex) {
        ErrorResponse error = new ErrorResponse(
            "INTERNAL_ERROR", 
            "An unexpected error occurred", 
            System.currentTimeMillis()
        );
        return ResponseEntity.status(500).body(error);
    }
}

// Error Response DTO
record ErrorResponse(String code, String message, long timestamp) {}
```

✅ `@ControllerAdvice` → applies to **all controllers**
✅ Specific handlers first → generic last

---

### 🔹 11.3 Spring Boot’s Default Behavior

- Spring Boot uses `@ControllerAdvice` internally
- Converts common exceptions to proper HTTP status codes
- With `spring-boot-starter-web`, you get **automatic JSON error responses**

```json
{
  "timestamp": "2025-04-05T12:00:00",
  "status": 404,
  "error": "Not Found",
  "path": "/api/users/999"
}
```

✅ But for **custom errors**, use `@ControllerAdvice`

---

### 📌 Interview Answer

> _"I use `@ControllerAdvice` with `@ExceptionHandler` to handle exceptions globally. It keeps controllers clean and ensures consistent error responses. I handle specific exceptions like `ResourceNotFoundException` with 404, and generic ones with 500. I return a structured `ErrorResponse` DTO. This is essential for production APIs."_  

---

## 12. What is `@RequestMapping`? What are some of its common attributes?

> `@RequestMapping` is a **core annotation** that maps HTTP requests to handler methods.

It’s the **parent** of `@GetMapping`, `@PostMapping`, etc.

---

### 🔹 12.1 Basic Usage

```java
@RequestMapping("/api/users")
public class UserController {

    @RequestMapping(method = RequestMethod.GET)
    public List<User> getAll() { ... }

    @RequestMapping(path = "/{id}", method = RequestMethod.GET)
    public User getById(@PathVariable Long id) { ... }
}
```

---

### 🔹 12.2 Common Attributes

| Attribute | Purpose | Example |
|---------|--------|--------|
| `value` / `path` | URL pattern | `@RequestMapping("/users")` |
| `method` | HTTP method | `method = RequestMethod.POST` |
| `params` | Request parameters | `params = "action=save"` |
| `headers` | HTTP headers | `headers = "Content-Type=application/json"` |
| `consumes` | Request body type | `consumes = "application/json"` |
| `produces` | Response type | `produces = "application/json"` |

---

### 🔹 12.3 Shortcuts (Recommended)

Instead of:
```java
@RequestMapping(value = "/users", method = RequestMethod.GET)
```

Use:
```java
@GetMapping("/users")
@PostMapping("/users")
@PutMapping("/users/{id}")
@DeleteMapping("/users/{id}")
```

✅ Cleaner, more readable.

---

### 📌 Interview Answer

> _"`@RequestMapping` maps HTTP requests to controller methods. Common attributes include `path`, `method`, `consumes`, and `produces`. I use it at the class level to define a base path, and method-level shortcuts like `@GetMapping` for clarity. It supports complex conditions like headers and params, but I keep it simple in most cases."_  

---

## 13. What is `@PathVariable` and `@RequestParam`? When would you use each?

| Annotation | Purpose | URL Example |
|----------|--------|------------|
| `@PathVariable` | Extract value from **URL path** | `/users/123` → `123` |
| `@RequestParam` | Extract value from **query string** | `/users?role=admin` → `admin` |

---

### 🔹 13.1 `@PathVariable` – Path Variables

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    return userService.findById(id);
}

@GetMapping("/users/{id}/orders/{orderId}")
public Order getOrder(
    @PathVariable Long id, 
    @PathVariable Long orderId
) {
    // ...
}
```

✅ Use when:
- Resource is identified by path
- RESTful URLs (`/users/123`, `/orders/456`)

---

### 🔹 13.2 `@RequestParam` – Query Parameters

```java
@GetMapping("/users")
public List<User> getUsers(
    @RequestParam String role,
    @RequestParam(required = false) Integer age,
    @RequestParam(defaultValue = "0") int page
) {
    // ...
}
```

✅ Use when:
- Filtering, sorting, pagination
- Optional parameters
- Not part of the resource identity

> 🔗 Example: `/users?role=admin&age=30&page=1`

---

### 🔹 13.3 Common Attributes

| Attribute | `@PathVariable` | `@RequestParam` |
|---------|----------------|----------------|
| `required` | ✅ (default `true`) | ✅ (default `true`) |
| `defaultValue` | ❌ No | ✅ Yes |
| `name` / `value` | ✅ Rename variable | ✅ Rename param |

---

### 📌 Interview Answer

> _"`@PathVariable` extracts values from the URL path — like `/users/123`. `@RequestParam` extracts query parameters — like `?role=admin`. I use `@PathVariable` for resource IDs in REST APIs, and `@RequestParam` for filters, pagination, or optional inputs. `@RequestParam` supports `defaultValue` and `required = false`, which is great for flexible APIs."_  

---

## 14. What is `@RequestBody` and `@ResponseBody`?

| Annotation | Purpose | Direction |
|----------|--------|---------|
| `@RequestBody` | Deserialize HTTP request body to Java object | **Inbound** |
| `@ResponseBody` | Serialize Java object to HTTP response body | **Outbound** |

---

### 🔹 14.1 `@RequestBody` – Read JSON into Object

```java
@PostMapping("/users")
public User createUser(@RequestBody User user) {
    return userService.save(user);
}
```

✅ Spring uses **Jackson** to convert JSON to `User` object

> Input:
```json
{ "name": "Alice", "email": "alice@example.com" }
```

---

### 🔹 14.2 `@ResponseBody` – Write Object to JSON

```java
@GetMapping("/users/{id}")
@ResponseBody
public User getUser(@PathVariable Long id) {
    return userService.findById(id);
}
```

✅ Returns:
```json
{ "name": "Alice", "email": "alice@example.com" }
```

---

### 🔹 14.3 `@RestController` = `@Controller + @ResponseBody`

```java
@RestController
public class UserController {
    // All methods automatically have @ResponseBody
}
```

✅ No need to annotate every method.

---

### 📌 Interview Answer

> _"`@RequestBody` deserializes the HTTP request body (usually JSON) into a Java object using Jackson. `@ResponseBody` serializes the return value to the response body. In REST APIs, I use `@RequestBody` for input and rely on `@RestController` to apply `@ResponseBody` automatically. This makes APIs clean and JSON-first."_  

---

## 15. What is Spring Data JPA? How does it simplify database operations?

> **Spring Data JPA** is a module that **simplifies data access** by reducing boilerplate code for JPA repositories.

It’s **not JPA** — it’s a **layer on top of JPA/Hibernate**.

---

### 🔹 15.1 Problem: Without Spring Data JPA

```java
@Repository
public class UserRepository {
    
    @PersistenceContext
    private EntityManager em;

    public User findById(Long id) {
        return em.find(User.class, id);
    }

    public List<User> findAll() {
        return em.createQuery("SELECT u FROM User u", User.class)
                 .getResultList();
    }

    public User save(User user) {
        if (user.getId() == null) {
            em.persist(user);
        } else {
            em.merge(user);
        }
        return user;
    }
}
```

❌ Too much boilerplate.

---

### 🔹 15.2 Solution: With Spring Data JPA

```java
public interface UserRepository extends JpaRepository<User, Long> {
    // All CRUD methods auto-implemented
}
```

✅ You get for free:
- `save()`, `saveAll()`
- `findById()`, `findAll()`
- `deleteById()`, `count()`
- `existsById()`

---

### 🔹 15.3 Query Methods by Convention

```java
public interface UserRepository extends JpaRepository<User, Long> {
    
    List<User> findByRole(String role);
    List<User> findByRoleAndActive(String role, boolean active);
    User findByEmail(String email);
    List<User> findTop3ByOrderByCreatedAtDesc();
}
```

✅ Spring parses method names and generates queries automatically.

---

### 🔹 15.4 Custom Queries

```java
public interface UserRepository extends JpaRepository<User, Long> {
    
    @Query("SELECT u FROM User u WHERE u.email LIKE %:domain%")
    List<User> findByDomain(@Param("domain") String domain);
}
```

---

### 🔹 15.5 Key Benefits

| Benefit | How |
|--------|-----|
| ✅ No boilerplate | `JpaRepository` provides CRUD |
| ✅ Type-safe queries | Method names or `@Query` |
| ✅ Pagination & Sorting | Built-in support |
| ✅ Reactive Repositories | `ReactiveCrudRepository` |
| ✅ Easy testing | In-memory DB support |

---

### 📌 Interview Answer

> _"Spring Data JPA reduces boilerplate by providing auto-implemented CRUD methods via `JpaRepository`. I just define an interface — no implementation needed. It supports query methods by convention (e.g., `findByEmail`), custom `@Query`, and pagination. It works with Hibernate but saves me from writing repetitive DAO code. I use it in every Spring Boot project."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use Spring Data JPA for repositories, `@RequestBody` for input, and `@ControllerAdvice` for global exception handling."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---


Just say: _"Let’s do [topic]"_

You're mastering Spring Boot at a **senior level**. 💪
