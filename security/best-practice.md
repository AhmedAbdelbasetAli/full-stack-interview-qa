
---

## 🔐 1. **Never Trust the Client**  
### 🧠 The Golden Rule of Web Security

> ❝The client (browser, mobile app, API consumer) is **hostile territory**.❞

Even if you wrote the frontend, **attackers can bypass it** using:
- Browser DevTools
- Proxy tools (Burp Suite, Charles)
- Custom scripts
- Modified APKs (mobile)

### ✅ What This Means

| You Control | You Do NOT Control |
|-----------|-------------------|
| ✅ Server logic | ❌ What the client sends |
| ✅ Database | ❌ Client-side JavaScript |
| ✅ API responses | ❌ Form submissions from fake clients |
| ✅ Authentication checks | ❌ `localStorage`, cookies (they can be tampered with) |

---

### 🛑 Example: Bypassing Client-Side Validation

```html
<!-- Client-side validation -->
<input type="number" min="1" max="100" id="amount">
<button onclick="submit()">Buy</button>

<script>
function submit() {
    const amount = document.getElementById('amount').value;
    if (amount < 1 || amount > 100) {
        alert("Invalid amount");
        return;
    }
    fetch('/api/buy', { method: 'POST', body: JSON.stringify({ amount }) });
}
</script>
```

❌ **Attackers can bypass this**:

```bash
curl -X POST https://yoursite.com/api/buy -d '{"amount": 9999}'
```

✅ The server receives `9999` — even though the UI only allowed 1–100.

---

### ✅ Fix: Always Validate on Server

```java
// Java (Spring Boot)
@PostMapping("/buy")
public ResponseEntity<?> buy(@Valid @RequestBody PurchaseRequest request) {
    // Validation happens here — not just in browser
    service.processPurchase(request);
    return ResponseEntity.ok().build();
}

public class PurchaseRequest {
    @Min(1) @Max(100)
    private int amount;

    // getter/setter
}
```

✅ Even if attacker sends `9999`, server rejects it with `400 Bad Request`

---

### 📌 Interview Answer
> _"I never trust the client. Client-side validation is for UX only. All security and validation happen on the server. An attacker can bypass any frontend restriction — so the server must enforce all rules."_  

---

## 🔐 2. **Server-Side Input Validation**  
### 🛡️ Stop Bad Data at the Gate

> Validate **every input** — query params, form data, JSON, headers.

---

### ✅ Use Built-In Validation Frameworks

#### Java (Spring Boot + Bean Validation)
```java
public class UserCreateRequest {
    @NotBlank(message = "Email is required")
    @Email(message = "Must be a valid email")
    private String email;

    @Size(min = 8, message = "Password too short")
    @Pattern(regexp = ".*[A-Z].*", message = "Must contain uppercase")
    private String password;

    @Past
    private LocalDate birthDate;

    // getters and setters
}
```

```java
@PostMapping("/users")
public ResponseEntity<?> createUser(@Valid @RequestBody UserCreateRequest req) {
    // If invalid, Spring returns 400 automatically
    userService.create(req);
    return ResponseEntity.ok().build();
}
```

✅ Auto-returns `400 Bad Request` with error details

---

#### Node.js (Express + express-validator)
```js
const { body, validationResult } = require('express-validator');

app.post('/user', 
  body('email').isEmail().normalizeEmail(),
  body('password').isLength({ min: 8 }),
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

### ✅ Validate Business Logic Too

```java
if (user.getAge() < 18) {
    throw new ForbiddenException("Must be 18+");
}
```

✅ Not just format — **business rules matter**

---

### 📌 Best Practices
| Rule | Why |
|------|-----|
| ✅ Validate early | Fail fast |
| ✅ Use DTOs (not entities) | Avoid mass assignment |
| ✅ Normalize data | Trim emails, lowercase usernames |
| ✅ Return clear errors | But don’t leak system details |

---

## 🔐 3. **Prevent SQL Injection**  
### 🚫 Never Concatenate User Input into SQL

> One of the **most dangerous** and **common** attacks.

---

### ❌ Vulnerable Code (Java)
```java
String query = "SELECT * FROM users WHERE email = '" + email + "'";
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(query);
```

If `email = ' OR '1'='1`, query becomes:
```sql
SELECT * FROM users WHERE email = '' OR '1'='1'
```

✅ Logs attacker in as **first user** (often admin)

---

### ✅ Secure: Use Parameterized Queries

```java
String query = "SELECT * FROM users WHERE email = ?";
PreparedStatement pstmt = conn.prepareStatement(query);
pstmt.setString(1, email);  // Safe — treated as data, not SQL
ResultSet rs = pstmt.executeQuery();
```

✅ Works the same in:
- Python: `cursor.execute("SELECT * FROM users WHERE email = %s", (email,))`
- Node.js: `db.query("SELECT * FROM users WHERE email = ?", [email])`
- C#: `command.Parameters.AddWithValue("@email", email)`

---

### ✅ ORM is Safer (But Not Immune)

```java
// JPA/Hibernate
User user = repo.findByEmail(email);  // Safe
```

❌ But avoid:
```java
@Query("SELECT u FROM User u WHERE u.email = ?1")
User findByEmail(String email);  // Still safe

@Query("SELECT u FROM User u WHERE u.email = '#{?1}'")  // SpEL — can be risky
```

---

### 📌 Interview Answer
> _"I prevent SQL injection with parameterized queries — never string concatenation. In Spring, I use JPA repositories or named parameters. I also use tools like SonarQube to detect unsafe patterns in code."_  

---

## 🔐 4. **Authentication & Session Security**

> Who are you? Are you allowed to do this?

---

### ✅ 1. Password Storage: Use Strong Hashing

```java
// Java with Spring Security
PasswordEncoder encoder = new BCryptPasswordEncoder();
String hashed = encoder.encode("rawPassword");

// To check
boolean matches = encoder.matches("rawPassword", hashed);
```

✅ **Never store plaintext passwords**  
✅ Use **bcrypt**, **scrypt**, or **Argon2**  
✅ Salt is automatic in bcrypt

---

### ✅ 2. Sessions vs Tokens

| Approach | How | Security |
|--------|-----|---------|
| **Sessions** | Server stores session → sends `JSESSIONID` cookie | ✅ Secure if `HttpOnly`, `Secure`, `SameSite` |
| **JWT** | Token contains data (e.g., `{userId: 123}`), signed | ✅ Stateless, but can’t be revoked easily |

---

### ✅ 3. Secure Session Cookies

```http
Set-Cookie: JSESSIONID=abc123; 
  HttpOnly; 
  Secure; 
  SameSite=Lax; 
  Max-Age=3600
```

| Flag | Purpose |
|------|--------|
| `HttpOnly` | ❌ JavaScript can’t read it → prevents XSS theft |
| `Secure` | 🔒 Only sent over HTTPS |
| `SameSite=Lax` | 🛑 Blocks CSRF in most cases |
| `Max-Age` | ⏳ Short-lived sessions |

---

### ✅ 4. JWT Best Practices

```json
{
  "sub": "123",
  "role": "USER",
  "iat": 1712345600,
  "exp": 1712349200
}
```

✅ **Do**:
- Keep payload small
- Use short expiry (15–60 mins)
- Use refresh tokens
- Sign with strong algorithm (HS256, RS256)

❌ **Don’t**:
- Store sensitive data
- Make tokens long-lived
- Skip signature verification

---

## 🔐 5. **Authorization: Who Can Do What?**

> Even if authenticated, does the user have permission?

---

### ✅ Role-Based Access Control (RBAC)

```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long id) { ... }

@PreAuthorize("hasAuthority('user:write')")
public void updateUser(User user) { ... }
```

Or in code:
```java
if (!currentUser.isAdmin() && !user.getOwner().equals(currentUser)) {
    throw new AccessDeniedException("Not allowed");
}
```

---

### ✅ Prevent IDOR (Insecure Direct Object Reference)

```java
// ❌ Vulnerable
@GetMapping("/documents/{id}")
public Document getDoc(@PathVariable Long id) {
    return docRepo.findById(id); // No ownership check
}

// ✅ Secure
@GetMapping("/documents/{id}")
public Document getDoc(@PathVariable Long id, Principal principal) {
    Document doc = docRepo.findById(id);
    if (!doc.getOwner().equals(principal.getName())) {
        throw new AccessDeniedException("Not your document");
    }
    return doc;
}
```

---

## 🔐 6. **Client-Side Security: Prevent XSS**

> Cross-Site Scripting — when attacker injects JavaScript.

---

### ✅ Types of XSS

| Type | How | Example |
|------|-----|--------|
| **Stored** | Saved on server (e.g., comment) | `<script>alert(1)</script>` in bio |
| **Reflected** | Echoed in response (e.g., search) | `?q=<script>steal()</script>` |
| **DOM-based** | Client-side JS writes to DOM | `el.innerHTML = location.hash` |

---

### ✅ Defense: Escape Output

```js
// ❌ Dangerous
element.innerHTML = userInput;

// ✅ Safe
element.textContent = userInput;
```

Or use libraries:
- **DOMPurify** (sanitize HTML)
- **React** (auto-escapes by default)

```jsx
// React — safe
<div>{userInput}</div>

// To render HTML (dangerous)
<div dangerouslySetInnerHTML={{__html: richText}} />
```

---

### ✅ Content Security Policy (CSP)

```http
Content-Security-Policy: 
  default-src 'self';
  script-src 'self' https://apis.google.com;
  object-src 'none';
  frame-ancestors 'none';
```

✅ Blocks inline scripts, external domains, iframes

---

## 🔐 7. **Prevent CSRF**

> Forcing a logged-in user to make unwanted requests.

---

### ✅ Defense

| Method | How |
|-------|-----|
| ✅ **Anti-CSRF Tokens** | Hidden field in forms, validated on server |
| ✅ **SameSite Cookies** | `SameSite=Lax` blocks cross-site POSTs |
| ✅ **Custom Headers** | e.g., `X-Requested-With` — only from your JS |

```html
<input type="hidden" name="_csrf" value="abc123" />
```

✅ Spring Security adds this automatically

---

## 🔐 8. **Secure Dependencies**

> Your app is only as secure as its weakest library.

---

### ✅ Tools
| Tool | Use |
|------|-----|
| `npm audit` | Node.js |
| `pip-audit` | Python |
| **OWASP Dependency-Check** | Java, multi-language |
| **Snyk**, **Dependabot** | CI/CD integration |

```xml
<!-- Maven: OWASP Dependency-Check -->
<plugin>
  <groupId>org.owasp</groupId>
  <artifactId>dependency-check-maven</artifactId>
  <executions>
    <execution>
      <goals><goal>check</goal></goals>
    </execution>
  </executions>
</plugin>
```

---

## ✅ Final Tip

> 🎯 In interviews, **say this**:
> _"I treat the client as untrusted. I validate everything on the server, use parameterized queries, hash passwords with bcrypt, set secure cookies, and sanitize output to prevent XSS. I also scan dependencies and use CSP."_  

That shows **end-to-end security thinking**.

---
