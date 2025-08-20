# ☁️ AWS Deep Dive (Part 2)  
**API Gateway, ELB, ASG, CloudFront & RDS**

This document covers **core AWS services** for **scalable, secure, and high-performance architectures**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 1. What is API Gateway in AWS?

> **Amazon API Gateway** is a **fully managed service** that makes it easy to **create, publish, maintain, monitor, and secure APIs** at any scale.

It acts as a **front door** for your backend services (Lambda, EC2, containers, HTTP endpoints).

---

### 🔹 Key Features

| Feature | Description |
|--------|-------------|
| ✅ **REST & WebSocket APIs** | Build RESTful APIs or real-time WebSocket APIs |
| ✅ **Serverless Integration** | Directly invoke Lambda functions |
| ✅ **Request/Response Transformation** | Modify headers, body before/after backend |
| ✅ **Authentication & Authorization** | IAM, Cognito, Lambda Authorizers, OAuth |
| ✅ **Throttling & Rate Limiting** | Protect backend from abuse |
| ✅ **Caching** | Cache responses to reduce backend load |
| ✅ **Monitoring** | CloudWatch metrics, logging, X-Ray tracing |
| ✅ **Custom Domains & HTTPS** | Use your domain with ACM certificate |

---

### 🔹 Example: Serverless API

```text
[User] → HTTPS → [API Gateway] → [Lambda: Process Request]
                     ↓
               [Logs to CloudWatch]
```

✅ No servers to manage  
✅ Auto-scales with traffic  
✅ Pay per request

---

### 🔹 Use Cases

| Use Case | Why |
|--------|-----|
| ✅ **Serverless Backends** | Front-end for Lambda microservices |
| ✅ **Microservices Gateway** | Single entry point for multiple services |
| ✅ **Legacy System Exposure** | Expose internal APIs securely |
| ✅ **Mobile & Web APIs** | Secure, scalable, monitored APIs |

---

### 📌 Interview Answer

> _"API Gateway is AWS's managed service for creating and managing APIs. I use it to expose Lambda functions as REST APIs, add authentication with Cognito, and enforce rate limiting. It handles scaling, SSL, and monitoring. I use it for serverless backends and as a gateway for microservices."_  

---

## 2. What is Elastic Load Balancer (ELB)? What types are available?

> **Elastic Load Balancing (ELB)** automatically **distributes incoming application traffic across multiple targets** (EC2, containers, Lambda, IP addresses).

It increases **availability** and **fault tolerance**.

---

### 🔹 Types of Load Balancers

| Type | OSI Layer | Use Case | Key Features |
|------|---------|--------|------------|
| **Application Load Balancer (ALB)** | Layer 7 (HTTP/HTTPS) | Modern apps, microservices | Host/path-based routing, Lambda integration, WebSockets |
| **Network Load Balancer (NLB)** | Layer 4 (TCP/UDP) | High-performance, low-latency | Ultra-high performance, static IP, preserve source IP |
| **Classic Load Balancer (CLB)** | Layer 4 & 7 | Legacy apps | Basic load balancing (not recommended for new apps) |
| **Gateway Load Balancer (GWLB)** | Layer 3 (IP) | Deploy third-party virtual appliances | Transparently insert firewalls, IDS/IPS |

---

### 🔹 Example: ALB with Microservices

```text
[User] → HTTPS → [ALB]
                     ↓
           [Service A: /users → EC2]
           [Service B: /orders → ECS]
           [Service C: /reports → Lambda]
```

✅ ALB routes based on **path** or **host**

---

### 🔹 Key Features

| Feature | How |
|--------|-----|
| ✅ **Health Checks** | Automatically route traffic away from unhealthy instances |
| ✅ **Cross-Zone Load Balancing** | Distribute traffic across all AZs |
| ✅ **Sticky Sessions** | Route user to same backend (for session affinity) |
| ✅ **HTTPS Termination** | Decrypt SSL at LB, backend uses HTTP |
| ✅ **WAF Integration** | Protect against SQLi, XSS |

---

### 📌 Interview Answer

> _"ELB distributes traffic across multiple servers for high availability. I use ALB for HTTP apps with path-based routing, NLB for TCP/UDP with high performance, and avoid CLB for new apps. I enable health checks and HTTPS termination. It’s essential for scalable, resilient architectures."_  

---

## 3. What is Auto Scaling Group (ASG) in AWS?

> An **Auto Scaling Group (ASG)** automatically **launches and terminates EC2 instances** based on demand or a schedule.

It ensures **availability**, **fault tolerance**, and **cost efficiency**.

---

### 🔹 How ASG Works

```
[CloudWatch Alarms] → [ASG Policy] → [Launch/Terminate Instances]
       ↑
[CPU > 70% for 5 min]
```

1. Define **launch template** (AMI, instance type, key pair, security group)
2. Set **minimum**, **desired**, **maximum** capacity
3. Add **scaling policies**:
   - Target Tracking (e.g., 60% CPU)
   - Step Scaling (e.g., +2 instances if CPU > 80%)
   - Scheduled (e.g., scale up on Black Friday)

---

### 🔹 Key Features

| Feature | Description |
|--------|-------------|
| ✅ **Health Checks** | Replace unhealthy instances (EC2 or ELB health) |
| ✅ **Multiple Availability Zones** | Distribute instances across AZs |
| ✅ **Instance Protection** | Prevent specific instances from being terminated |
| ✅ **Lifecycle Hooks** | Pause during launch/termination for custom actions |
| ✅ **Warm Pools** | Keep pre-initialized instances ready (reduce cold start) |

---

### 🔹 Use Cases

| Use Case | Why |
|--------|-----|
| ✅ **Web/App Servers** | Scale with traffic |
| ✅ **Batch Processing** | Scale up for jobs, down after |
| ✅ **High Availability** | Replace failed instances automatically |
| ✅ **Cost Optimization** | Scale down during low traffic |

---

### 📌 Interview Answer

> _"ASG automatically manages EC2 instances based on demand. I define a launch template, min/desired/max size, and scaling policies. It replaces unhealthy instances and spans multiple AZs. I use it for web servers to handle traffic spikes and ensure availability. Combined with ELB, it’s the backbone of scalable apps."_  

---

## 4. What is CloudFront? How does it work with S3?

> **Amazon CloudFront** is a **global Content Delivery Network (CDN)** that securely delivers data, videos, applications, and APIs with **low latency and high transfer speeds**.

---

### 🔹 How CloudFront Works

```
[User in Tokyo] → [CloudFront Edge Location (Tokyo)]
                              ↓
                  [Origin: S3, EC2, ELB, etc.]
```

1. User requests `image.jpg`
2. CloudFront checks **edge cache**:
   - ✅ **Hit**: Serve from Tokyo (10ms)
   - ❌ **Miss**: Fetch from origin (200ms), cache for next time
3. Subsequent users get it from edge

---

### 🔹 Integration with S3

```text
[User] → [CloudFront] → [S3 Bucket (private)]
```

✅ **Best Practice**:  
- Keep S3 bucket **private**  
- CloudFront uses **Origin Access Identity (OAI)** to access S3  
- Users access only via CloudFront

---

### 🔹 Key Features

| Feature | Description |
|--------|-------------|
| ✅ **Global Edge Network** | 300+ locations worldwide |
| ✅ **HTTPS** | Free SSL/TLS with ACM |
| ✅ **Caching** | Configurable TTLs (min, default, max) |
| ✅ **Security** | WAF, DDoS protection (Shield), signed URLs/cookies |
| ✅ **Dynamic Content** | Cache API responses, not just static |
| ✅ **Lambda@Edge** | Run Lambda at edge locations (e.g., modify headers) |

---

### 🔹 Use Cases

| Use Case | How |
|--------|-----|
| ✅ **Static Websites** | S3 + CloudFront + Route 53 |
| ✅ **Video Streaming** | On-demand or live |
| ✅ **API Acceleration** | Cache responses at edge |
| ✅ **Software Distribution** | Fast global downloads |

---

### 📌 Interview Answer

> _"CloudFront is AWS's CDN. I use it with S3 to serve static assets globally with low latency. I keep S3 private and use OAI for secure access. It caches content at edge locations, supports HTTPS, and integrates with WAF. I also use Lambda@Edge for custom logic at the edge."_  

---

## 5. What is RDS? Which databases does it support?

> **Amazon RDS (Relational Database Service)** is a **managed relational database service** that makes it easy to **set up, operate, and scale** a relational database in the cloud.

You focus on data; AWS handles **backups, patching, replication, and failover**.

---

### 🔹 Supported Engines

| Database | Use Case |
|--------|--------|
| ✅ **Amazon Aurora** | MySQL/PostgreSQL-compatible, high performance, scalable |
| ✅ **MySQL** | Open-source, widely used |
| ✅ **PostgreSQL** | Advanced features, extensible |
| ✅ **MariaDB** | MySQL fork, open-source |
| ✅ **Oracle** | Enterprise apps, licensing included |
| ✅ **SQL Server** | Windows apps, .NET |

---

### 🔹 Key Features

| Feature | Description |
|--------|-------------|
| ✅ **Automated Backups** | Daily snapshots, point-in-time recovery (up to 35 days) |
| ✅ **Multi-AZ Deployment** | Synchronous standby in another AZ for failover |
| ✅ **Read Replicas** | Up to 5 async replicas for read scaling |
| ✅ **Automated Patching** | Apply OS/database patches during maintenance window |
| ✅ **Monitoring** | CloudWatch metrics (CPU, storage, connections) |
| ✅ **Encryption** | At rest (KMS) and in transit (SSL) |
| ✅ **Parameter Groups** | Customize database settings |

---

### 🔹 High Availability Setup

```
[Primary DB (us-east-1a)] ↔ [Standby DB (us-east-1b)]
       ↑
[Application] → Always connects to endpoint (AWS handles failover)
```

✅ If primary fails, RDS promotes standby → minimal downtime

---

### 🔹 When to Use RDS

| Use Case | Why |
|--------|-----|
| ✅ **Web/App Databases** | Managed, scalable, HA |
| ✅ **Enterprise Apps** | Oracle, SQL Server with licensing |
| ✅ **Production Workloads** | Automated backups, monitoring |
| ✅ **Read-Heavy Apps** | Add read replicas |

---

### 🔹 When NOT to Use RDS

| Anti-Pattern | Why |
|-------------|-----|
| ❌ **Serverless Apps** | Use **Aurora Serverless** or **DynamoDB** |
| ❌ **NoSQL Workloads** | Use **DynamoDB** |
| ❌ **Full Root Access Needed** | RDS restricts some OS-level access |

---

### 📌 Interview Answer

> _"RDS is AWS's managed relational database service. It supports MySQL, PostgreSQL, Oracle, SQL Server, and Aurora. I use it for production apps because AWS handles backups, patching, and failover. I enable Multi-AZ for high availability and read replicas for scaling reads. For serverless, I use Aurora Serverless."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine services**:
> _"I use RDS for the database, ALB to distribute traffic, ASG to scale EC2 instances, and CloudFront with S3 for static assets."_  

That shows **deep, integrated AWS architecture knowledge**.

---

