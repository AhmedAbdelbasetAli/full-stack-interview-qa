# 🔐 Production Security Deep Dive  
**Secrets, IAM, API Security & Authentication**

This document covers **essential production security practices** — critical for **protecting sensitive data** and **building secure systems in the cloud**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 1. How do you secure secrets (API keys, DB passwords) in production?

> **Never hardcode secrets** in source code, config files, or version control.

Use **dedicated secret management tools**.

---

### 🔹 1.1 Anti-Patterns to Avoid

| ❌ Bad Practice | Risk |
|----------------|------|
| Hardcoding in code | `String dbPassword = "secret123"` → leaked in Git |
| Storing in `application.properties` | Same as above |
| Committing `.env` files | Exposed in repo |
| Using plain environment variables | Still vulnerable if logs leak |

---

### 🔹 1.2 Secure Solutions

| Solution | Use Case |
|--------|--------|
| ✅ **AWS Secrets Manager** | Rotate secrets, fine-grained access |
| ✅ **AWS Parameter Store (SSM)** | Simpler, cheaper than Secrets Manager |
| ✅ **HashiCorp Vault** | Multi-cloud, dynamic secrets, encryption as a service |
| ✅ **Azure Key Vault** | Azure-native secret management |
| ✅ **Google Secret Manager** | GCP-native |
| ✅ **Kubernetes Secrets** | For containerized apps (but encrypt etcd) |

---

### 🔹 1.3 Best Practices

| Rule | How |
|------|-----|
| ✅ **Never commit secrets to Git** | Use `.gitignore`, pre-commit hooks |
| ✅ **Use secret managers in production** | Fetch at startup or on-demand |
| ✅ **Rotate secrets regularly** | Automate rotation (e.g., RDS auto-rotation) |
| ✅ **Least privilege access** | Only specific services can read secrets |
| ✅ **Encrypt secrets at rest and in transit** | Enabled by default in most tools |

---

### 🔹 1.4 Example: AWS Secrets Manager (Java)

```java
AWSSecretsManager client = AWSSecretsManagerClientBuilder.defaultClient();
GetSecretValueRequest request = new GetSecretValueRequest().withSecretId("prod/db-password");
GetSecretValueResult result = client.getSecretValue(request);
String dbPassword = result.getSecretString();
```

✅ IAM policy controls who can read the secret.

---

### 📌 Interview Answer

> _"I never hardcode secrets. In production, I use AWS Secrets Manager or HashiCorp Vault to store API keys and passwords. The app fetches them at startup using IAM roles. Secrets are rotated automatically, and access is restricted via policies. This ensures secrets are never in code or logs."_  

---

## 2. What is environment variable injection? Is it secure by itself?

> **Environment variable injection** means passing configuration (like DB URLs, API keys) to an app via `ENV` variables — not hardcoded.

But **it’s not secure by itself**.

---

### 🔹 2.1 How It Works

```bash
# Set env var
export DB_PASSWORD="mysecretpassword"

# Run app
java -jar myapp.jar
```

✅ App reads:
```java
String password = System.getenv("DB_PASSWORD");
```

---

### 🔹 2.2 Is It Secure?

❌ **No — not by itself**.

| Risk | Explanation |
|------|-------------|
| ❌ **Logs can leak env vars** | If app logs all env vars |
| ❌ **Process list exposure** | `ps aux` may show env in some cases |
| ❌ **Not encrypted at rest** | If stored in plain text config |
| ❌ **No audit trail** | Who set the value? When? |

---

### 🔹 2.3 Secure Usage Pattern

✅ **Combine with a secret manager**:

```bash
# Fetch secret from AWS Secrets Manager
export DB_PASSWORD=$(aws secretsmanager get-secret-value --secret-id prod/db-password --query SecretString --output text)

# Run app
java -jar myapp.jar
```

✅ Now:
- Secret is **not in code or config**
- Fetched at **runtime**
- Access controlled by **IAM**

---

### 📌 Interview Answer

> _"Environment variables are better than hardcoding, but not secure by themselves. They can leak in logs or process lists. I use them only to inject secrets fetched from AWS Secrets Manager or Vault. The app never sees the raw secret in config — it’s injected at runtime with strict IAM controls."_  

---

## 3. What are IAM roles and policies in AWS/Azure/GCP?

> **IAM (Identity and Access Management)** controls **who can do what** in the cloud.

It’s the **foundation of cloud security**.

---

### 🔹 3.1 AWS: IAM Roles & Policies

| Concept | Purpose |
|--------|--------|
| **IAM User** | Human user (e.g., developer) |
| **IAM Role** | Identity for services (e.g., EC2, Lambda) — can be assumed |
| **Policy** | JSON document defining permissions (e.g., "can read S3 bucket") |

---

### 🔹 3.2 Example: EC2 with IAM Role

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-app-bucket/*"
    }
  ]
}
```

✅ Attach this policy to an **IAM role**, assign to EC2 → instance can read S3 **without credentials**.

---

### 🔹 3.3 Azure: Azure AD & RBAC

| Concept | Azure Equivalent |
|--------|------------------|
| IAM User | Azure AD User |
| IAM Role | Azure AD Application / Service Principal |
| Policy | Azure Role (e.g., Storage Blob Reader) |

✅ Use **Managed Identities** for VMs/Functions → auto-managed identity.

---

### 🔹 3.4 GCP: Service Accounts & IAM

| Concept | GCP Equivalent |
|--------|----------------|
| IAM Role | IAM Role (e.g., `roles/storage.objectViewer`) |
| Service Identity | Service Account |

✅ Attach service account to Compute Engine → auto tokens via metadata server.

---

### 🔹 3.5 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Principle of Least Privilege** | Grant only needed permissions |
| ✅ **Use Roles, Not Long-Term Keys** | Roles are temporary, auto-rotated |
| ✅ **Enable Logging & Monitoring** | CloudTrail, Azure Monitor, Cloud Audit Logs |
| ✅ **Rotate Keys Regularly** | Or better — use roles |

---

### 📌 Interview Answer

> _"IAM roles and policies define who can access what in the cloud. In AWS, I assign roles to EC2 instances so they can access S3 without hardcoded keys. In Azure, I use Managed Identities. In GCP, service accounts. I follow least privilege — only grant needed permissions. This is more secure than long-term access keys."_  

---

## 4. How do you secure an API (REST or GraphQL)?

> Securing an API involves **authentication, authorization, rate limiting, input validation, and transport security**.

---

### 🔹 4.1 Key Security Layers

| Layer | How to Secure |
|------|---------------|
| ✅ **Transport** | Enforce HTTPS (TLS) |
| ✅ **Authentication** | JWT, OAuth 2.0, API keys |
| ✅ **Authorization** | Role-based access (RBAC), attribute-based (ABAC) |
| ✅ **Input Validation** | Validate & sanitize all inputs |
| ✅ **Rate Limiting** | Prevent abuse and DDoS |
| ✅ **Logging & Monitoring** | Track access, detect anomalies |
| ✅ **Error Handling** | Don’t leak stack traces |
| ✅ **CORS** | Restrict origins |
| ✅ **Security Headers** | CSP, HSTS, X-Frame-Options |

---

### 🔹 4.2 REST API Security Example

```java
@RestController
@RequestMapping("/api")
@PreAuthorize("hasRole('USER')")
public class ApiController {

    @GetMapping("/data")
    @RateLimit(max = 100, perSecond = 60)
    public Data getData(@Valid @RequestParam Filter filter) {
        // Validate input
        // Return data
    }
}
```

✅ Protected by:
- HTTPS
- JWT auth
- Role check
- Rate limiting
- Input validation

---

### 🔹 4.3 GraphQL-Specific Risks

| Risk | Mitigation |
|------|-----------|
| ❌ **Query Complexity** | Limit depth, cost analysis |
| ❌ **Over-fetching/Under-fetching** | Not a security risk, but performance |
| ❌ **Introspection in Prod** | Disable or restrict |
| ❌ **N+1 Queries** | Use DataLoader |

✅ Use tools like `graphql-java` with query cost analysis.

---

### 📌 Interview Answer

> _"I secure APIs with HTTPS, JWT authentication, role-based authorization, and rate limiting. I validate all inputs, use security headers, and log access. For GraphQL, I limit query depth and disable introspection in production. I never expose sensitive data in errors. Security is layered — no single point of failure."_  

---

## 5. What is API key vs token-based authentication?

| Feature | API Key | Token-Based (JWT, OAuth) |
|--------|--------|--------------------------|
| **State** | Often stateless, but can be tracked | Stateless (JWT) or stateful |
| **Scope** | Usually per-application | Can be per-user, with roles |
| **Expiration** | Rarely expires (long-lived) | Short-lived (15min–1hr) |
| **Revocation** | Hard (must rotate key) | Easy (wait for expiry or blacklist) |
| **Security** | Lower — long-lived, no user context | Higher — short-lived, user claims |
| **Use Case** | Machine-to-machine, internal services | User-facing apps, mobile, SPAs |

---

### 🔹 5.1 API Key – Simple but Risky

```http
GET /api/data
Authorization: ApiKey abc123
```

✅ Pros:
- Simple to implement
- Good for internal microservices

❌ Cons:
- Long-lived → high impact if leaked
- No user context
- Hard to revoke

---

### 🔹 5.2 Token-Based – Secure & Flexible

```http
GET /api/data
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

✅ Pros:
- Short-lived
- Contains user info (JWT claims)
- Can be revoked (with token blacklist or short expiry)
- Works with OAuth/OIDC

---

### 🔹 5.3 When to Use Which?

| Use Case | Choice |
|--------|--------|
| ✅ Internal service-to-service | API Key (with IP allowlist) |
| ✅ Mobile app, SPA, public API | JWT / OAuth tokens |
| ✅ Legacy system | API Key (but rotate frequently) |
| ✅ High-security app | Always tokens |

---

### 📌 Interview Answer

> _"API keys are simple but less secure — they're long-lived and hard to revoke. Token-based auth (like JWT) is more secure: short-lived, contains user context, and supports revocation. I use API keys for internal services with IP restrictions, and tokens for user-facing APIs. Tokens integrate better with OAuth and provide better audit trails."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine security layers**:
> _"I store secrets in AWS Secrets Manager, inject them via IAM roles, secure APIs with JWT and rate limiting, and enforce HTTPS."_  

That shows **defense-in-depth thinking** — exactly what senior roles want.

---

