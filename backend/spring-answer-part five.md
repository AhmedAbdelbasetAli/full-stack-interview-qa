# 🌱 Spring Boot Deep Dive (Part 5)  
**Security, AOP, Configuration, and Profiles**

This document covers **advanced Spring Boot concepts** — critical for **enterprise applications**, **production systems**, and **senior backend roles**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 21. What is Spring Security? How do you secure endpoints?

> **Spring Security** is a powerful, customizable framework for **authentication, authorization, and protection** against common vulnerabilities (CSRF, XSS, session fixation).

It’s the **standard for securing Spring applications**.

---

### 🔹 21.1 Key Features

| Feature | Purpose |
|--------|--------|
| ✅ **Authentication** | Who are you? (Login, JWT, OAuth2) |
| ✅ **Authorization** | What can you do? (`hasRole()`, `hasAuthority()`) |
| ✅ **Method Security** | Secure service methods |
| ✅ **CSRF Protection** | Prevent cross-site request forgery |
| ✅ **Session Management** | Control session creation, timeout |
| ✅ **OAuth2 / OpenID Connect** | Social login, SSO |
| ✅ **JWT Support** | Stateless authentication |

---

### 🔹 21.2 Securing Endpoints with `HttpSecurity`

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll()
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .requestMatchers("/api/users/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated()
            )
            .formLogin(withDefaults()) // Enable login form
            .logout(logout -> logout.permitAll());
        return http.build();
    }
}
```

✅ This secures endpoints based on:
- URL patterns
- Roles (`ADMIN`, `USER`)
- Authentication state

---

### 🔹 21.3 Common Matchers

| Matcher | Use |
|-------|-----|
| `permitAll()` | No auth required |
| `authenticated()` | Must be logged in |
| `hasRole("ADMIN")` | User has role `ROLE_ADMIN` |
| `hasAuthority("read")` | User has authority `read` |
| `denyAll()` | Block all |

---

### 📌 Interview Answer

> _"Spring Security is a comprehensive framework for securing Spring apps. I use it to secure endpoints by configuring `HttpSecurity` — allowing public access to `/public/**`, requiring authentication for others, and restricting `/admin/**` to ADMIN role. It handles authentication, CSRF, and session management out of the box. I combine it with JWT for stateless APIs."_  

---

## 22. How do you implement authentication and authorization in Spring Boot?

> **Authentication** = "Who are you?"  
> **Authorization** = "What can you do?"

Spring Boot makes it easy with **Spring Security + JWT** (for stateless APIs).

---

### 🔹 22.1 Step-by-Step: JWT Authentication

#### 🟦 1. Add Dependencies
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.5</version>
</dependency>
```

#### 🟦 2. Create User & Auth Models
```java
@Entity
public class User {
    @Id @GeneratedValue
    private Long id;
    private String username;
    private String password; // Hashed
    private String role; // USER, ADMIN
    // Getters, setters
}
```

#### 🟦 3. Login Controller
```java
@PostMapping("/login")
public ResponseEntity<AuthResponse> login(@RequestBody AuthRequest request) {
    // Validate user
    String token = jwtUtil.generateToken(request.getUsername());
    return ResponseEntity.ok(new AuthResponse(token));
}
```

#### 🟦 4. JWT Filter
```java
@Component
public class JwtFilter implements Filter {
    @Autowired private JwtUtil jwtUtil;

    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) {
        String token = extractToken((HttpServletRequest) req);
        if (token != null && jwtUtil.validate(token)) {
            String username = jwtUtil.extractUsername(token);
            UsernamePasswordAuthenticationToken auth = 
                new UsernamePasswordAuthenticationToken(username, null, getAuthorities(username));
            SecurityContextHolder.getContext().setAuthentication(auth);
        }
        chain.doFilter(req, res);
    }
}
```

#### 🟦 5. Secure Endpoints
```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests()
        .antMatchers("/api/auth/login").permitAll()
        .antMatchers("/api/admin/**").hasRole("ADMIN")
        .anyRequest().authenticated()
        .and()
        .addFilterBefore(new JwtFilter(), UsernamePasswordAuthenticationFilter.class);
}
```

---

### 📌 Interview Answer

> _"I implement authentication using Spring Security with JWT. Users login with username/password, and receive a signed JWT. A custom filter validates the token on each request and sets the authentication context. I use `@PreAuthorize` or `HttpSecurity` to enforce role-based access. Passwords are stored with BCrypt, and tokens are short-lived with refresh tokens for security."_  

---

## 23. What is AOP (Aspect-Oriented Programming) in Spring? Give an example use case.

> **AOP** separates **cross-cutting concerns** (logging, security, caching) from business logic.

It uses **advice** (what to do) and **pointcuts** (where to do it).

---

### 🔹 23.1 Key Concepts

| Term | Meaning |
|------|--------|
| **Aspect** | Modularization of a cross-cutting concern |
| **Join Point** | Point in execution (method call, exception) |
| **Pointcut** | Predicate that matches join points |
| **Advice** | Action taken (before, after, around) |
| **Weaving** | Applying aspects to target objects |

---

### 🔹 23.2 Example: Logging with `@Around`

```java
@Aspect
@Component
public class LoggingAspect {

    private static final Logger log = LoggerFactory.getLogger(LoggingAspect.class);

    @Around("@annotation(LogExecutionTime)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        Object result = joinPoint.proceed();
        long duration = System.currentTimeMillis() - start;
        log.info("{} executed in {} ms", joinPoint.getSignature(), duration);
        return result;
    }
}

// Custom annotation
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LogExecutionTime {}

// Usage
@Service
public class UserService {
    @LogExecutionTime
    public User findById(Long id) {
        // Simulate work
        return userRepository.findById(id);
    }
}
```

---

### 🔹 23.3 Common Use Cases

| Use Case | Advice Type |
|--------|-------------|
| ✅ **Logging** | `@Around`, `@After` |
| ✅ **Performance Monitoring** | `@Around` |
| ✅ **Security Checks** | `@Before` |
| ✅ **Transaction Management** | `@Around` (used by `@Transactional`) |
| ✅ **Caching** | `@Around` (used by `@Cacheable`) |

---

### 📌 Interview Answer

> _"AOP allows me to modularize cross-cutting concerns like logging, security, or performance monitoring. I use `@Aspect` with `@Around` advice to log method execution time. The aspect intercepts methods annotated with `@LogExecutionTime` and measures duration. This keeps business logic clean and promotes reusability. Spring uses AOP internally for `@Transactional` and `@Cacheable`."_  

---

## 24. How do you externalize configuration in Spring Boot?

> **Externalized configuration** lets you **separate configuration from code** — so you can change settings **without recompiling**.

Spring Boot supports multiple sources, with **order of precedence**.

---

### 🔹 24.1 Configuration Sources (Highest to Lowest Priority)

1. **Command-line arguments** (`--server.port=8081`)
2. **`SPRING_APPLICATION_JSON`** (env var)
3. **ServletConfig parameters**
4. **JNDI**
5. **Java System properties** (`System.setProperty`)
6. **OS environment variables**
7. **`application-{profile}.properties`**
8. **`application.properties`**
9. **`@PropertySource` annotations**
10. **Default properties**

---

### 🔹 24.2 Example: `application.yml`

```yaml
server:
  port: 8080

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/bankdb
    username: ${DB_USER:root}
    password: ${DB_PASSWORD:password}

app:
  jwt-secret: ${JWT_SECRET:mydefaultsecret123}
  debug-mode: false
```

✅ Use **placeholders** with defaults: `${VAR:default}`

---

### 🔹 24.3 Accessing Configuration

#### 🟦 `@Value`
```java
@Value("${app.jwt-secret}")
private String jwtSecret;
```

#### 🟦 `@ConfigurationProperties` (Recommended)
```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private String jwtSecret;
    private boolean debugMode;
    // Getters and setters
}
```

✅ Type-safe, IDE-friendly, supports validation.

---

### 📌 Interview Answer

> _"I externalize configuration using `application.yml` and environment variables. I use `@ConfigurationProperties` for type-safe access to grouped settings like `app.jwt-secret`. I never hardcode values — everything is configurable via properties or env vars. This allows different settings per environment without code changes."_  

---

## 25. What are Spring Profiles? How do you use them?

> **Profiles** allow you to **define beans and configuration for specific environments** (dev, test, prod).

They enable **environment-specific behavior**.

---

### 🔹 25.1 Define Profiles

#### 🟦 In `application.yml`
```yaml
# application-dev.yml
spring:
  datasource:
    url: jdbc:h2:mem:devdb
  jpa:
    show-sql: true

# application-prod.yml
spring:
  datasource:
    url: jdbc:mysql://prod-db:3306/app
  jpa:
    show-sql: false
    hibernate:
      ddl-auto: validate
```

#### 🟦 Activate Profile
```bash
java -jar app.jar --spring.profiles.active=prod
```

Or in `application.yml`:
```yaml
spring:
  profiles:
    active: dev
```

---

### 🔹 25.2 Profile-Specific Beans

```java
@Bean
@Profile("dev")
public DataSource devDataSource() {
    return new H2DataSource();
}

@Bean
@Profile("prod")
public DataSource prodDataSource() {
    return new MySQLDataSource();
}
```

---

### 🔹 25.3 Conditional Configuration

```java
@Configuration
@Profile("test")
public class TestConfig {
    @Bean
    public UserService mockUserService() {
        return Mockito.mock(UserService.class);
    }
}
```

---

### 📌 Interview Answer

> _"Spring Profiles let me define environment-specific configuration and beans. I use `application-dev.yml`, `application-prod.yml`, and activate them via `spring.profiles.active`. I define different data sources, logging levels, and feature flags per profile. In tests, I use `@Profile("test")` to inject mocks. This ensures the right setup runs in each environment."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use Spring Profiles for environment-specific config, externalize secrets via env vars, secure APIs with Spring Security and JWT, and monitor performance with AOP."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---



Just say: _"Let’s do [topic]"_

You're mastering **enterprise Spring Boot** at a **senior level**. 💪
