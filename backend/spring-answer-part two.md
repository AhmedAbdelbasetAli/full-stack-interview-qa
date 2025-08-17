# 🌱 Spring Boot Deep Dive  
**Auto-Configuration, REST, and Core Annotations**

This document continues the deep dive into **Spring Boot** — the **go-to framework** for modern Java backend development.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 6. What is Spring Boot? How is it different from the Spring Framework?

> **Spring Boot** is an **opinionated extension** of the **Spring Framework** that simplifies setup, configuration, and deployment of Spring applications.

Think of it as **"Spring made easy"**.

---

### 🔹 6.1 Spring Framework vs Spring Boot

| Feature | Spring Framework | Spring Boot |
|--------|------------------|------------|
| **Configuration** | Manual (XML, Java config) | Auto-configuration |
| **Setup Time** | High (lots of boilerplate) | Low (starters) |
| **Embedded Server** | ❌ No (need external Tomcat) | ✅ Yes (Tomcat, Jetty, Netty) |
| **Dependency Management** | Manual | Via `spring-boot-starter-*` |
| **Production Readiness** | Add-ons needed | Built-in (Actuator, metrics) |
| **Use Case** | Full control, legacy apps | Rapid development, microservices |

---

### 🔹 6.2 Key Features of Spring Boot

| Feature | Benefit |
|--------|--------|
| ✅ **Auto-configuration** | Auto-configures beans based on classpath |
| ✅ **Starter Dependencies** | Predefined dependency bundles (e.g., `spring-boot-starter-web`) |
| ✅ **Embedded Server** | No need to deploy WAR — run as JAR |
| ✅ **Actuator** | Production monitoring (health, metrics, env) |
| ✅ **Externalized Configuration** | `application.yml`, profiles, env vars |
| ✅ **No XML** | Java or YAML config only |

---

### 🔹 6.3 Example: Spring vs Spring Boot

#### ❌ Spring (Manual Setup)
```xml
<!-- web.xml, dispatcher-servlet.xml, etc. -->
```

#### ✅ Spring Boot (Minimal)
```java
@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

✅ Done. Embedded Tomcat starts on port 8080.

---

### 📌 Interview Answer

> _"Spring Boot is an opinionated extension of the Spring Framework that eliminates boilerplate configuration. It provides auto-configuration, embedded servers, and starter dependencies to let you build production-ready apps quickly. While Spring gives full control, Spring Boot accelerates development — ideal for microservices and REST APIs. I use Spring Boot for 90% of new projects."_  

---

## 7. What is auto-configuration in Spring Boot?

> **Auto-configuration** is Spring Boot’s ability to **automatically configure beans** based on:
- ✅ What’s on the classpath
- ✅ What beans you’ve defined
- ✅ Application properties

It’s the **magic behind `spring-boot-starter-*`**.

---

### 🔹 7.1 How It Works

1. You add `spring-boot-starter-data-jpa`
2. Spring Boot sees:
   - `Hibernate` and `JPA` on classpath
   - `DataSource` bean
3. Automatically configures:
   - `EntityManagerFactory`
   - `TransactionManager`
   - `JpaRepository` support

✅ No need to write `@Configuration` for common setups.

---

### 🔹 7.2 Example: Auto-Configured Web Server

Add:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

✅ Spring Boot automatically:
- Configures embedded Tomcat
- Sets up `DispatcherServlet`
- Enables Spring MVC
- Starts server on port 8080

No XML, no manual config.

---

### 🔹 7.3 How to See Auto-Configuration?

Enable debug logging:
```yaml
# application.yml
debug: true
```

✅ On startup, Spring Boot logs:
- Which auto-configurations were applied
- Which were skipped and why

Example:
```
   DataSourceAutoConfiguration matched:
      - @ConditionalOnClass found required classes: ...
```

---

### 🔹 7.4 Customizing Auto-Configuration

| Method | Use |
|------|-----|
| `@EnableAutoConfiguration(exclude = ...)` | Disable specific auto-config |
| `@SpringBootApplication(exclude = ...)` | Same |
| Define your own bean | Overrides auto-config (e.g., custom `DataSource`) |

```java
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
public class App { ... }
```

---

### 📌 Interview Answer

> _"Auto-configuration automatically sets up beans based on classpath and existing beans. For example, adding `spring-boot-starter-web` auto-configures Tomcat and Spring MVC. It uses `@Conditional` annotations to decide what to configure. I can disable it with `exclude` or override with custom beans. It’s the key reason Spring Boot reduces boilerplate."_  

---

## 8. What is the entry point of a Spring Boot application?

> The entry point is a class with the **`@SpringBootApplication`** annotation and a **`main` method** that calls `SpringApplication.run()`.

---

### 🔹 8.1 Example

```java
@SpringBootApplication
public class BankApp {
    public static void main(String[] args) {
        SpringApplication.run(BankApp.class, args);
    }
}
```

---

### 🔹 8.2 What `@SpringBootApplication` Does

It’s a **convenience annotation** that combines:

```java
@Configuration      // Marks as config class
@EnableAutoConfiguration  // Enables auto-configuration
@ComponentScan      // Scans for @Component, @Service, etc.
```

✅ So this:
```java
@SpringBootApplication
public class App { ... }
```

Is equivalent to:
```java
@Configuration
@EnableAutoConfiguration
@ComponentScan
public class App { ... }
```

---

### 🔹 8.3 What `SpringApplication.run()` Does

1. Creates `ApplicationContext`
2. Enables auto-configuration
3. Performs component scan
4. Starts embedded server (if web app)
5. Triggers `CommandLineRunner`/`ApplicationRunner`

---

### 📌 Interview Answer

> _"The entry point is a class with `@SpringBootApplication` and a `main` method that calls `SpringApplication.run()`. `@SpringBootApplication` enables auto-configuration, component scanning, and configures the app. `run()` starts the context, scans for beans, and launches the embedded server. This single class replaces what used to require XML files and external servers."_  

---

## 9. How do you create a REST API using Spring Boot?

Here’s the **step-by-step process** to build a production-ready REST API.

---

### 🔹 9.1 Step 1: Add Dependencies

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
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

### 🔹 9.2 Step 2: Configure Database

```yaml
# application.yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/bankdb
    username: root
    password: password
  jpa:
    hibernate:
      ddl-auto: update
```

---

### 🔹 9.3 Step 3: Create Model

```java
@Entity
public class Account {
    @Id @GeneratedValue
    private Long id;
    private String number;
    private BigDecimal balance;

    // Getters, setters, constructors
}
```

---

### 🔹 9.4 Step 4: Create Repository

```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Long> {
}
```

---

### 🔹 9.5 Step 6: Create REST Controller

```java
@RestController
@RequestMapping("/api/accounts")
public class AccountController {

    @Autowired
    private AccountService service;

    @GetMapping
    public List<Account> getAll() {
        return service.getAllAccounts();
    }

    @PostMapping
    public Account create(@RequestBody Account account) {
        return service.createAccount(account);
    }

    @GetMapping("/{id}")
    public Account getById(@PathVariable Long id) {
        return service.getAccount(id);
    }
}
```

---

### 🔹 9.6 Step 7: Run and Test

✅ Start app → embedded server runs on `http://localhost:8080`  
✅ Test with `curl` or Postman:
```bash
curl http://localhost:8080/api/accounts
```

---

### 📌 Interview Answer

> _"I create a REST API by adding `spring-boot-starter-web`, defining a model, repository, service, and `@RestController`. I use `@GetMapping`, `@PostMapping`, and `@PathVariable` to map HTTP methods. Spring Boot auto-configures Jackson for JSON, and I return POJOs directly. It’s fast to build and production-ready with minimal config."_  

---

## 10. What is `@RestController`? How is it different from `@Controller`?

| Annotation | Purpose | Response Body |
|----------|--------|---------------|
| `@Controller` | Traditional Spring MVC (views) | ❌ No — returns view name |
| `@RestController` | REST APIs (JSON/XML) | ✅ Yes — returns data |

---

### 🔹 10.1 `@Controller` – For HTML Views

```java
@Controller
public class HomeController {
    @GetMapping("/home")
    public String home(Model model) {
        model.addAttribute("message", "Hello");
        return "home"; // View name (e.g., home.html)
    }
}
```

✅ Returns a **view template** (Thymeleaf, JSP)

---

### 🔹 10.2 `@RestController` – For REST APIs

```java
@RestController
public class ApiController {
    @GetMapping("/api/data")
    public Map<String, String> getData() {
        return Map.of("name", "Alice");
    }
}
```

✅ Returns **JSON** directly:
```json
{"name": "Alice"}
```

---

### 🔹 10.3 `@RestController = @Controller + @ResponseBody`

```java
@RestController
public class ApiController { ... }
```

Is equivalent to:
```java
@Controller
@ResponseBody
public class ApiController { ... }
```

✅ `@ResponseBody` tells Spring to **serialize the return value** to HTTP response body (JSON).

---

### 🔹 10.4 When to Use Which?

| Use Case | Annotation |
|--------|-----------|
| ✅ REST API (JSON) | `@RestController` |
| ✅ HTML + Thymeleaf | `@Controller` |
| ✅ Mix of both | `@Controller` + `@ResponseBody` on specific methods |

---

### 📌 Interview Answer

> _"`@RestController` is a convenience annotation for REST APIs — it combines `@Controller` and `@ResponseBody`, so return values are automatically serialized to JSON. `@Controller` is for server-side rendering (HTML). I use `@RestController` for all APIs and `@Controller` only when returning views. It makes the intent clear and reduces boilerplate."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use Spring Boot for auto-configuration, `@SpringBootApplication` as entry point, and `@RestController` to expose REST endpoints."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---

