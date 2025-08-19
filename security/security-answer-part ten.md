# 🔐 Security Testing & Risk Management  
**CI/CD Security, Pen Testing, Zero-Day, OWASP & Dependency Updates**

This document covers **essential security practices** for **continuous security validation**, **threat awareness**, and **proactive risk management**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 1. How do you perform security testing in CI/CD?

> **Security testing in CI/CD** means **automating security checks** as part of the build and deployment pipeline — **shifting security left**.

It ensures **every code change is checked for vulnerabilities** before reaching production.

---

### 🔹 1.1 Key Security Checks in CI/CD

| Check | Tool Example | When to Run |
|------|-------------|------------|
| ✅ **Static Application Security Testing (SAST)** | SonarQube, Checkmarx, GitHub Code Scanning | On every push |
| ✅ **Software Composition Analysis (SCA)** | Snyk, Dependabot, OWASP Dependency-Check | On every build |
| ✅ **Secrets Detection** | GitGuardian, TruffleHog, detect-secrets | Pre-commit & CI |
| ✅ **Dynamic Application Security Testing (DAST)** | OWASP ZAP, Burp Suite | Nightly or pre-deploy |
| ✅ **Container Scanning** | Trivy, Clair, Snyk Container | On Docker build |
| ✅ **Infrastructure as Code (IaC) Scanning** | Checkov, tfsec, Snyk Infrastructure | On Terraform/CloudFormation changes |

---

### 🔹 1.2 Example: GitHub Actions CI/CD Pipeline

```yaml
name: CI/CD with Security

on: [push]

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # Secrets detection
      - name: Scan for secrets
        uses: gitguardian/gg-shield@v5
        with:
          scan-mode: staged

      # SCA: Check for vulnerable dependencies
      - name: Snyk Test
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --fail-on-vuln

      # SAST: Code analysis
      - name: SonarQube Scan
        uses: sonarsource/sonarqube-scan-action@master
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}

      # Build & Test
      - run: npm install && npm test

      # Container scan
      - name: Trivy Container Scan
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: 'myapp:${{ github.sha }}'
          format: 'table'
          exit-code: '1'
          ignore-unfixed: true
          severity: 'CRITICAL,HIGH'
```

✅ **Fails the build** on high-severity issues.

---

### 🔹 1.3 Benefits

| Benefit | How |
|--------|-----|
| ✅ **Early Detection** | Find vulnerabilities before deployment |
| ✅ **Automated Enforcement** | No bypassing security |
| ✅ **Consistency** | Every change is tested the same way |
| ✅ **Developer Feedback** | PR comments show issues |
| ✅ **Compliance** | Audit trail of security checks |

---

### 📌 Interview Answer

> _"I integrate security into CI/CD by running SAST, SCA, and secrets scanning on every build. Tools like Snyk and SonarQube fail the build on critical vulnerabilities. I also scan containers and IaC. This shifts security left — developers get feedback early, and we prevent risky code from reaching production."_  

---

## 2. What is penetration testing?

> **Penetration testing (pen test)** is an **authorized simulated cyberattack** on a system to **identify and exploit security vulnerabilities**.

It’s like **hiring a hacker to break into your system** — so you can fix it first.

---

### 🔹 2.1 Types of Pen Testing

| Type | Description |
|------|-------------|
| **Black Box** | Tester has no internal knowledge — simulates external attacker |
| **White Box** | Tester has full access (code, config) — deep internal review |
| **Gray Box** | Partial knowledge (e.g., user account) — realistic attack scenario |

---

### 🔹 2.2 Common Targets

| Target | Goal |
|-------|------|
| Web Applications | Find XSS, SQLi, auth flaws |
| APIs | Test for broken object level authorization |
| Network | Find open ports, misconfigured firewalls |
| Mobile Apps | Reverse engineer, check insecure storage |
| Cloud Infrastructure | Open S3 buckets, IAM misconfigurations |

---

### 🔹 2.3 Pen Test Process

1. **Reconnaissance** – Gather info (domains, IPs, tech stack)
2. **Scanning** – Use tools (Nmap, Burp, ZAP) to find entry points
3. **Exploitation** – Attempt to exploit vulnerabilities
4. **Post-Exploitation** – Escalate privileges, move laterally
5. **Reporting** – Document findings, risks, and fixes

---

### 🔹 2.4 Tools Used

| Tool | Purpose |
|------|--------|
| **Burp Suite** | Web app testing, intercepting requests |
| **OWASP ZAP** | Open-source alternative to Burp |
| **Metasploit** | Exploitation framework |
| **Nmap** | Network scanning |
| **SQLmap** | Automated SQL injection |
| **John the Ripper** | Password cracking |

---

### 📌 Interview Answer

> _"Penetration testing is a simulated attack to find real-world vulnerabilities. I use it to test web apps, APIs, and cloud infrastructure. Testers try to exploit issues like XSS, SQLi, or misconfigurations. I run pen tests annually or after major changes. It’s different from automated scans — it includes human creativity and deep exploitation."_  

---

## 3. What is a zero-day vulnerability?

> A **zero-day vulnerability** is a **security flaw that is unknown to the vendor** and **has no patch available**.

"Zero-day" means **zero days** between discovery and exploitation.

---

### 🔹 3.1 How Zero-Days Work

```
[Researcher/Attacker] → Discovers unpatched flaw → [Exploits it]  
       ↓
[Vendor] → Learns of exploit in wild → [Develops patch] → [Releases fix]
```

✅ The window between exploit and patch is when systems are most vulnerable.

---

### 🔹 3.2 Famous Examples

| Vulnerability | Impact |
|---------------|--------|
| **Log4Shell (CVE-2021-44228)** | RCE in Java apps via log messages |
| **Heartbleed (CVE-2014-0160)** | Steal memory from OpenSSL servers |
| **SolarWinds Supply Chain Attack** | Backdoor in software update |

---

### 🔹 3.3 How to Mitigate Zero-Day Risk

| Strategy | How |
|--------|-----|
| ✅ **Keep systems updated** | Patch as soon as fixes are available |
| ✅ **Use WAF with virtual patching** | Block exploit patterns before patch |
| ✅ **Minimize attack surface** | Disable unused services |
| ✅ **Network segmentation** | Limit lateral movement |
| ✅ **Threat intelligence** | Monitor for new zero-days |
| ✅ **Principle of Least Privilege** | Limit damage if compromised |

---

### 📌 Interview Answer

> _"A zero-day vulnerability is an unknown security flaw with no patch. Attackers exploit it before the vendor can fix it. I mitigate the risk by keeping systems updated, using WAFs for virtual patching, minimizing attack surface, and applying least privilege. I also monitor threat feeds for new zero-days like Log4Shell."_  

---

## 4. What is the OWASP Top 10? Name a few items.

> The **OWASP Top 10** is a **standard awareness document** for **critical web application security risks**, maintained by the Open Web Application Security Project (OWASP).

It’s updated every 3–4 years — latest is **OWASP Top 10: 2021**.

---

### 🔹 4.1 OWASP Top 10 (2021)

| Rank | Risk | Example |
|------|------|--------|
| **1** | **Broken Access Control** | Can access another user’s data |
| **2** | **Cryptographic Failures** | Weak hashing, plaintext passwords |
| **3** | **Injection** | SQLi, OS command injection |
| **4** | **Insecure Design** | Flawed architecture (e.g., no rate limiting) |
| **5** | **Security Misconfiguration** | Verbose errors, open S3 buckets |
| **6** | **Vulnerable and Outdated Components** | Using Log4j 1.x |
| **7** | **Identification and Authentication Failures** | Weak passwords, no MFA |
| **8** | **Software and Data Integrity Failures** | CI/CD hijacking, insecure updates |
| **9** | **Security Logging and Monitoring Failures** | No logs, delayed breach detection |
| **10** | **Server-Side Request Forgery (SSRF)** | Force server to access internal systems |

---

### 🔹 4.2 Why It Matters

- Used by **developers, auditors, and pentesters**
- Guides **security training and testing**
- Required for **compliance** (PCI-DSS, HIPAA)
- Helps prioritize **security efforts**

---

### 📌 Interview Answer

> _"The OWASP Top 10 is a list of the most critical web security risks. Top items include Broken Access Control, Cryptographic Failures, and Injection (like SQLi). I use it as a checklist to secure my apps — for example, validating access control, hashing passwords with bcrypt, and preventing SQLi with prepared statements. It’s essential for building secure software."_  

---

## 5. How do you keep third-party libraries up to date securely?

> **Outdated libraries are a top cause of breaches** (e.g., Log4Shell).

You must **update dependencies securely and continuously**.

---

### 🔹 5.1 Secure Update Process

#### ✅ 1. **Monitor for Vulnerabilities**
- Use **Snyk**, **Dependabot**, or **GitHub Alerts**
- Subscribe to security mailing lists

#### ✅ 2. **Automate Pull Requests**
- **Dependabot** or **Renovate** create PRs for updates
- Include changelogs and vulnerability details

#### ✅ 3. **Review Before Merging**
- Check for **breaking changes**
- Verify no **new vulnerabilities** introduced
- Run **automated tests**

#### ✅ 4. **Test in Staging**
- Deploy to staging
- Run integration and security tests

#### ✅ 5. **Deploy with Rollback Plan**
- Use canary or blue/green deployment
- Monitor logs and metrics
- Roll back if issues

---

### 🔹 5.2 Best Practices

| Rule | How |
|------|-----|
| ✅ **Pin versions** | Use lock files (`package-lock.json`, `pom.xml`) |
| ✅ **Minimize dependencies** | Less code = fewer risks |
| ✅ **Audit before adding** | Review new libraries |
| ✅ **Use LTS versions** | Long-term support, fewer breaking changes |
| ✅ **Set update cadence** | Weekly or monthly review |
| ✅ **Remove unused deps** | `npm prune`, `pip-autoremove` |

---

### 📌 Interview Answer

> _"I keep third-party libraries updated using Dependabot or Snyk to auto-create PRs. I review changelogs, run tests, and deploy to staging first. I pin versions, minimize dependencies, and audit new libraries. This ensures I patch vulnerabilities quickly without breaking the app. It’s a continuous process — not a one-time task."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine practices**:
> _"I use OWASP Top 10 as a guide, run SCA in CI/CD, perform pen tests, and update libraries automatically with Dependabot."_  

That shows **mature, proactive security thinking**.

---

