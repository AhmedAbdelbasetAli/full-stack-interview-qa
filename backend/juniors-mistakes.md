# ⚠️ Common Mistakes Junior Backend Java Developers Make  
**(And How to Avoid Them)**

This document highlights **15+ common mistakes** junior Java backend developers make — with **real examples**, **why they’re harmful**, and **how to fix them**.

Perfect for **code reviews**, **mentoring**, and **leveling up your skills**.

---

## 🔴 1. Returning `null` Instead of `Optional<T>` or Collections

### ❌ Bad
```java
public User findUser(Long id) {
    return userRepository.findById(id); // Returns null if not found
}
```

```java
public List<Order> getOrders(Long userId) {
    List<Order> orders = orderRepo.findByUserId(userId);
    return orders != null ? orders : Collections.emptyList(); // Still risky
}
```

### ❌ Why It’s Bad
- Forces callers to **check for `null`** everywhere
- Risk of `NullPointerException`
- API is **ambiguous**: "Can this return null?"

### ✅ Fix: Use `Optional` and Empty Collections
```java
public Optional<User> findUser(Long id) {
    return userRepository.findById(id); // Clear: may not exist
}

public List<Order> getOrders(Long userId) {
    return orderRepo.findByUserId(userId); // Return empty list, never null
}
```

> ✅ Spring Data JPA `findAll()` returns empty `List`, not `null`.

---

## 🔴 2. Not Handling Exceptions Properly

### ❌ Bad
```java
public void processPayment(Payment payment) {
    try {
        paymentService.charge(payment);
    } catch (Exception e) {
        // Silent catch — "swallowing" exception
    }
}
```

### ❌ Why It’s Bad
- **Silent failures** — no one knows something went wrong
- Impossible to debug
- Violates **fail-fast** principle

### ✅ Fix: Log or Propagate
```java
public void processPayment(Payment payment) {
    try {
        paymentService.charge(payment);
    } catch (PaymentException e) {
        log.error("Payment failed for user: {}", payment.getUserId(), e);
        throw new BusinessException("Payment failed", e);
    }
}
```

> ✅ Use **specific exceptions**, not `Exception`.

---

## 🔴 3. Business Logic in Controllers

### ❌ Bad
```java
@RestController
public class UserController {
    
    @PostMapping("/users")
    public ResponseEntity<?> createUser(@RequestBody UserRequest req) {
        if (req.getEmail() == null || !req.getEmail().contains("@")) {
            return badRequest().body("Invalid email");
        }
        if (userRepository.existsByEmail(req.getEmail())) {
            return conflict().body("Email already exists");
        }
        User user = new User();
        user.setEmail(req.getEmail());
        user.setRole("USER");
        user.setCreatedAt(Instant.now());
        userRepository.save(user);
        return ok(user);
    }
}
```

### ❌ Why It’s Bad
- Controller should **not contain business rules**
- Hard to test
- Violates **Single Responsibility Principle**

### ✅ Fix: Move to Service Layer
```java
@Service
public class UserService {
    public User createUser(UserRequest req) {
        validateEmail(req.getEmail());
        if (userRepository.existsByEmail(req.getEmail())) {
            throw new ConflictException("Email exists");
        }
        User user = new User(req.getEmail(), "USER", Instant.now());
        return userRepository.save(user);
    }
}

@RestController
public class UserController {
    @PostMapping("/users")
    public ResponseEntity<User> createUser(@RequestBody UserRequest req) {
        User user = userService.createUser(req);
        return ok(user);
    }
}
```

---

## 🔴 4. Not Using DTOs (Data Transfer Objects)

### ❌ Bad
```java
@Entity
public class User {
    @Id
    private Long id;
    private String email;
    private String password; // 🔥 Exposed in JSON!
    // getters, setters
}

@RestController
public class UserController {
    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id); // Returns full entity
    }
}
```

### ❌ Why It’s Bad
- Exposes **sensitive fields** (password, roles)
- Ties API to **database schema**
- No control over **serialization**

### ✅ Fix: Use DTOs
```java
public class UserDto {
    private Long id;
    private String email;
    // No password!
}

public UserDto toDto(User user) {
    return new UserDto(user.getId(), user.getEmail());
}
```

> ✅ Never expose entities directly via API.

---

## 🔴 5. Blocking the Main Thread (No Async)

### ❌ Bad
```java
@GetMapping("/report")
public Report generateReport() {
    // Takes 30 seconds
    return reportService.generate(); // Blocks Tomcat thread
}
```

### ❌ Why It’s Bad
- Limits **scalability** — one slow request blocks others
- Risk of **thread pool exhaustion**

### ✅ Fix: Use Async or Queue
```java
@Async
public CompletableFuture<Report> generateReportAsync() {
    return CompletableFuture.completedFuture(reportService.generate());
}

@GetMapping("/report")
public CompletableFuture<Report> getReport() {
    return reportService.generateReportAsync();
}
```

> ✅ Or better: use **message queue** (SQS, Kafka) for long jobs.

---

## 🔴 6. Hardcoding Values (URLs, Keys, etc.)

### ❌ Bad
```java
String apiUrl = "https://api.dev.com/users";
String apiKey = "dev123abc";
```

### ❌ Why It’s Bad
- Can’t deploy to **prod** without code change
- Secrets in code → **security risk**
- Violates **12-factor app** principles

### ✅ Fix: Use Configuration
```yaml
# application-prod.yml
external:
  api:
    url: https://api.prod.com
    key: ${API_KEY}
```

```java
@Value("${external.api.key}")
private String apiKey;
```

> ✅ Inject via environment variables or AWS Secrets Manager.

---

## 🔴 7. Not Validating Input

### ❌ Bad
```java
@PostMapping("/users")
public User createUser(@RequestBody UserRequest req) {
    return userService.create(req); // No validation
}
```

### ❌ Why It’s Bad
- Risk of **malformed data**
- **Security vulnerabilities** (e.g., injection)
- Poor **user experience**

### ✅ Fix: Use Bean Validation
```java
public class UserRequest {
    @NotBlank
    @Email
    private String email;

    @Size(min = 8)
    private String password;
}

// In controller
public ResponseEntity<?> createUser(@Valid @RequestBody UserRequest req) { ... }
```

> ✅ Combine with global exception handler.

---

## 🔴 8. Overusing `@Autowired` (Field Injection)

### ❌ Bad
```java
@Component
public class OrderService {
    @Autowired
    private PaymentService paymentService; // Field injection
}
```

### ❌ Why It’s Bad
- Hard to **test** (can’t inject mocks easily)
- Violates **dependency injection** best practices
- Can’t make dependencies `final`

### ✅ Fix: Use Constructor Injection
```java
@Service
public class OrderService {
    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

> ✅ Spring supports constructor injection by default (no `@Autowired` needed in Spring 4.3+).

---

## 🔴 9. Not Using Transactions Properly

### ❌ Bad
```java
@Transactional
public void transferFunds(Long fromId, Long toId, BigDecimal amount) {
    User from = userRepository.findById(fromId);
    User to = userRepository.findById(toId);

    from.setBalance(from.getBalance().subtract(amount));
    to.setBalance(to.getBalance().add(amount));

    // Business logic in between
    fraudService.checkTransfer(from, to, amount); // Might throw

    userRepository.save(from);
    userRepository.save(to);
}
```

### ❌ Why It’s Bad
- If `fraudService` throws, **partial update** may occur
- `@Transactional` only rolls back **unchecked exceptions**

### ✅ Fix: Keep transaction short, handle exceptions
```java
@Transactional
public void transferFunds(Long fromId, Long toId, BigDecimal amount) {
    userRepository.decrementBalance(fromId, amount);
    userRepository.incrementBalance(toId, amount);
}
```

> ✅ Or use **event sourcing** or **saga pattern** for complex workflows.

---

## 🔴 10. Ignoring Performance (N+1 Queries)

### ❌ Bad
```java
List<User> users = userRepository.findAll();
for (User user : users) {
    System.out.println(user.getOrders().size()); // Triggers DB query each time
}
```

### ❌ Why It’s Bad
- **N+1 queries**: 1 for users, N for orders
- Slow, scales poorly

### ✅ Fix: Use `JOIN FETCH` or DTO Projection
```java
@Query("SELECT u FROM User u JOIN FETCH u.orders")
List<User> findAllWithOrders();
```

> ✅ Use **Hibernate SQL logging** to catch N+1 early.

---

## 🔴 11. Using `public` on Everything

### ❌ Bad
```java
public class User {
    public Long id;
    public String email;
    public String password;
}
```

### ❌ Why It’s Bad
- No **encapsulation**
- Anyone can modify `password`
- Violates **OOP principles**

### ✅ Fix: Use `private` + Getters/Setters
```java
public class User {
    private Long id;
    private String email;
    private String password;

    // public getters and setters
}
```

---

## 🔴 12. Not Writing Tests (or Writing Bad Tests)

### ❌ Bad
```java
@Test
void testCreateUser() {
    // No arrange, no assert
    userService.createUser(new UserRequest("test@x.com", "pass"));
}
```

### ✅ Fix: Use AAA Pattern
```java
@Test
void shouldCreateUserWhenEmailIsUnique() {
    // Arrange
    UserRequest req = new UserRequest("new@x.com", "pass");

    // Act
    User user = userService.createUser(req);

    // Assert
    assertThat(user).isNotNull();
    assertThat(user.getEmail()).isEqualTo("new@x.com");
}
```

---

## ✅ Final Tips for Juniors

| Rule | Why |
|------|-----|
| ✅ **Write small, focused methods** | Easier to read, test, and debug |
| ✅ **Use meaningful names** | `calculateTax()` not `doStuff()` |
| ✅ **Refactor early** | Don’t wait for "big refactor" |
| ✅ **Ask for code reviews** | Learn from seniors |
| ✅ **Read error messages** | They tell you what’s wrong |
| ✅ **Use an IDE effectively** | Refactor, debug, navigate |

---

## 🎯 Interview Answer (Self-Awareness)

> _"Early in my career, I put business logic in controllers and returned nulls. I’ve learned to use services, Optional, DTOs, and proper validation. I now write clean, testable code with small methods and constructor injection. I still make mistakes — but I review, learn, and improve."_  

That shows **growth and professionalism**.

---

