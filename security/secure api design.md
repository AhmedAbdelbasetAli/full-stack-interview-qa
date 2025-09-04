# 🔐 Secure API Design  
**From Planning to Production – Build APIs That Are Safe, Scalable & Maintainable**

This document is a **comprehensive guide to designing secure APIs** — covering **REST, authentication, input validation, rate limiting, error handling**, and **real-world best practices**.

You’ll learn:
- ✅ How to design clean, secure REST APIs
- ✅ Authentication & authorization strategies
- ✅ Input validation, rate limiting, logging
- ✅ Real code examples (Node.js + Express, Java + Spring Boot)
- ✅ Interview-ready answers

---

## 🔹 1. Design Principles for Secure APIs

> A secure API is **not just about encryption** — it’s about **design**, **behavior**, and **defense in depth**.

### ✅ Core Principles

| Principle | Why It Matters |
|--------|---------------|
| ✅ **Never Trust the Client** | All security happens on the server |
| ✅ **Least Privilege** | Users and services get only the permissions they need |
| ✅ **Defense in Depth** | Multiple layers of security (auth, validation, logging) |
| ✅ **Fail Securely** | Default to deny, return minimal info in errors |
| ✅ **Secure by Default** | HTTPS, secure headers, short-lived tokens |

---

## 🔹 2. REST API Design Best Practices

### ✅ Use Proper HTTP Methods
| Method | Purpose | Idempotent? |
|-------|--------|------------|
| `GET` | Retrieve resource | ✅ Yes |
| `POST` | Create resource | ❌ No |
| `PUT` | Replace resource | ✅ Yes |
| `PATCH` | Partial update | ❌ No |
| `DELETE` | Delete resource | ✅ Yes |

---

### ✅ Use Proper Status Codes
| Code | Meaning |
|------|--------|
| `200 OK` | Success (GET, PUT, PATCH) |
| `201 Created` | Resource created (POST) |
| `204 No Content` | Success, no response body (DELETE) |
| `400 Bad Request` | Client error (invalid input) |
| `401 Unauthorized` | Missing or invalid auth |
| `403 Forbidden` | Authenticated but not authorized |
| `404 Not Found` | Resource doesn’t exist |
| `429 Too Many Requests` | Rate limiting |
| `500 Internal Server Error` | Server bug (don’t leak details) |

---

### ✅ Example: User API
```http
GET    /api/v1/users          → 200 + list
POST   /api/v1/users          → 201 + created user
GET    /api/v1/users/123      → 200 + user
PUT    /api/v1/users/123      → 200 + updated user
DELETE /api/v1/users/123      → 204
```

✅ Version in URL (`/v1`) for backward compatibility

---

## 🔹 3. Authentication & Authorization

> **Authentication**: Who are you?  
> **Authorization**: What are you allowed to do?

---

### ✅ Option 1: JWT (Stateless)
```json
{
  "sub": "123",
  "name": "Alice",
  "role": "USER",
  "iat": 1712345600,
  "exp": 1712349200
}
```

#### Pros:
- ✅ Stateless (good for microservices)
- ✅ Easy to scale

#### Cons:
- ❌ Hard to revoke
- ❌ Token size grows with claims

#### Fix:
- Use short expiry (15–60 mins)
- Pair with **refresh tokens** (stored securely)

---

### ✅ Option 2: Session + JWT (Hybrid)
- Login → server creates session, returns JWT with `sessionId`
- JWT is stateless, but server can revoke session

---

### ✅ Option 3: OAuth 2.0 / OpenID Connect
- For **third-party login** (Google, GitHub)
- Use `authorization_code` flow with PKCE for SPAs

---

### ✅ Java (Spring Security) Example
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/v1/public/**").permitAll()
                .requestMatchers("/api/v1/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            .addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }
}
```

---

### ✅ Node.js (Express) Middleware
```js
function authenticateToken(req, res, next) {
  const token = req.headers['authorization']?.split(' ')[1];
  if (!token) return res.status(401).json({ error: 'No token' });

  jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
    if (err) return res.status(403).json({ error: 'Invalid token' });
    req.user = user;
    next();
  });
}

app.get('/profile', authenticateToken, (req, res) => {
  res.json(req.user);
});
```

---

## 🔹 4. Input Validation & Sanitization

> Validate **every input** — query, body, headers, path.

---

### ✅ Java (Spring Boot + Bean Validation)
```java
public class TransferRequest {
    @NotNull
    @Min(1)
    private Long toUserId;

    @DecimalMin("0.01")
    @Digits(integer=10, fraction=2)
    private BigDecimal amount;

    // getters/setters
}

@PostMapping("/transfer")
public ResponseEntity<?> transfer(@Valid @RequestBody TransferRequest req) {
    // If invalid, Spring returns 400 automatically
    service.transfer(req);
    return ResponseEntity.ok().build();
}
```

---

### ✅ Node.js (Express + express-validator)
```js
const { body, validationResult } = require('express-validator');

app.post('/transfer',
  body('toUserId').isInt({ min: 1 }),
  body('amount').isDecimal().custom(value => value > 0),
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    // Proceed
  }
);
```

---

## 🔹 5. Rate Limiting

> Prevent abuse, brute force, DDoS.

---

### ✅ Node.js (express-rate-limit)
```js
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests
  message: { error: 'Too many requests' },
  standardHeaders: true, // Return rate limit info in headers
  legacyHeaders: false
});

app.use('/api/', limiter);
```

✅ Headers:
```http
RateLimit-Limit: 100
RateLimit-Remaining: 98
RateLimit-Reset: 1712345600
```

---

### ✅ Java (Spring Boot + Redis)
Use `spring-boot-starter-data-redis` + custom rate limiter with `ReactiveRedisTemplate`.

---

## 🔹 6. Secure Headers

> Tell the browser how to behave.

```js
app.use((req, res, next) => {
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');
  res.setHeader('Content-Security-Policy', 
    "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'"
  );
  next();
});
```

✅ Or use `helmet` in Node.js:
```js
const helmet = require('helmet');
app.use(helmet());
```

---

## 🔹 7. Error Handling

> Never expose system details.

---

### ✅ Bad: Leaky Error
```json
{
  "error": "Internal Server Error",
  "message": "Error: ER_BAD_FIELD_ERROR: Unknown column 'email' in 'where clause'",
  "stack": "at Query..."
}
```

❌ Exposes DB schema and stack trace

---

### ✅ Good: Generic Error
```json
{
  "error": "Internal Server Error",
  "message": "An unexpected error occurred"
}
```

✅ Log full error server-side, return minimal info to client

---

### ✅ Global Error Handler (Node.js)
```js
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ 
    error: 'Internal Server Error',
    message: 'An unexpected error occurred'
  });
});
```

---

## 🔹 8. Logging & Monitoring

| Log This | Don’t Log |
|--------|----------|
| ✅ Timestamp, IP, endpoint, status code | ❌ Passwords, tokens, PII |
| ✅ Request ID for tracing | ❌ Stack traces in response |
| ✅ Failed login attempts | ❌ Raw SQL with data |

```js
app.use((req, res, next) => {
  const start = Date.now();
  const requestId = uuid.v4();
  console.log(`${new Date().toISOString()} ${requestId} ${req.method} ${req.path} ${req.ip}`);
  
  res.on('finish', () => {
    const duration = Date.now() - start;
    console.log(`${requestId} ${res.statusCode} ${duration}ms`);
  });
  
  next();
});
```

---

## 🔹 9. API Versioning

> Avoid breaking changes.

| Strategy | Example |
|--------|--------|
| ✅ **URL Versioning** | `/api/v1/users` |
| ✅ **Header Versioning** | `Accept: application/vnd.myapi.v1+json` |
| ❌ Query Param | `/api/users?version=1` (hard to cache) |

---

## 🔹 10. Documentation

> Use **OpenAPI (Swagger)** for machine-readable docs.

```yaml
# openapi.yaml
openapi: 3.0.0
info:
  title: SecureBank API
  version: 1.0.0
paths:
  /users:
    get:
      summary: Get all users
      responses:
        '200':
          description: OK
```

✅ Tools: **Swagger UI**, **Redoc**, **Postman**

---

## 📌 Interview Answer

> _"I design secure APIs with clear REST principles, proper status codes, and versioning. I use JWT or OAuth for auth, validate all input, and add rate limiting. I return generic errors, use security headers, and log responsibly. I document with OpenAPI and never trust the client. The server enforces all rules."_  

---

## ✅ Final Tip

> 🎯 In interviews, **draw the flow**:
> ```
> [Client] → HTTPS → [Auth] → [Validate] → [Rate Limit] → [Business Logic] → [Log]
> ```
> And say:  
> _"Every request goes through a security pipeline."_  

That shows **architectural thinking**.

---

