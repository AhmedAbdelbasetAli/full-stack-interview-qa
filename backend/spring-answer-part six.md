# 🌱 Spring Boot Deep Dive (Part 6)  
**Validation, Testing, Actuator, CORS, and Database Connection**

This document covers **essential production-grade practices** in Spring Boot — critical for **real-world applications** and **senior engineering roles**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 26. How do you validate request data in Spring Boot?

> Use **Bean Validation (JSR-380 / Hibernate Validator)** with annotations like `@NotNull`, `@Size`, `@Email`, etc.

Validation happens **automatically** when you use `@Valid` on request objects.

---

### 🔹 26.1 Step 1: Add Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

✅ Included in `spring-boot-starter-web` — no need to add separately.

---

### 🔹 26.2 Step 2: Annotate DTO

```java
public class UserRequest {
    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Invalid email format")
    @NotBlank(message = "Email is required")
    private String email;

    @Min(value = 18, message = "Age must be at least 18")
    private int age;

    @Size(min = 6, message = "Password must be at least 6 characters")
    private String password;

    // Getters and setters
}
```

---

### 🔹 26.3 Step 3: Use `@Valid` in Controller

```java
@PostMapping("/users")
public ResponseEntity<User> createUser(@Valid @RequestBody UserRequest request) {
    User user = userService.create(request);
    return ResponseEntity.ok(user);
}
```

✅ If validation fails → **400 Bad Request** + error details

---

### 🔹 26.4 Global Exception Handling for Validation Errors

```java
@RestControllerAdvice
public class ValidationHandler {
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error -> 
            errors.put(error.getField(), error.getDefaultMessage())
        );
        return ResponseEntity.badRequest().body(errors);
    }
}
```

✅ Returns:
```json
{
  "email": "Invalid email format",
  "age": "Age must be at least 18"
}
```

---

### 📌 Interview Answer

> _"I use Bean Validation with `@Valid` and annotations like `@NotBlank`, `@Email`, `@Min`. I apply `@Valid` to `@RequestBody` DTOs in controllers. If validation fails, Spring throws `MethodArgumentNotValidException`, which I handle globally with `@RestControllerAdvice` to return a clean 400 response with field-level errors. This ensures clean, user-friendly error handling."_  

---

## 27. How do you test a Spring Boot application?

> Use **Spring Boot Test Slices** for fast, focused testing:
- ✅ `@WebMvcTest` – Controllers
- ✅ `@DataJpaTest` – Repositories
- ✅ `@JsonTest` – JSON serialization
- ✅ `@SpringBootTest` – Full context (integration)

---

### 🔹 27.1 Example: Controller Test (`@WebMvcTest`)

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

    @Autowired
    private MockMvc mvc;

    @MockBean
    private UserService userService;

    @Test
    void shouldReturnUser() throws Exception {
        when(userService.findById(1L)).thenReturn(new User("Alice"));

        mvc.perform(get("/api/users/1"))
           .andExpect(status().isOk())
           .andExpect(jsonPath("$.name").value("Alice"));
    }
}
```

✅ `@MockBean` → mocks `UserService`  
✅ `MockMvc` → simulates HTTP requests

---

### 🔹 27.2 Example: Repository Test (`@DataJpaTest`)

```java
@DataJpaTest
class UserRepositoryTest {

    @Autowired
    private TestEntityManager entityManager;

    @Autowired
    private UserRepository repo;

    @Test
    void shouldFindUserByEmail() {
        User user = new User("Alice", "alice@example.com");
        entityManager.persistAndFlush(user);

        Optional<User> found = repo.findByEmail("alice@example.com");
        assertThat(found).isPresent();
        assertThat(found.get().getName()).isEqualTo("Alice");
    }
}
```

✅ Uses **in-memory H2 database**

---

### 🔹 27.3 Example: Full Integration Test (`@SpringBootTest`)

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class FullIntegrationTest {

    @LocalServerPort
    private int port;

    @Test
    void shouldCreateUser() throws Exception {
        String json = "{ \"name\": \"Bob\", \"email\": \"bob@example.com\" }";

        String result = given()
            .body(json)
            .contentType(ContentType.JSON)
            .when()
            .post("/api/users")
            .then()
            .statusCode(201)
            .extract().asString();
    }
}
```

✅ Uses **real server on random port**  
✅ Requires `spring-boot-starter-test`

---

### 📌 Interview Answer

> _"I use Spring Boot’s test slices for fast, focused tests. `@WebMvcTest` for controllers with `MockMvc`, `@DataJpaTest` for repositories with in-memory DB, and `@SpringBootTest` for full integration. I mock dependencies with `@MockBean` and use `TestEntityManager` for persistence tests. This ensures fast unit tests and reliable integration tests."_  

---

## 28. What is Actuator in Spring Boot? Why is it useful?

> **Spring Boot Actuator** provides **production-ready endpoints** for monitoring and managing your app.

It’s like a **control panel** for your running application.

---

### 🔹 28.1 Add Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

---

### 🔹 28.2 Key Endpoints

| Endpoint | Purpose |
|--------|--------|
| `GET /actuator/health` | Health check (UP/DOWN) |
| `GET /actuator/info` | App info (version, git) |
| `GET /actuator/metrics` | System metrics (heap, threads) |
| `GET /actuator/env` | Environment variables |
| `GET /actuator/beans` | All Spring beans |
| `GET /actuator/mappings` | All request mappings |
| `GET /actuator/httptrace` | Recent HTTP requests |
| `POST /actuator/shutdown` | Graceful shutdown (disabled by default) |

---

### 🔹 28.3 Configure Actuator

```yaml
# application.yml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,env,beans
  endpoint:
    health:
      show-details: always
    shutdown:
      enabled: true
```

✅ **Never expose all endpoints in production!**

---

### 🔹 28.4 Custom Actuator Endpoint

```java
@Component
@Endpoint(id = "status")
public class StatusEndpoint {

    @ReadOperation
    public Map<String, Object> getStatus() {
        return Map.of(
            "status", "OK",
            "timestamp", System.currentTimeMillis()
        );
    }
}
```

✅ Accessible at `/actuator/status`

---

### 📌 Interview Answer

> _"Actuator provides production monitoring endpoints like `/health`, `/metrics`, and `/env`. I use it for health checks, debugging, and performance monitoring. I expose only necessary endpoints in production and secure them with Spring Security. I also create custom endpoints for business status. It’s essential for observability in microservices."_  

---

## 29. How do you enable CORS in a Spring Boot application?

> **CORS (Cross-Origin Resource Sharing)** allows your API to be accessed from different domains (e.g., `localhost:3000` → `localhost:8080`).

---

### 🔹 29.1 Global CORS Configuration

```java
@Configuration
public class CorsConfig {

    @Bean
    public CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowedOriginPatterns(Arrays.asList("http://localhost:*", "https://myapp.com"));
        config.setAllowedMethods(Arrays.asList("GET", "POST", "PUT", "DELETE"));
        config.setAllowedHeaders(Arrays.asList("*"));
        config.setAllowCredentials(true);

        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/api/**", config);
        return source;
    }
}
```

✅ `setAllowedOriginPatterns` → supports wildcards (Spring 5.3+)  
✅ `setAllowCredentials(true)` → for cookies/auth

---

### 🔹 29.2 Per-Controller CORS

```java
@CrossOrigin(origins = "http://localhost:3000")
@RestController
@RequestMapping("/api/users")
public class UserController { ... }
```

---

### 🔹 29.3 Via `application.yml` (Limited)

```yaml
spring:
  web:
    cors:
      allowed-origins: "http://localhost:3000"
      allowed-methods: "*"
      allowed-headers: "*"
      path-pattern: "/api/**"
```

✅ Simpler, but less flexible.

---

### 📌 Interview Answer

> _"I enable CORS globally using `CorsConfigurationSource` — allowing specific origins, methods, and headers for `/api/**`. I use `setAllowedOriginPatterns` for flexibility and `setAllowCredentials(true)` when auth is needed. For simple cases, I use `@CrossOrigin` on controllers. I never allow `*` in production — always specify trusted origins."_  

---

## 30. How do you connect a Spring Boot app to a database?

> Use **Spring Data JPA** + **DataSource configuration** — Spring Boot auto-configures everything.

---

### 🔹 30.1 Step 1: Add Dependencies

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
</dependencies>
```

---

### 🔹 30.2 Step 2: Configure `application.yml`

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/bankdb?useSSL=false&serverTimezone=UTC
    username: root
    password: password
    driver-class-name: com.mysql.cj.jdbc.Driver

  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    database-platform: org.hibernate.dialect.MySQLDialect
```

✅ `ddl-auto` options:
- `create` – Drop and create on startup
- `update` – Update schema (development)
- `validate` – Validate only (production)
- `none` – No changes

---

### 🔹 30.3 Step 3: Define Entity & Repository

```java
@Entity
public class Account {
    @Id @GeneratedValue
    private Long id;
    private String number;
    private BigDecimal balance;
    // Getters, setters
}

@Repository
public interface AccountRepository extends JpaRepository<Account, Long> { }
```

---

### 🔹 30.4 Step 4: Use in Service

```java
@Service
public class AccountService {
    @Autowired
    private AccountRepository repo;

    public Account create(Account account) {
        return repo.save(account);
    }
}
```

✅ Spring Boot auto-configures:
- `DataSource`
- `EntityManagerFactory`
- `TransactionManager`

---

### 📌 Interview Answer

> _"I connect to a database by adding `spring-boot-starter-data-jpa` and the database driver. I configure `spring.datasource` and `spring.jpa` in `application.yml`. Spring Boot auto-configures the `DataSource`, `EntityManager`, and `TransactionManager`. I define `@Entity` classes and `JpaRepository` interfaces — no implementation needed. This is production-ready with minimal config."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I validate requests with `@Valid`, test with `@WebMvcTest`, monitor with Actuator, enable CORS securely, and connect to MySQL with Spring Data JPA."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---



You're mastering **full-stack Spring Boot development** at a **senior level**. 💪
