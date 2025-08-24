# 🌱 Spring Beans & Annotations Deep Dive  
**Complete Guide to Spring Beans and All Major Spring Annotations**

This document covers **everything you need to know** about **Spring Beans** and **Spring Annotations** — essential for **Java backend development**, **Spring Boot**, and **system design interviews**.

---

## 🔹 What is a Bean in Spring?

> A **Spring Bean** is **any Java object** that is **instantiated, configured, and managed by the Spring IoC (Inversion of Control) container**.

Think of it as a **managed object** — Spring creates it, wires it, and destroys it.

---

### ✅ Key Points About Beans

| Feature | Explanation |
|--------|-------------|
| ✅ **Managed by Spring** | Created and destroyed by Spring container |
| ✅ **Configured via DI (Dependency Injection)** | Dependencies are injected, not created manually |
| ✅ **Singleton by Default** | One instance per Spring context (can be changed) |
| ✅ **Lifecycle Managed** | `@PostConstruct`, `@PreDestroy` |
| ✅ **Can Be Any Class** | Service, Repository, Controller, Config, Utility |

---

### ✅ Example: Simple Bean

```java
public class UserService {
    public void saveUser(User user) {
        System.out.println("Saving user: " + user.getName());
    }
}
```

To make it a **Spring Bean**, you need to **register it** — using annotations or XML.

---

## 🔹 How Are Beans Created?

### 1. **Component Scanning + Stereotype Annotations**
Spring scans packages and auto-registers classes with annotations like `@Component`.

### 2. **Java Configuration (`@Configuration` + `@Bean`)**
You manually define beans in a config class.

```java
@Configuration
public class AppConfig {
    
    @Bean
    public UserService userService() {
        return new UserService();
    }
}
```

### 3. **XML Configuration** (Legacy)
```xml
<bean id="userService" class="com.example.UserService" />
```

✅ Modern apps use **annotations** or **Java config**.

---

## 🔹 Bean Lifecycle

1. **Instantiate** → Spring creates the object
2. **Populate Properties** → Inject dependencies
3. **Initialization** → `@PostConstruct`, `InitializingBean`
4. **In Use** → Bean is ready
5. **Destruction** → `@PreDestroy`, `DisposableBean`

---

## 🔹 Bean Scopes

| Scope | Description |
|------|-------------|
| `@Scope("singleton")` | One instance per Spring context (default) |
| `@Scope("prototype")` | New instance every time it’s requested |
| `@Scope("request")` | One per HTTP request |
| `@Scope("session")` | One per HTTP session |
| `@Scope("application")` | One per ServletContext |

```java
@Component
@Scope("prototype")
public class TempUser {}
```

---

## 📦 All Major Spring Annotations (Categorized)

Here’s a **complete list** of **essential Spring annotations** grouped by purpose.

---

## 1️⃣ 🧱 Stereotype Annotations (Bean Registration)

These mark classes as Spring beans.

| Annotation | Purpose | Example |
|----------|--------|--------|
| `@Component` | Generic stereotype for any Spring-managed component | `@Component class MyService` |
| `@Service` | Business logic layer | `@Service class UserService` |
| `@Repository` | Data access layer (DAO) — translates exceptions to Spring’s `DataAccessException` | `@Repository class UserRepository` |
| `@Controller` | MVC controller (returns views) | `@Controller class UserController` |
| `@RestController` | REST controller (returns JSON/XML) | `@RestController class UserApiController` |

✅ All are **stereotypes** — `@Service`, `@Repository`, `@Controller` are specializations of `@Component`.

---

## 2️⃣ 🔌 Dependency Injection Annotations

Used to inject beans.

| Annotation | Purpose | Example |
|----------|--------|--------|
| `@Autowired` | Injects a bean (by type) | `@Autowired private UserService userService;` |
| `@Qualifier` | Resolves ambiguity when multiple beans of same type exist | `@Autowired @Qualifier("premiumService") Service service;` |
| `@Primary` | Marks a bean as preferred when multiple exist | `@Component @Primary class PremiumService` |
| `@Value` | Injects values (strings, numbers, properties) | `@Value("${app.name}") String appName;` |

---

## 3️⃣ ⚙️ Configuration Annotations

Used to define configuration classes and beans.

| Annotation | Purpose | Example |
|----------|--------|--------|
| `@Configuration` | Marks class as source of bean definitions | `@Configuration class AppConfig` |
| `@Bean` | Defines a bean method (used in `@Configuration` class) | `@Bean public DataSource dataSource() { ... }` |
| `@ComponentScan` | Tells Spring where to scan for components | `@ComponentScan("com.example")` |
| `@PropertySource` | Load properties file | `@PropertySource("app.properties")` |
| `@Import` | Import other config classes | `@Import(DatabaseConfig.class)` |

---

## 4️⃣ 🌐 Web & REST Annotations (Spring MVC)

Used in web controllers.

| Annotation | Purpose | Example |
|----------|--------|--------|
| `@RequestMapping` | Map HTTP requests to methods | `@RequestMapping("/users")` |
| `@GetMapping` | Shortcut for `@RequestMapping(method = GET)` | `@GetMapping("/users")` |
| `@PostMapping` | POST requests | `@PostMapping("/users")` |
| `@PutMapping`, `@DeleteMapping` | PUT and DELETE | `@DeleteMapping("/users/{id}")` |
| `@RequestParam` | Extract query parameters | `@RequestParam String name` |
| `@PathVariable` | Extract path variables | `@PathVariable Long id` |
| `@RequestBody` | Bind HTTP request body to object | `@RequestBody User user` |
| `@ResponseBody` | Return object as response body (JSON) | `@ResponseBody User getUser()` |
| `@RestController` | `@Controller + @ResponseBody` on all methods | `@RestController class UserApi` |
| `@CrossOrigin` | Enable CORS for a method or controller | `@CrossOrigin(origins = "https://trusted.com")` |

---

## 5️⃣ 🔄 AOP (Aspect-Oriented Programming)

For cross-cutting concerns like logging, security.

| Annotation | Purpose | Example |
|----------|--------|--------|
| `@Aspect` | Define an aspect (cross-cutting logic) | `@Aspect class LoggingAspect` |
| `@Before` | Run before method | `@Before("execution(* com.example.service.*.*(..))")` |
| `@After`, `@AfterReturning`, `@AfterThrowing` | Run after method | `@AfterReturning("pointcut")` |
| `@Around` | Wrap method (before + after) | `@Around("pointcut")` |

---

## 6️⃣ 🔐 Security Annotations

Used with **Spring Security**.

| Annotation | Purpose | Example |
|----------|--------|--------|
| `@EnableWebSecurity` | Enable Spring Security config | `@Configuration @EnableWebSecurity` |
| `@PreAuthorize`, `@PostAuthorize` | Method-level security | `@PreAuthorize("hasRole('ADMIN')")` |
| `@Secured` | Role-based access | `@Secured("ROLE_USER")` |
| `@RolesAllowed` | JSR-250 standard | `@RolesAllowed("ADMIN")` |

---

## 7️⃣ 📦 Data Access (Spring Data JPA)

Used with databases.

| Annotation | Purpose | Example |
|----------|--------|--------|
| `@Entity` | JPA entity (maps to table) | `@Entity class User` |
| `@Table` | Customize table name | `@Table(name = "users")` |
| `@Id` | Primary key | `@Id @GeneratedValue Long id;` |
| `@Column` | Customize column | `@Column(name = "user_name")` |
| `@OneToMany`, `@ManyToOne` | Relationships | `@OneToMany(mappedBy = "user")` |
| `@Repository` | Mark DAO as Spring bean | `@Repository class UserRepository` |
| `@Query` | Custom JPQL or native query | `@Query("SELECT u FROM User u WHERE u.active = true")` |

---

## 8️⃣ 🚀 Spring Boot Annotations

| Annotation | Purpose | Example |
|----------|--------|--------|
| `@SpringBootApplication` | Main annotation — combines `@Configuration`, `@ComponentScan`, `@EnableAutoConfiguration` | `@SpringBootApplication class App` |
| `@EnableAutoConfiguration` | Auto-configure beans based on classpath | (Part of `@SpringBootApplication`) |
| `@ConditionalOn...` | Conditional bean registration | `@ConditionalOnMissingBean` |
| `@ConfigurationProperties` | Bind properties to a class | `@ConfigurationProperties("app")` |

---

## 9️⃣ 🔄 Lifecycle Annotations

| Annotation | Purpose | Example |
|----------|--------|--------|
| `@PostConstruct` | Runs after bean creation | `@PostConstruct void init()` |
| `@PreDestroy` | Runs before bean destruction | `@PreDestroy void cleanup()` |

---

## 📌 Interview Answer (Summary)

> _"A Spring Bean is any object managed by the Spring container — created, wired, and destroyed by Spring. I use annotations like @Component, @Service, @Repository to register beans. @Autowired injects dependencies. @RestController handles HTTP requests. Spring Boot uses @SpringBootApplication to auto-configure everything. I prefer constructor injection for immutability and testability."_  

---

## ✅ Best Practices

| Rule | Why |
|------|-----|
| ✅ **Use constructor injection** | Immutable, testable, no circular dependency issues |
| ✅ **Use specific stereotypes** (`@Service`, `@Repository`) | Better semantics and exception translation |
| ✅ **Use `@Qualifier` when needed** | Resolve ambiguity |
| ✅ **Avoid field injection** (`@Autowired` on field) | Hard to test, breaks encapsulation |
| ✅ **Use `@Configuration` + `@Bean` for external classes** | When you can’t annotate the class with `@Component` |

---

## ✅ Final Tip

> 🎯 In interviews, **draw the flow**:
> ```
> [Controller] → @Autowired → [Service] → @Autowired → [Repository]
> ```
> And say:  
> _"Spring manages all these as beans, injects dependencies, and handles the lifecycle."_  

That shows **deep understanding of Spring internals**.

---

