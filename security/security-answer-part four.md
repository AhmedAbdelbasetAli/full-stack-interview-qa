# 🔐 Advanced Web Security Deep Dive  
**Cookies, OAuth 2.0, OIDC, SSO & Password Reset**

This document covers **advanced security concepts** — essential for **secure authentication systems** and **enterprise applications**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Security best practices
- ✅ Interview-ready answers

---

## 1. What is the difference between `HttpOnly`, `Secure`, and `SameSite` cookie attributes?

These are **critical security attributes** that protect cookies from common attacks.

---

### 🔹 1.1 `HttpOnly`

> **Prevents JavaScript from accessing the cookie** — protects against **XSS (Cross-Site Scripting)**.

```http
Set-Cookie: auth_token=abc123; HttpOnly
```

✅ **Effect**: `document.cookie` cannot read it  
❌ Without it: XSS attack can steal the token

> ✅ **Use for**: Authentication cookies (JWT, session ID)

---

### 🔹 1.2 `Secure`

> **Ensures the cookie is only sent over HTTPS** — prevents **eavesdropping on HTTP**.

```http
Set-Cookie: auth_token=abc123; Secure
```

✅ **Effect**: Browser only sends cookie if connection is encrypted  
❌ Without it: Cookie sent over HTTP → visible to attackers

> ✅ **Use for**: All sensitive cookies (auth, session)

---

### 🔹 1.3 `SameSite`

> **Controls when cookies are sent with cross-origin requests** — prevents **CSRF (Cross-Site Request Forgery)**.

| Value | Behavior | Use Case |
|------|---------|--------|
| `SameSite=Strict` | Only sent in **same-site** requests | High-security (banking) |
| `SameSite=Lax` | Sent with **safe** cross-site requests (e.g., top-level navigation) | General web apps |
| `SameSite=None` | Sent with **all** cross-site requests | Required for third-party embeds (must also set `Secure`) |

```http
Set-Cookie: auth_token=abc123; SameSite=Lax; Secure
```

✅ **Lax is recommended** for most apps.

---

### 📌 Interview Answer

> _"HttpOnly prevents JavaScript access (XSS protection), Secure ensures the cookie is only sent over HTTPS, and SameSite controls cross-origin sending to prevent CSRF. I use `HttpOnly; Secure; SameSite=Lax` for authentication cookies — this defends against XSS, eavesdropping, and CSRF. Never omit these in production."_  

---

## 2. What is OAuth 2.0? What are its main roles?

> **OAuth 2.0** is an **authorization framework** that allows a **third-party app** to access a user’s resources **without sharing credentials**.

It’s **not authentication** — it’s **delegated authorization**.

---

### 🔹 2.1 Main Roles in OAuth 2.0

| Role | Description | Example |
|------|------------|--------|
| **Resource Owner** | The user who owns the data | Alice |
| **Client** | The app requesting access | Mobile app, web app |
| **Authorization Server** | Issues access tokens | Google, GitHub, Auth0 |
| **Resource Server** | Hosts the protected data | Google API (Gmail, Drive) |

---

### 🔹 2.2 Flow Overview (Authorization Code Flow)

```
[User] → [Client App] → [Authorization Server] → [User logs in]  
       ← [Authorization Code]  
[Client] → [Authorization Server] → [Exchange code for Access Token]  
       ← [Access Token]  
[Client] → [Resource Server] → [Access User Data]  
```

---

### 🔹 2.3 Common Grant Types

| Grant Type | Use Case |
|-----------|--------|
| **Authorization Code** | Web apps, mobile apps (most secure) |
| **Implicit** | Legacy SPAs (less secure) |
| **Client Credentials** | Service-to-service (no user) |
| **Refresh Token** | Get new access token after expiry |

---

### 🔹 2.4 Example: Login with Google

1. User clicks "Login with Google"
2. Redirect to Google (authorization server)
3. User logs in and consents
4. Google redirects back with **authorization code**
5. Your server exchanges code for **access token**
6. Use token to call Google APIs (e.g., get user email)

---

### 📌 Interview Answer

> _"OAuth 2.0 is an authorization framework where a client app gets limited access to a user’s data on a resource server, via an authorization server. Roles: user (resource owner), app (client), auth server (Google), and API (resource server). I use Authorization Code flow for security — it prevents token exposure and supports refresh tokens."_  

---

## 3. What is OpenID Connect (OIDC)? How does it differ from OAuth?

> **OpenID Connect (OIDC)** is an **authentication layer** built on top of **OAuth 2.0**.

It adds **user identity** — answering "Who are you?" using **ID tokens**.

---

### 🔹 3.1 Key Difference

| Feature | OAuth 2.0 | OpenID Connect |
|--------|----------|---------------|
| **Purpose** | Authorization (access resources) | Authentication (prove identity) |
| **Tokens** | Access Token | Access Token + **ID Token (JWT)** |
| **User Info** | Optional (via `/userinfo`) | Built-in (ID token contains claims) |
| **Standard** | RFC 6749 | OIDC spec (OAuth + extensions) |

---

### 🔹 3.2 ID Token (JWT)

The **ID token** is a **JWT** that contains user identity:

```json
{
  "sub": "1234567890",
  "name": "Alice",
  "email": "alice@example.com",
  "iat": 1516239022,
  "exp": 1516242622,
  "iss": "https://accounts.google.com",
  "aud": "my-client-id"
}
```

✅ Signed by the **authorization server** — can be verified.

---

### 🔹 3.3 OIDC Flow (Simplified)

Same as OAuth 2.0, but:
- Client requests `openid` scope
- Authorization server returns **ID token** + access token
- Client verifies ID token (signature, `exp`, `aud`)
- Extracts user info from ID token

---

### 📌 Interview Answer

> _"OAuth 2.0 is for authorization — letting apps access your data. OpenID Connect adds authentication — proving who you are. It does this with an ID token (JWT) that contains user info like name and email. I use OIDC for 'Login with Google/Facebook' — it’s OAuth 2.0 + identity. The ID token is verified and used to create a session."_  

---

## 4. What is SSO (Single Sign-On)? How does it work?

> **SSO (Single Sign-On)** allows a user to **log in once** and access **multiple applications** without logging in again.

---

### 🔹 4.1 How SSO Works

```
[User] → [App A] → [Redirect to SSO Server] → [Login]  
       ← [SSO Cookie]  
[User] → [App B] → [Redirect to SSO Server] → [SSO Cookie valid → Grant access]  
       ← [Access Token]  
```

✅ After first login, subsequent apps don’t ask for credentials.

---

### 🔹 4.2 SSO Protocols

| Protocol | Use Case |
|--------|--------|
| **SAML** | Enterprise (Okta, Azure AD) |
| **OpenID Connect** | Modern apps (Google, Auth0) |
| **OAuth 2.0** | With identity provider |

---

### 🔹 4.3 Example: Google Workspace SSO

- User logs into Gmail
- Goes to Google Drive → already logged in
- Goes to Google Calendar → already logged in
- All use the same SSO session

---

### 🔹 4.4 Benefits

| Benefit | Explanation |
|--------|-------------|
| ✅ **User Experience** | One login for many apps |
| ✅ **Security** | Centralized control, easier to revoke |
| ✅ **Admin Efficiency** | Add/remove users once |
| ✅ **Compliance** | Enforce MFA, audit log centrally |

---

### 📌 Interview Answer

> _"SSO lets users log in once and access multiple apps. It works via a central identity provider (like Google or Okta). On first login, the SSO server creates a session. When accessing another app, the user is redirected — the SSO server sees the session and grants access. I use OIDC or SAML for SSO in enterprise systems."_  

---

## 5. How do you securely implement password reset functionality?

> Password reset is a **high-risk feature** — if compromised, attackers can take over accounts.

---

### 🔹 5.1 Secure Password Reset Flow

```
[User] → "Forgot Password" → [Enter email]  
       → [Server sends reset link to email]  
       → [Link: https://app.com/reset?token=abc123]  
       → [Form: Enter new password]  
       → [Server: Verify token, hash password, invalidate token]  
```

---

### 🔹 5.2 Security Requirements

| Requirement | How to Implement |
|-----------|------------------|
| ✅ **Use Time-Limited Tokens** | Token expires in 15–60 mins |
| ✅ **One-Time Use** | Invalidate after use |
| ✅ **Secure Token Generation** | Cryptographically random (e.g., 32+ chars) |
| ✅ **No Sensitive Data in URL** | Token only — no user ID |
| ✅ **Rate Limiting** | Prevent brute-force on reset endpoint |
| ✅ **Email Confirmation** | Send email on successful reset |
| ✅ **Don’t Reveal Account Status** | Same message whether email exists or not |

---

### 🔹 5.3 Backend Implementation (Pseudocode)

```java
@PostMapping("/forgot-password")
public void forgotPassword(@RequestParam String email) {
    User user = userRepo.findByEmail(email);
    if (user != null) { // Don't reveal if user exists
        String token = generateSecureToken(); // 32+ char random
        PasswordReset reset = new PasswordReset(user.getId(), token, LocalDateTime.now().plusMinutes(30));
        resetRepo.save(reset);
        emailService.sendResetLink(email, token);
    }
    // Always return 200 — don't leak info
}

@GetMapping("/reset-password")
public String showResetForm(@RequestParam String token) {
    PasswordReset reset = resetRepo.findByToken(token);
    if (reset == null || reset.isExpired() || reset.isUsed()) {
        throw new InvalidTokenException();
    }
    return "reset-form";
}

@PostMapping("/reset-password")
public String resetPassword(@RequestParam String token, @RequestParam String newPassword) {
    PasswordReset reset = resetRepo.findByToken(token);
    validate(reset);

    User user = userRepo.findById(reset.getUserId());
    user.setPassword(passwordEncoder.encode(newPassword));
    userRepo.save(user);

    reset.setUsed(true);
    resetRepo.save(reset); // Invalidate token

    emailService.notifyPasswordChanged(user.getEmail());
    return "redirect:/login?reset=success";
}
```

---

### 📌 Interview Answer

> _"I implement password reset with time-limited, one-time-use tokens sent to the user’s email. The token is cryptographically random and expires in 15 minutes. I don’t reveal whether an email exists, rate-limit requests, and send a confirmation email after reset. The token is invalidated after use. This balances usability and security — preventing abuse while allowing legitimate users to recover access."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine security layers**:
> _"I use HttpOnly+Secure+SameSite cookies for sessions, OAuth 2.0 with OIDC for SSO, and secure password reset with expiring tokens."_  

That shows **deep, integrated security thinking**.


