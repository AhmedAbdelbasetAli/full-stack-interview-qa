# 🔐 Advanced Cryptography & Security  
**Salting, Password Hashing, Encryption, Signatures & CAs**

This document covers **essential cryptographic concepts** — critical for **secure authentication**, **data protection**, and **system design**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 1. What is salting in password hashing? Why is it important?

> **Salting** means adding a **random, unique value** to a password before hashing it.

It prevents **rainbow table attacks** and ensures **identical passwords have different hashes**.

---

### 🔹 1.1 Problem: Without Salting

```text
Password: "password123"
Hash (SHA-256): ef92b778bafe771e89245b89ecbc08a44a4e166c06659911881f383d4473e94f
```

✅ If two users have the same password → **same hash**  
✅ Attacker can use a **precomputed rainbow table** to reverse common hashes

---

### 🔹 1.2 Solution: Add a Salt

```text
Password: "password123"
Salt: "a1b2c3d4e5" (random, per-user)
Hash = SHA-256("password123" + "a1b2c3d4e5")
```

✅ Now:
- Same password → different hash (due to unique salt)
- Rainbow tables useless (would need one per salt)
- Attacker must brute-force each password individually

---

### 🔹 1.3 How Salting Works in Practice

```java
// On user registration
String salt = generateRandomSalt(); // e.g., 16 bytes
String hash = hashPassword(password, salt);

// Store both in DB
user.setSalt(salt);
user.setPasswordHash(hash);
```

✅ Salt is **not secret** — it’s stored in plain text alongside the hash.

---

### 🔹 1.4 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Unique salt per user** | Prevents identical hashes |
| ✅ **Cryptographically random** | Not predictable |
| ✅ **At least 16 bytes** | Harder to guess |
| ✅ **Never reuse salts** | Defeats the purpose |

---

### 📌 Interview Answer

> _"Salting adds a unique, random value to each password before hashing. This ensures even identical passwords produce different hashes, preventing rainbow table attacks. The salt is stored with the hash — it doesn’t need to be secret. I always use per-user salts when storing passwords."_  

---

## 2. Which hashing algorithms are suitable for passwords (e.g., bcrypt, scrypt, Argon2)?

> **Not all hash functions are good for passwords**.

❌ **Avoid**: `MD5`, `SHA-1`, `SHA-256` — they’re **too fast** for passwords.

✅ **Use**: **Key Derivation Functions (KDFs)** that are **slow and memory-hard**.

---

### 🔹 2.1 Recommended Password Hashing Algorithms

| Algorithm | Why It's Good | Use Case |
|---------|--------------|--------|
| **bcrypt** | Slow, adaptive, widely supported | Most common choice |
| **scrypt** | Memory-hard — resists GPU/ASIC attacks | High-security apps |
| **Argon2** | Winner of Password Hashing Competition (2015) | Modern systems |
| **PBKDF2** | NIST-approved, but less resistant to hardware attacks | Legacy compliance |

---

### 🔹 2.2 Why Speed Matters

- Fast hash (SHA-256): 100M hashes/second on GPU
- Slow hash (bcrypt): 10–100 hashes/second

✅ Slower = harder to brute-force.

---

### 🔹 2.3 Example: Using bcrypt in Java

```java
// Hash password
String hashed = BCrypt.hashpw("mypassword", BCrypt.gensalt());

// Verify
if (BCrypt.checkpw(inputPassword, storedHash)) {
    // Login successful
}
```

✅ `gensalt()` includes a random salt automatically.

---

### 📌 Interview Answer

> _"I use bcrypt, scrypt, or Argon2 for password hashing — never MD5 or SHA-256. These are slow, adaptive functions designed to resist brute-force attacks. Bcrypt is most common and well-supported. I use libraries that handle salting automatically. The goal is to make password cracking computationally expensive."_  

---

## 3. What is symmetric vs asymmetric encryption?

| Feature | Symmetric Encryption | Asymmetric Encryption |
|--------|----------------------|------------------------|
| **Keys** | One shared key (secret) | Two keys: public + private |
| **Speed** | Fast | Slow |
| **Use Case** | Encrypting large data | Key exchange, digital signatures |
| **Examples** | AES, DES | RSA, ECC |
| **Key Distribution** | Hard (must be shared securely) | Easy (public key can be published) |

---

### 🔹 3.1 Symmetric Encryption

```text
Sender: Data + Secret Key → Encrypted Data
Receiver: Encrypted Data + Same Secret Key → Decrypted Data
```

✅ **Pros**: Fast, good for bulk data  
❌ **Cons**: Key must be shared securely (e.g., via asymmetric encryption)

> 🔐 Used in: **TLS session keys**, **database encryption**

---

### 🔹 3.2 Asymmetric Encryption

```text
Public Key: Encrypt → Only Private Key can Decrypt
Private Key: Sign → Anyone with Public Key can Verify
```

✅ **Pros**: No key sharing needed  
❌ **Cons**: Slow, not for large data

> 🔐 Used in: **TLS handshake**, **digital signatures**, **SSH**

---

### 🔹 3.3 Hybrid Approach (TLS Example)

1. Client and server use **asymmetric encryption** (RSA) to agree on a **session key**
2. Then use **symmetric encryption** (AES) with that key for the rest of the session

✅ Best of both worlds.

---

### 📌 Interview Answer

> _"Symmetric encryption uses one shared key — fast, good for bulk data. Asymmetric uses a public/private key pair — slower, but secure key exchange. I use symmetric (AES) for encrypting data at rest, and asymmetric (RSA) for TLS and digital signatures. In practice, systems like TLS use both: asymmetric to exchange a symmetric session key."_  

---

## 4. What is a digital signature?

> A **digital signature** is a **cryptographic technique** that verifies:
- ✅ **Authenticity**: Who signed it?
- ✅ **Integrity**: Has it been tampered with?
- ✅ **Non-repudiation**: Sender can’t deny signing

It uses **asymmetric encryption**.

---

### 🔹 4.1 How It Works

```text
Sender:
  1. Hash the message
  2. Encrypt hash with **private key** → Signature
  3. Send: Message + Signature

Receiver:
  1. Hash the message
  2. Decrypt signature with **public key** → Original hash
  3. Compare hashes → If match, message is valid
```

✅ Only the sender could have created the signature (they have the private key).

---

### 🔹 4.2 Real-World Uses

| Use Case | Example |
|--------|--------|
| ✅ **Code Signing** | Verify software isn’t tampered |
| ✅ **Document Signing** | PDFs, contracts |
| ✅ **TLS/SSL** | Server proves identity |
| ✅ **Blockchain** | Verify transactions |

---

### 🔹 4.3 Example: Signing a JWT

```java
// Server signs JWT with private key
String jwt = Jwts.builder()
    .setSubject("user123")
    .signWith(SignatureAlgorithm.RS256, privateKey)
    .compact();

// Client verifies with public key
Jwts.parser()
    .setSigningKey(publicKey)
    .parseClaimsJws(token);
```

✅ Ensures JWT wasn’t forged.

---

### 📌 Interview Answer

> _"A digital signature proves a message came from a specific sender and hasn’t been altered. The sender signs the message hash with their private key. The receiver verifies it with the public key. I use it in JWTs, code signing, and APIs to ensure authenticity and integrity."_  

---

## 5. What is a Certificate Authority (CA)?

> A **Certificate Authority (CA)** is a **trusted third party** that **issues digital certificates** to verify the identity of websites, people, or organizations.

It’s the **foundation of trust on the internet**.

---

### 🔹 5.1 How CAs Work

```
[Website] → [Generates CSR] → [CA] → [Issues SSL Certificate]
       ↑                              ↓
       └──── [Browser trusts CA] ← [Signed by CA]
```

1. Website owner generates a **Certificate Signing Request (CSR)**
2. CA verifies ownership (e.g., email, DNS)
3. CA signs the certificate with its **private key**
4. Browser trusts the CA → trusts the site

---

### 🔹 5.2 Types of CAs

| Type | Example |
|------|--------|
| **Public CA** | Let's Encrypt, DigiCert, Sectigo |
| **Private CA** | Internal PKI for corporate networks |

✅ **Let's Encrypt** issues free certificates automatically.

---

### 🔹 5.3 Certificate Chain

```
Root CA → Intermediate CA → Website Certificate
```

✅ Browsers ship with **trusted root CA certificates**.

---

### 🔹 5.4 Why CAs Are Critical

| Benefit | How |
|--------|-----|
| ✅ **Trust** | You know `https://google.com` is really Google |
| ✅ **Encryption** | Enables HTTPS (TLS) |
| ✅ **Phishing Prevention** | Fake sites can’t get valid certs easily |

---

### 📌 Interview Answer

> _"A Certificate Authority (CA) is a trusted entity that issues SSL/TLS certificates. It verifies a website’s identity and signs its certificate. Browsers trust major CAs — so when you visit an HTTPS site, you know it’s legitimate. I use Let's Encrypt for free, automated certificates in production."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I store passwords with bcrypt (salted, slow hash), use TLS with CA-signed certificates, and sign JWTs with RSA for authenticity."_  

That shows **deep, integrated security thinking**.

---
