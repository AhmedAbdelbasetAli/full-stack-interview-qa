# ☁️ AWS Deep Dive (Part 1)  
**EC2, S3, IAM, Lambda & Storage Tiers**

This document covers **core AWS services** — essential for **cloud engineering**, **DevOps**, and **system design interviews**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 1. What is EC2? How do you launch and manage an EC2 instance?

> **Amazon EC2 (Elastic Compute Cloud)** is a **web service that provides resizable compute capacity in the cloud**.

It’s **virtual servers** you can launch on demand.

---

### 🔹 Key Features

| Feature | Description |
|--------|-------------|
| ✅ **Instance Types** | Optimized for compute, memory, GPU, etc. (e.g., `t3.micro`, `c5.xlarge`) |
| ✅ **AMI (Amazon Machine Image)** | Pre-configured template (OS, apps) |
| ✅ **Security Groups** | Virtual firewall (stateful) |
| ✅ **Key Pairs** | SSH access (private key) |
| ✅ **EBS (Elastic Block Store)** | Persistent storage (like a hard drive) |
| ✅ **Auto Scaling** | Automatically adjust capacity |
| ✅ **Spot Instances** | Bid on unused capacity (up to 90% off) |

---

### 🔹 How to Launch an EC2 Instance (Step-by-Step)

1. **Choose AMI**: Ubuntu, Amazon Linux, Windows
2. **Choose Instance Type**: `t3.micro` (free tier)
3. **Configure Instance**: Number, network (VPC), subnet
4. **Add Storage**: EBS volume (e.g., 8 GB GP2)
5. **Add Tags**: `Name=web-server`, `Env=prod`
6. **Configure Security Group**: Allow SSH (22), HTTP (80), HTTPS (443)
7. **Review and Launch**: Select or create a key pair

---

### 🔹 Managing EC2 Instances

| Task | How |
|-----|-----|
| ✅ **Connect via SSH** | `ssh -i key.pem ubuntu@<public-ip>` |
| ✅ **Monitor** | CloudWatch (CPU, memory, disk) |
| ✅ **Scale** | Manual or Auto Scaling Group |
| ✅ **Stop/Start** | Stop to save cost (EBS persists) |
| ✅ **Terminate** | Permanently delete (EBS deleted unless "Delete on Termination" = false) |
| ✅ **Use Systems Manager** | Run commands, patch OS without SSH |

---

### 📌 When to Use EC2

| Use Case | Why |
|--------|-----|
| ✅ **Web/App Servers** | Full control over OS and software |
| ✅ **Legacy Apps** | Can’t run on containers/serverless |
| ✅ **High-Performance Computing** | GPU instances for ML |
| ✅ **Databases** | RDS is better, but EC2 for custom DB setups |

---

### 📌 Interview Answer

> _"EC2 is AWS's virtual server service. I launch it by choosing an AMI, instance type, VPC, and security group. I use EBS for persistent storage and assign a key pair for SSH access. I manage it via CloudWatch, Auto Scaling, and Systems Manager. I use EC2 for full control — like running custom databases or legacy apps."_  

---

## 2. What is S3? What are its main use cases?

> **Amazon S3 (Simple Storage Service)** is an **object storage service** that offers **scalability, data availability, security, and performance**.

Think of it as a **global hard drive** for your apps.

---

### 🔹 Key Features

| Feature | Description |
|--------|-------------|
| ✅ **Unlimited Storage** | Store any amount of data |
| ✅ **Durability** | 99.999999999% (11 nines) |
| ✅ **Availability** | 99.99% for S3 Standard |
| ✅ **Buckets** | Top-level containers (globally unique name) |
| ✅ **Objects** | Files (up to 5 TB) with metadata |
| ✅ **Versioning** | Keep multiple versions of an object |
| ✅ **Encryption** | SSE-S3, SSE-KMS, SSE-C |
| ✅ **Lifecycle Policies** | Move to cheaper storage or delete after time |

---

### 🔹 Main Use Cases

| Use Case | Example |
|--------|--------|
| ✅ **Static Website Hosting** | HTML, CSS, JS, images |
| ✅ **Backup & Archive** | Database backups, logs |
| ✅ **Data Lake** | Store raw data for analytics (with Athena, Redshift) |
| ✅ **Media Storage** | Videos, images for apps |
| ✅ **Software Distribution** | ISO files, installers |
| ✅ **Disaster Recovery** | Copy on-premise data to S3 |

---

### 🔹 Example: Static Website

```text
User → CloudFront (CDN) → S3 Bucket (index.html, style.css)
```

✅ S3 serves the site globally with low cost and high durability.

---

### 📌 Interview Answer

> _"S3 is AWS's object storage for files. I use it for static websites, backups, media storage, and data lakes. It's durable, scalable, and secure. I enable versioning for safety and lifecycle policies to move old data to Glacier. It's the backbone of most cloud architectures."_  

---

## 3. What is the difference between S3 Standard, S3 Intelligent-Tiering, and S3 Glacier?

| Feature | S3 Standard | S3 Intelligent-Tiering | S3 Glacier |
|--------|-------------|------------------------|----------|
| **Use Case** | Frequently accessed data | Unknown or changing access patterns | Long-term archive (rare access) |
| **Availability** | 99.99% | 99.9% | Minutes to hours (retrieval time) |
| **Durability** | 99.999999999% | Same | Same |
| **Cost** | $$$ | $$ | $ |
| **Retrieval Fee** | None | None | Yes (varies by speed) |
| **Minimum Storage Duration** | None | 30 days | 90 days |
| **Retrieval Time** | Immediate | Immediate | 1 min – 12 hrs |

---

### 🔹 When to Use Which?

| Scenario | Choice |
|--------|--------|
| ✅ **Website assets, active data** | S3 Standard |
| ✅ **User uploads, logs (unknown access)** | S3 Intelligent-Tiering |
| ✅ **Backup, compliance, archive** | S3 Glacier (or Glacier Deep Archive for 7+ year) |

---

### 🔹 S3 Intelligent-Tiering

- Automatically moves objects between **frequent** and **infrequent access** tiers
- No retrieval fees
- Small monitoring fee (~$0.0025 per 1K objects/month)
- Ideal when you don’t know access patterns

---

### 📌 Interview Answer

> _"S3 Standard is for frequently accessed data. S3 Intelligent-Tiering automatically moves data between tiers based on usage — great for unpredictable access. S3 Glacier is for long-term archive with low cost but retrieval delays and fees. I use Standard for apps, Intelligent-Tiering for logs, and Glacier for backups."_  

---

## 4. What is IAM? How do you manage users, roles, and policies?

> **IAM (Identity and Access Management)** controls **who can do what** in AWS.

It’s the **foundation of AWS security**.

---

### 🔹 Core Concepts

| Concept | Purpose | Example |
|--------|--------|--------|
| **User** | Human or application identity | `alice@company.com` |
| **Group** | Collection of users | `Developers`, `Admins` |
| **Role** | Identity with permissions that can be assumed | EC2 instance role |
| **Policy** | JSON document defining permissions | "Can read S3 bucket X" |
| **Permission Boundary** | Limits max permissions for a user/role | Prevent privilege escalation |

---

### 🔹 How to Manage IAM

#### ✅ Users & Groups
- Create users → add to groups → attach policies to groups
- Use **MFA** for all users
- Rotate access keys regularly

#### ✅ Roles
- **EC2 Role**: Attach to instance → auto credentials via metadata
- **Cross-Account Role**: Access resources in another AWS account
- **SAML/SSO Role**: Federate with corporate identity (Okta, Azure AD)

#### ✅ Policies
- Use **AWS Managed Policies** (e.g., `AmazonS3ReadOnlyAccess`)
- Write **Custom Policies** for least privilege
- Attach to users, groups, or roles

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

---

### 🔹 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Principle of Least Privilege** | Grant only needed permissions |
| ✅ **Use Roles, Not Long-Term Keys** | Roles are temporary and auto-rotated |
| ✅ **Enable MFA** | Critical for root and admin users |
| ✅ **Rotate Credentials** | Or better — use roles |
| ✅ **Use IAM Access Analyzer** | Find unintended resource access |

---

### 📌 Interview Answer

> _"IAM controls access in AWS. I create users, add them to groups, and attach policies. I use roles for EC2 instances so they can access S3 without hardcoded keys. I follow least privilege — only grant needed permissions. I enable MFA and avoid long-term access keys. IAM is critical for security."_  

---

## 5. What is AWS Lambda? When would you use it?

> **AWS Lambda** is a **serverless compute service** that runs your code in response to **events** — without provisioning or managing servers.

You pay only for **execution time** (ms).

---

### 🔹 Key Features

| Feature | Description |
|--------|-------------|
| ✅ **Event-Driven** | Triggered by S3, API Gateway, DynamoDB, SQS, etc. |
| ✅ **Auto-Scaling** | Scales from 0 to thousands of instances |
| ✅ **Pay-Per-Use** | Billed per 1ms of execution |
| ✅ **No Server Management** | AWS handles OS, scaling, availability |
| ✅ **Cold Starts** | First invocation may be slow (100ms–2s) |
| ✅ **Timeout** | Max 15 minutes (900 seconds) |
| ✅ **Memory** | 128 MB to 10 GB (CPU scales with memory) |

---

### 🔹 Common Triggers

| Trigger | Use Case |
|--------|--------|
| **S3** | Process uploaded files (resize image, validate CSV) |
| **API Gateway** | Serverless REST APIs |
| **DynamoDB Streams** | React to database changes |
| **CloudWatch Events** | Scheduled tasks (cron) |
| **SQS** | Process messages from queue |
| **SNS** | Fan-out notifications |

---

### 🔹 When to Use Lambda

| Use Case | Why |
|--------|-----|
| ✅ **Event Processing** | File uploads, messages |
| ✅ **APIs with Variable Load** | Startups, bursty traffic |
| ✅ **Scheduled Tasks** | Daily reports, cleanup |
| ✅ **Microservices** | Small, independent functions |
| ✅ **Real-Time File Processing** | Virus scan, metadata extraction |

---

### 🔹 When NOT to Use Lambda

| Anti-Pattern | Why |
|-------------|-----|
| ❌ **Long-Running Tasks** | >15 min timeout |
| ❌ **High-Frequency, Low-Latency** | Cold starts hurt performance |
| ❌ **Stateful Apps** | No local storage (use EFS or external) |
| ❌ **Large Monoliths** | Hard to debug, deploy, and monitor |

---

### 📌 Interview Answer

> _"Lambda is AWS's serverless function service. I use it for event-driven tasks like processing S3 uploads or handling API requests via API Gateway. It auto-scales and charges per execution. I use it for APIs, background jobs, and integrations. But I avoid it for long-running or high-frequency tasks due to cold starts and cost."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine services**:
> _"I use S3 for storage, Lambda to process uploads, IAM for security, and EC2 for apps that need full control."_  

That shows **deep, integrated AWS knowledge**.

---
