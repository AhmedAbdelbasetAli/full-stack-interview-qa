# 🔐 Advanced Security Concepts  
**Least Privilege, Rate Limiting, 2FA, and Cryptographic Basics**

This document covers **essential security principles and practices** — critical for **building secure systems** and **protecting user data**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 1. What is the principle of least privilege?

> **"Give users and processes only the minimum permissions they need to do their job."**

It’s a **core security principle** to **limit damage** from breaches or mistakes.

---

### 🔹 1.1 Examples

| Scenario | Least Privilege Applied |
|--------|------------------------|
| **Database User** | App DB user has `SELECT`, `INSERT`, `UPDATE` — but not `DROP TABLE` |
| **Cloud IAM Role** | EC2 instance can only read from S3 bucket `logs/`, not write or delete |
| **User Roles** | Regular user can’t delete other users; only admins can |
| **Microservice** | Payment service can’t access user profiles — only its own data |

---

### 🔹 1.2 Benefits

| Benefit | How It Helps |
|--------|-------------|
| ✅ **Limits Attack Impact** | If compromised, attacker has limited access |
| ✅ **Prevents Accidental Damage** | Dev can’t accidentally drop production DB |
| ✅ **Easier Auditing** | Clear permission boundaries |
| ✅ **Compliance** | Required by standards like PCI-DSS, HIPAA |

---

### 📌 Interview Answer

> _"The principle of least privilege means giving only the minimum access needed. I apply it to database users, IAM roles, and application permissions. For example, my app’s database user can read and write to its tables but can’t drop tables. This limits damage if credentials are compromised."_  

---

## 2. What is rate limiting? Why is it important for security?

> **Rate limiting** restricts **how many requests a user or IP can make in a given time**.

It protects against **abuse, denial-of-service, and brute force attacks**.

---

### 🔹 2.1 How It Works

| Limit | Example |
|------|--------|
| `100 requests / minute per IP` | API abuse prevention |
| `5 login attempts / hour per user` | Brute force protection |
| `1000 requests / second per API key` | DDoS mitigation |

✅ When limit exceeded → return `429 Too Many Requests`

---

### 🔹 2.2 Real-World Examples

| Service | Rate Limit |
|--------|-----------|
| **Twitter API** | 300 requests/15 mins |
| **GitHub API** | 5000 requests/hour (authenticated) |
| **Google reCAPTCHA** | Triggers after suspicious activity |

---

### 🔹 2.3 Implementation (Spring Boot Example)

```java
@RestController
@RateLimit(maxRequests = 100, perSeconds = 60)
public class ApiController {
    @GetMapping("/data")
    public List<Data> getData() { ... }
}
```

Or use **Redis** for distributed rate limiting:
- Key: `rate_limit:user_123`
- Value: request count
- TTL: 60 seconds

---

### 🔹 2.4 Why It’s Important

| Threat | How Rate Limiting Helps |
|-------|------------------------|
| ❌ **Brute Force Attacks** | Limits login attempts |
| ❌ **DDoS / Scraping** | Prevents overwhelming the server |
| ❌ **API Abuse** | Stops bots from draining resources |
| ❌ **Credential Stuffing** | Slows down automated attacks |

---

### 📌 Interview Answer

> _"Rate limiting controls how many requests a client can make in a time window. I use it to prevent brute force attacks, DDoS, and API abuse. For example, I limit login attempts to 5 per hour. I implement it using Redis for distributed systems. It’s essential for availability and security."_  

---

## 3. What is brute force attack protection?

> **Brute force attack protection** prevents attackers from **guessing passwords** by trying **many combinations**.

---

### 🔹 3.1 Common Techniques

| Technique | How It Works |
|---------|-------------|
| ✅ **Rate Limiting** | Block after N failed attempts |
| ✅ **Account Lockout** | Temporarily lock account after 5–10 failures |
| ✅ **CAPTCHA** | Challenge human vs bot after suspicious activity |
| ✅ **Progressive Delays** | Wait 1s, then 2s, then 4s between attempts |
| ✅ **Multi-Factor Authentication (MFA)** | Even if password guessed, second factor blocks access |

---

### 🔹 3.2 Example: Secure Login Flow

```java
@PostMapping("/login")
public ResponseEntity<?> login(@RequestBody LoginRequest req) {
    User user = userRepo.findByEmail(req.getEmail());
    
    if (user == null || !passwordEncoder.matches(req.getPassword(), user.getPassword())) {
        failedAttemptService.recordFailure(req.getEmail());
        
        if (failedAttemptService.isBlocked(req.getEmail())) {
            return ResponseEntity.status(403).body("Account temporarily locked");
        }
        
        return ResponseEntity.status(401).body("Invalid credentials");
    }
    
    failedAttemptService.clear(req.getEmail());
    return ResponseEntity.ok(generateToken(user));
}
```

✅ `FailedAttemptService` tracks failures in Redis with TTL.

---

### 📌 Interview Answer

> _"Brute force protection stops attackers from guessing passwords. I combine rate limiting, temporary lockouts, and CAPTCHA after repeated failures. I never lock accounts permanently — only temporarily. I also use MFA as a final barrier. This balances security and usability."_  

---

## 4. What is two-factor authentication (2FA)? How does it work?

> **Two-Factor Authentication (2FA)** requires **two types of proof** to log in:
1. **Something you know** (password)
2. **Something you have** (phone, token) or **something you are** (fingerprint)

---

### 🔹 4.1 Types of 2FA

| Type | Example |
|------|--------|
| **SMS/Text Code** | Google sends 6-digit code |
| **Authenticator App** | Google Authenticator, Authy (TOTP) |
| **Hardware Token** | YubiKey |
| **Push Notification** | "Approve login?" on phone |
| **Biometrics** | Fingerprint, face ID |

---

### 🔹 4.2 TOTP (Time-Based One-Time Password)

- App generates 6-digit code based on:
  - Secret key (shared during setup)
  - Current time (30-second window)
- Server verifies using same algorithm

✅ No internet needed after setup.

---

### 🔹 4.3 Setup Flow

1. User enables 2FA
2. Server generates **secret key** and QR code
3. User scans QR into Google Authenticator
4. User enters current code to verify
5. On future logins: enter password + 6-digit code

---

### 🔹 4.4 Security Benefits

| Benefit | Explanation |
|--------|-------------|
| ✅ **Stops Password-Only Breaches** | Even if password leaked, attacker can’t log in |
| ✅ **Reduces Phishing Risk** | One-time codes can’t be reused |
| ✅ **Easy to Implement** | Libraries available for TOTP |

---

### 📌 Interview Answer

> _"2FA requires two proofs: something you know (password) and something you have (phone). I use TOTP with Google Authenticator — it’s secure, offline, and widely supported. On login, the user enters their password and a 6-digit time-based code. This prevents account takeover even if the password is compromised."_  

---

## 5. What is hashing vs encryption vs encoding?

| Concept | Purpose | Reversible? | Example |
|--------|--------|------------|--------|
| **Hashing** | Transform data → fixed-size digest | ❌ No | `SHA-256`, `bcrypt` (passwords) |
| **Encryption** | Transform data → unreadable, but reversible | ✅ Yes (with key) | `AES`, `RSA` (secure messages) |
| **Encoding** | Represent data in different format | ✅ Yes (no key) | `Base64`, `URL encoding` |

---

### 🔹 5.1 Hashing – One-Way Transformation

> Used for **passwords**, **integrity checks**.

```java
// Password hashing
String hash = BCrypt.hashpw("mypassword", BCrypt.gensalt());
// Store hash, not password

// Verify
if (BCrypt.checkpw(input, storedHash)) { ... }
```

✅ **Never reversible** — attacker can’t get original password  
✅ Use **bcrypt**, **scrypt**, **PBKDF2** for passwords (slow = good)

---

### 🔹 5.2 Encryption – Two-Way, Key-Based

> Used for **confidentiality** — data must be recovered.

```java
// Encrypt
byte[] encrypted = AES.encrypt("Secret data", key);

// Decrypt
String original = AES.decrypt(encrypted, key);
```

✅ **Symmetric**: Same key for encrypt/decrypt (`AES`)  
✅ **Asymmetric**: Public/private key (`RSA`)

---

### 🔹 5.3 Encoding – Data Representation

> Not security — just format change.

```java
// Base64 encoding
String encoded = Base64.getEncoder().encodeToString("Hello".getBytes());
// "SGVsbG8="

// Decode
String decoded = new String(Base64.getDecoder().decode(encoded));
// "Hello"
```

❌ **Not secure** — anyone can decode  
✅ Use for: embedding binary in JSON, URL-safe strings

---

### 📌 Interview Answer

> _"Hashing is one-way (used for passwords), encryption is two-way with a key (used for secure data), and encoding is format conversion (like Base64). I hash passwords with bcrypt, encrypt sensitive data with AES, and use Base64 to encode binary in JSON. Never use encoding for security — it's not encryption."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine defenses**:
> _"I apply least privilege, rate limit login attempts, require 2FA, hash passwords with bcrypt, and encrypt sensitive data."_  

That shows **defense-in-depth thinking** — exactly what senior roles want.

---

