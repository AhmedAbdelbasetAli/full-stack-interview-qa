# ☁️ AWS Deep Dive (Part 3)  
**VPC, CloudWatch, CloudFormation, EBS vs Instance Store, Security**

This document covers **advanced AWS concepts** — essential for **cloud architecture**, **DevOps**, and **system design interviews**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 1. What is VPC? How do subnets, route tables, and security groups work?

> **VPC (Virtual Private Cloud)** is your **logically isolated section of the AWS cloud** where you launch AWS resources.

It’s your **private network in the cloud**.

---

### 🔹 Key Components

| Component | Purpose |
|--------|--------|
| **CIDR Block** | IP address range (e.g., `10.0.0.0/16`) |
| **Subnets** | Sub-ranges within VPC (e.g., `10.0.1.0/24`) — can be **public** or **private** |
| **Route Tables** | Control where network traffic is directed |
| **Internet Gateway (IGW)** | Enables communication between VPC and the internet |
| **NAT Gateway** | Allows private subnets to access the internet (outbound only) |
| **Security Groups** | Virtual **stateful** firewalls (instance-level) |
| **Network ACLs** | Virtual **stateless** firewalls (subnet-level) |

---

### 🔹 How They Work Together

#### ✅ Public Subnet
- Subnet has a route to **Internet Gateway (IGW)**
- Hosts resources that need public internet access (e.g., web servers)
- Attached to **public route table** with `0.0.0.0/0 → IGW`

#### ✅ Private Subnet
- No direct route to IGW
- Can access internet via **NAT Gateway** (in public subnet)
- Hosts sensitive resources (e.g., app servers, databases)
- Attached to **private route table** with `0.0.0.0/0 → NAT Gateway`

---

### 🔹 Example: 3-Tier Web App

```
[Internet]
    ↓
[Public Subnet: Load Balancer, Web Server]
    ↓
[Private Subnet: App Server] ←→ [Private Subnet: Database]
```

- Web server: Public subnet, internet access
- App server: Private subnet, accesses DB and internet via NAT
- Database: Private subnet, no internet access

---

### 🔹 Security Groups vs Network ACLs

| Feature | Security Group | Network ACL |
|--------|----------------|------------|
| **Scope** | Instance-level | Subnet-level |
| **State** | **Stateful** (return traffic allowed automatically) | **Stateless** (must allow return traffic) |
| **Rules** | Allow only | Allow/Deny |
| **Evaluation** | All rules evaluated | Rules processed in order (numbered) |
| **Default** | Deny all in/out | Allow all in/out |

✅ Use **Security Groups** for fine-grained control  
✅ Use **Network ACLs** for subnet-level defense (e.g., block malicious IPs)

---

### 📌 Interview Answer

> _"VPC is my isolated network in AWS. I use public subnets for load balancers and web servers, private subnets for app and DB. Route tables control traffic: public subnets use IGW, private use NAT Gateway. Security groups act as stateful firewalls on instances. This design ensures security and scalability."_  

---

## 2. What is CloudWatch? How is it used for monitoring?

> **Amazon CloudWatch** is a **monitoring and observability service** for AWS resources and applications.

It collects **metrics**, **logs**, and **events**, and allows you to **set alarms** and **take actions**.

---

### 🔹 Key Features

| Feature | Purpose |
|--------|--------|
| ✅ **Metrics** | Numerical data over time (e.g., CPU, memory, request count) |
| ✅ **Logs** | Collect and store log files from EC2, Lambda, containers |
| ✅ **Alarms** | Trigger actions when metrics breach thresholds (e.g., CPU > 80%) |
| ✅ **Events (EventBridge)** | React to changes (e.g., "EC2 instance stopped") |
| ✅ **Dashboards** | Visualize metrics and logs |
| ✅ **Synthetics** | Monitor endpoints with canaries |
| ✅ **Insights** | Query logs with structured syntax |

---

### 🔹 Example: Monitor EC2 CPU

```java
// CloudWatch collects CPU utilization every 1 minute (detailed monitoring)
// Create alarm:
//   - Metric: CPUUtilization
//   - Threshold: > 80% for 5 minutes
//   - Action: Send SNS notification or scale out ASG
```

✅ Alarm triggers **Auto Scaling** to add more instances.

---

### 🔹 Real-World Use Cases

| Use Case | How |
|--------|-----|
| ✅ **Auto Scaling** | Scale based on CloudWatch alarms |
| ✅ **Incident Response** | Alarm → SNS → PagerDuty |
| ✅ **Log Analysis** | Stream Lambda logs to CloudWatch, query with Insights |
| ✅ **Application Monitoring** | Use `PutMetricData` API to publish custom metrics |
| ✅ **Cost Monitoring** | Track spend with Cost Explorer (integrated) |

---

### 📌 Interview Answer

> _"CloudWatch is AWS's monitoring service. I use it to collect metrics (CPU, latency), logs, and set alarms. I trigger Auto Scaling when CPU is high, send alerts via SNS, and analyze logs with Insights. I also create dashboards to visualize system health. It's essential for observability and automation."_  

---

## 3. What is CloudFormation? How does it differ from Terraform?

> **AWS CloudFormation** is a **native AWS service** that allows you to **model, provision, and manage** AWS resources using **declarative templates**.

It’s **Infrastructure as Code (IaC)** for AWS.

---

### 🔹 How CloudFormation Works

- Define resources in **JSON or YAML template**
- CloudFormation **creates a stack** (collection of resources)
- Handles **dependencies**, **rollbacks on failure**, **updates**

```yaml
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-123456
      InstanceType: t3.micro
      SecurityGroupIds:
        - !Ref MySecurityGroup

  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow HTTP
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
```

✅ Run: `aws cloudformation create-stack --template-body file://template.yaml`

---

### 🔹 CloudFormation vs Terraform

| Feature | CloudFormation | Terraform |
|--------|----------------|-----------|
| **Provider** | AWS-native | HashiCorp (multi-cloud) |
| **Language** | JSON/YAML | HCL (HashiCorp Configuration Language) |
| **State Management** | AWS-managed | Local or remote backend (S3, Terraform Cloud) |
| **Multi-Cloud** | ❌ AWS only | ✅ AWS, Azure, GCP, etc. |
| **Drift Detection** | ✅ Yes (detect manual changes) | ✅ Yes |
| **Rollback** | ✅ Automatic on failure | ✅ Yes |
| **Learning Curve** | Steeper (AWS-specific) | Easier (cleaner syntax) |
| **Community** | Smaller | Large, rich modules |

---

### 🔹 When to Use Which?

| Scenario | Choice |
|--------|--------|
| ✅ **AWS-only, deep integration** | CloudFormation |
| ✅ **Multi-cloud, hybrid** | Terraform |
| ✅ **Enterprise AWS with compliance** | CloudFormation (native, auditable) |
| ✅ **Fast prototyping, rich modules** | Terraform |

---

### 📌 Interview Answer

> _"CloudFormation is AWS's native IaC service. I define resources in YAML, and it creates a stack with dependencies and rollback. Terraform is multi-cloud with cleaner HCL syntax. I use CloudFormation for AWS-only projects with tight integration, and Terraform for multi-cloud or when I need rich community modules."_  

---

## 4. What is EBS vs Instance Store?

| Feature | **EBS (Elastic Block Store)** | **Instance Store** |
|--------|-------------------------------|-------------------|
| **Type** | **Network-attached** storage (like a remote hard drive) | **Physically attached** to host (like local SSD) |
| **Persistence** | ✅ **Persistent** — survives instance stop/terminate (if not deleted) | ❌ **Ephemeral** — data lost when instance stops or fails |
| **Performance** | Good (GP3: 3,000 IOPS, 250 MB/s) | Excellent (up to 1M IOPS for NVMe) |
| **Use Case** | Boot volumes, databases, persistent data | Temporary storage, cache, buffers |
| **Backup** | ✅ Snapshots to S3 | ❌ No snapshots |
| **Resize** | ✅ Yes (while attached) | ❌ No |
| **Availability** | High (replicated within AZ) | Depends on host |

---

### 🔹 Example: Use EBS for Root Volume

```text
[EC2 Instance] ←→ [EBS Volume (root: /dev/xvda)]
```

✅ Stop instance → EBS persists → Start → same data

---

### 🔹 Example: Use Instance Store for Temp Processing

```text
[EC2 Instance (i3.large)] ←→ [Instance Store (NVMe SSD)]
```

✅ Use for:
- Caching (Redis, Memcached)
- Temporary processing (video encoding)
- High-speed scratch space

---

### 📌 When to Use Which?

| Scenario | Choice |
|--------|--------|
| ✅ **Root volume, DB, persistent data** | EBS |
| ✅ **High I/O, temporary data** | Instance Store |
| ✅ **Need snapshots** | EBS |
| ✅ **Low latency, high throughput** | Instance Store |

---

### 📌 Interview Answer

> _"EBS is persistent, network-attached storage — great for root volumes and databases. Instance Store is ephemeral, high-performance storage physically attached to the host — ideal for cache or temp processing. I use EBS for durability, Instance Store for speed when data is replaceable."_  

---

## 5. How do you secure data at rest and in transit in AWS?

> **Security is shared responsibility** — AWS secures the infrastructure, **you secure your data and access**.

---

### 🔹 Data at Rest (Stored Data)

| Service | How to Secure |
|--------|--------------|
| **S3** | ✅ SSE-S3 (AES-256), SSE-KMS (with key policy), SSE-C |
| **EBS** | ✅ Enable encryption (default in most regions) — uses KMS |
| **RDS** | ✅ Enable storage encryption — uses KMS |
| **DynamoDB** | ✅ Encryption at rest (KMS) |
| **EFS** | ✅ Enable encryption |
| **All** | ✅ Use **customer-managed KMS keys** for control and audit |

✅ **Best Practice**: Enable encryption by default across all services.

---

### 🔹 Data in Transit (Moving Data)

| Layer | How to Secure |
|------|---------------|
| **Application** | ✅ HTTPS (TLS) with ACM or custom cert |
| **Network** | ✅ Use VPC, private subnets, security groups |
| **Database** | ✅ SSL/TLS for RDS connections |
| **Internal** | ✅ mTLS for microservices (with App Mesh, Istio) |
| **All** | ✅ Use **CloudFront** or **API Gateway** with HTTPS |

✅ **Always use HTTPS**, never HTTP.

---

### 🔹 Key AWS Security Services

| Service | Purpose |
|--------|--------|
| ✅ **KMS (Key Management Service)** | Create and manage encryption keys |
| ✅ **Secrets Manager** | Store and rotate DB credentials, API keys |
| ✅ **IAM** | Control access with least privilege |
| ✅ **WAF & Shield** | Protect against DDoS, SQLi, XSS |
| ✅ **GuardDuty** | Threat detection (malware, unauthorized access) |
| ✅ **Config** | Track resource changes and compliance |

---

### 📌 Interview Answer

> _"I secure data at rest with encryption: SSE-KMS for S3, encrypted EBS volumes, and RDS storage encryption. For data in transit, I enforce HTTPS with ACM, use VPC for isolation, and secure internal traffic with security groups. I use KMS for key control, Secrets Manager for credentials, and WAF for web protection. Security is layered and automated."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine services**:
> _"I use VPC with public/private subnets, EBS for persistent DB storage, CloudWatch for monitoring, CloudFormation for IaC, and encrypt everything with KMS."_  

That shows **deep, integrated AWS mastery**.

---

