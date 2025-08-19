# 🔐 Authentication & Authorization Deep Dive  
**Sessions, JWT, and Secure Token Storage**

This document provides a **comprehensive, in-depth explanation** of **authentication, authorization, sessions, and JWT** — essential for **secure web applications**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Diagrams (conceptual)
- ✅ Real-world examples
- ✅ Interview-ready answers

---

## 1. What is the difference between authentication and authorization?

> These are **two distinct but related security concepts**.

| Concept | Question | Example |
|--------|--------|--------|
| ✅ **Authentication** | "Who are you?" | Logging in with username/password |
| ✅ **Authorization** | "What can you do?" | Can this user delete a post? |

---

### 🔹 1.1 Authentication – Proving Identity

> The process of **verifying a user's identity**.

#### 🟦 Common Methods
- Password + username
- OAuth (Google, GitHub login)
- Biometrics (fingerprint, face ID)
- Multi-factor (SMS, TOTP)

✅ Once authenticated, the system knows **who you are**.

---

### 🔹 1.2 Authorization – Checking Permissions

> The process of **determining what an authenticated user can do**.

#### 🟦 Common Mechanisms
- **Roles**: `ADMIN`, `USER`, `MODERATOR`
- **Permissions**: `canDeletePost`, `canEditProfile`
- **Policies**: "Users can edit their own posts"

---

### 🔹 1.3 Example Flow

```text
User → Login (Authentication) → "You are Alice"
       ↓
Alice → Request: "Delete post 101" → Authorization → "Alice is owner → ✅ Allow"
```

---

### 📌 Interview Answer

> _"Authentication is proving who you are — like logging in. Authorization is checking what you’re allowed to do — like deleting a post. I authenticate users with JWT or sessions, then authorize with roles or permissions. They’re separate concerns: you can be authenticated but not authorized."_  

---

## 2. What is a session? How is it stored and managed on the server?

> A **session** is a **server-side mechanism** to **track a user’s state across multiple HTTP requests**.

Since HTTP is **stateless**, sessions help maintain login state.

---

### 🔹 2.1 How Sessions Work

```
[User] → Login → [Server: Create Session] → [Send Session ID (Cookie)]
                    ↓
             [Session Store: Redis, DB, Memory]
```

1. User logs in
2. Server creates a **session object** (e.g., `{userId: 123, role: 'USER'}`)
3. Stores it in **session store** (memory, Redis, DB)
4. Sends **session ID** to browser via `Set-Cookie: JSESSIONID=abc123`
5. Browser sends cookie with every request
6. Server looks up session by ID

---

### 🔹 2.2 Session Storage Options

| Storage | Pros | Cons |
|--------|------|------|
| **In-Memory** | Fast | Not scalable (per-server) |
| **Database** | Persistent | Slower |
| **Redis** | Fast, scalable, shared | Requires external service |

✅ **Redis is most common** in production.

---

### 🔹 2.3 Managing Sessions

| Operation | How |
|--------|-----|
| ✅ **Create** | On successful login |
| ✅ **Read** | On each request (via cookie) |
| ✅ **Update** | Modify session data |
| ✅ **Invalidate** | On logout or timeout |
| ✅ **Expire** | Set TTL (e.g., 30 minutes) |

---

### 📌 Interview Answer

> _"A session is server-side state tracking. On login, the server creates a session object and sends a session ID via cookie. The browser sends it back with each request. The server uses it to look up the session in Redis or memory. I use Redis for scalability and set timeouts for security. Sessions are secure but require server state."_  

---

## 3. What is a JWT (JSON Web Token)? What are its components?

> **JWT (JSON Web Token)** is a **compact, URL-safe token** for securely transmitting information between parties as a **JSON object**.

It’s **stateless** — no server-side storage needed.

---

### 🔹 3.1 JWT Structure: `Header.Payload.Signature`

```text
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ
.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

---

### 🔹 3.2 Components

| Part | Purpose | Example |
|------|--------|--------|
| **Header** | Algorithm & token type | `{"alg": "HS256", "typ": "JWT"}` |
| **Payload** | Claims (data) | `{"sub": "123", "name": "Alice", "role": "USER", "exp": 1516239022}` |
| **Signature** | Ensures token hasn’t been tampered with | `HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)` |

---

### 🔹 3.3 Types of Claims

| Claim | Purpose |
|------|--------|
| `iss` (Issuer) | Who issued the token |
| `sub` (Subject) | User ID |
| `aud` (Audience) | Who it’s for |
| `exp` (Expiration) | When it expires |
| `iat` (Issued At) | When it was issued |
| `role`, `permissions` | Custom claims |

---

### 📌 Interview Answer

> _"JWT is a self-contained token with three parts: header (algorithm), payload (claims like user ID and role), and signature (to verify integrity). It’s stateless — the server doesn’t store it. I use it to authenticate users: on login, I issue a signed JWT with user ID and role. The client sends it in `Authorization: Bearer`, and I verify the signature and check `exp`."_  

---

## 4. What are the pros and cons of using JWT vs session cookies?

| Feature | JWT | Session Cookies |
|--------|-----|-----------------|
| **Stateless** | ✅ Yes | ❌ No (server stores session) |
| **Scalability** | ✅ High (no shared session store) | ❌ Needs Redis/DB |
| **Storage** | Client (localStorage, cookie) | Server (Redis, DB) |
| **Size** | ❌ Larger (base64 JSON) | ✅ Small (just ID) |
| **Revocation** | ❌ Hard (until expiry) | ✅ Easy (delete from store) |
| **CSRF Protection** | ✅ Not needed (no cookie) | ❌ Must protect |
| **XSS Risk** | ❌ High if in localStorage | ✅ Lower (HttpOnly cookie) |
| **Mobile Friendly** | ✅ Yes (easy to pass in header) | ✅ Yes |
| **Standard** | ✅ Open standard (RFC 7519) | ✅ Traditional |

---

### 🔹 4.1 When to Use JWT

| Use Case | Why |
|--------|-----|
| ✅ **Microservices** | No shared session store |
| ✅ **Mobile Apps** | Easy token handling |
| ✅ **Single Page Apps (SPA)** | Stateless, works with APIs |
| ✅ **SSO (Single Sign-On)** | Pass token across services |

---

### 🔹 4.2 When to Use Sessions

| Use Case | Why |
|--------|-----|
| ✅ **Traditional Web Apps** | Server renders HTML |
| ✅ **Need Instant Revocation** | Admin logs out user |
| ✅ **High Security** | Sensitive systems (banking) |
| ✅ **CSRF is manageable** | With proper headers |

---

### 📌 Interview Answer

> _"JWT is stateless and great for APIs and microservices — no shared session store. But it’s hard to revoke before expiry. Sessions are stateful but allow instant logout and are more secure when using HttpOnly cookies. I use JWT for SPAs and mobile apps, and sessions for traditional server-rendered apps where I need quick revocation."_  

---

## 5. Where should you store tokens securely? (Frontend: localStorage vs cookies)

> This is a **critical security decision** — where you store the token affects **XSS and CSRF risk**.

---

### 🔹 5.1 Option 1: `localStorage`

```javascript
localStorage.setItem('token', jwt);
// On each request:
fetch('/api/data', {
  headers: { 'Authorization': 'Bearer ' + localStorage.getItem('token') }
});
```

#### ✅ Pros
- Easy to access in JavaScript
- Persists after page reload

#### ❌ Cons
- **Vulnerable to XSS**: Malicious script can read it
- Not sent automatically

> ❌ **Not recommended** for sensitive apps.

---

### 🔹 5.2 Option 2: Cookies (Secure, HttpOnly, SameSite)

```http
Set-Cookie: auth_token=xyz; HttpOnly; Secure; SameSite=Lax
```

#### ✅ Pros
- **HttpOnly**: Cannot be read by JavaScript → safe from XSS
- **Secure**: Only sent over HTTPS
- **SameSite**: Prevents CSRF
- Automatically sent with requests

#### ❌ Cons
- Requires CSRF protection (if `SameSite=None`)
- Slightly more complex

---

### 🔹 5.3 Best Practice: Use Cookies with `HttpOnly`

```http
Set-Cookie: auth_token=eyJhbG...; 
           Path=/; 
           HttpOnly; 
           Secure; 
           SameSite=Lax
```

✅ This is the **most secure option** for web apps.

> 🔁 Use `SameSite=Lax` for general use, `SameSite=Strict` for high-security.

---

### 🔹 5.4 Hybrid Approach (Advanced)

- Store **refresh token** in `HttpOnly` cookie
- Store **access token** in memory (not persisted)
- Use access token in `Authorization: Bearer`
- Refresh when expired

✅ Balances security and usability.

---

### 📌 Interview Answer

> _"I store tokens in `HttpOnly`, `Secure`, `SameSite=Lax` cookies — not localStorage. This prevents XSS attacks because JavaScript can’t read the token. The browser automatically sends it with requests. For SPAs, I use this with CSRF protection. I avoid localStorage because it’s vulnerable to XSS. Security > convenience."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine security layers**:
> _"I authenticate with JWT, issue it over HTTPS, store it in HttpOnly cookies, and validate it on every request."_  

That shows **deep, layered security thinking**.

---

