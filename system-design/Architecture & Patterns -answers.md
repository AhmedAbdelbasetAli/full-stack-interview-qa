# 🏗️ System Design Deep Dive (Part 2)  
**Monolith vs Microservices, API Gateway, Service Discovery, and Circuit Breaker**

This document dives into **modern distributed system patterns** — essential for **senior backend roles**, **cloud architecture**, and **scalable applications**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Diagrams (conceptual)
- ✅ Real-world examples
- ✅ Interview-ready answers

---

## 1. What is the difference between monolithic and microservices architecture?

| Feature | Monolithic Architecture | Microservices Architecture |
|--------|-------------------------|----------------------------|
| **Structure** | Single, unified codebase | Multiple independent services |
| **Deployment** | One unit (all or nothing) | Independent per service |
| **Scaling** | Scale entire app | Scale individual services |
| **Tech Stack** | One stack for all | Different stacks per service |
| **Database** | Shared database | Per-service database (ideally) |
| **Complexity** | Simpler to develop | More complex (network, latency) |
| **Use Case** | Small teams, MVP | Large teams, high scale |

---

### 🔹 1.1 Monolithic Architecture

```
┌─────────────────────────────┐
│         Monolith            │
│                             │
│  [User] [Order] [Payment]   │
│       Shared Database       │
└────────────┬────────────────┘
             ↓
       [Single Server]
```

✅ Pros:
- Simple to develop and deploy
- Easy testing
- Fast inter-service calls (in-process)

❌ Cons:
- Hard to scale (scale whole app)
- Tight coupling
- One bug can bring down entire system
- Difficult for large teams to collaborate

> 🟨 Good for: Startups, MVPs, small apps

---

### 🔹 1.2 Microservices Architecture

```
[User] → [API Gateway] → [User Service]  
                       → [Order Service]  
                       → [Payment Service]  
                               ↓
                       [Per-Service Databases]
```

✅ Pros:
- Independent deployment
- Scalable (scale only hot services)
- Fault isolation (one service down ≠ full outage)
- Team autonomy (each team owns a service)

❌ Cons:
- Network complexity
- Latency (HTTP/RPC calls)
- Data consistency challenges
- Operational overhead (monitoring, logging)

> 🟨 Good for: Netflix, Uber, Amazon — large-scale systems

---

### 📌 Interview Answer

> _"A monolith is a single, tightly-coupled application where all components run in one process. Microservices break it into independent services that communicate over APIs. Monoliths are simpler but hard to scale; microservices are complex but allow independent scaling, deployment, and team ownership. I’d use a monolith for an MVP and microservices when scaling to millions of users."_  

---

## 2. What are the pros and cons of microservices?

---

### 🔹 2.1 Pros of Microservices

| Advantage | Explanation |
|---------|-------------|
| ✅ **Independent Deployment** | Deploy one service without affecting others |
| ✅ **Scalability** | Scale only high-load services (e.g., payment) |
| ✅ **Fault Isolation** | One service failure doesn’t crash the whole system |
| ✅ **Team Autonomy** | Teams can own services end-to-end |
| ✅ **Technology Flexibility** | Use different stacks (Java, Node.js, Go) |
| ✅ **Resilience** | With patterns like circuit breaker, retries |
| ✅ **CI/CD Friendly** | Small services → faster builds and tests |

---

### 🔹 2.2 Cons of Microservices

| Disadvantage | Explanation |
|------------|-------------|
| ❌ **Network Complexity** | More HTTP/RPC calls → latency, failures |
| ❌ **Distributed Transactions** | Hard to maintain ACID across services |
| ❌ **Data Consistency** | Eventual consistency, not immediate |
| ❌ **Operational Overhead** | Need monitoring, logging, tracing |
| ❌ **Testing Complexity** | Integration testing across services |
| ❌ **Service Discovery Needed** | How do services find each other? |
| ❌ **Latency** | Inter-service calls are slower than in-process |

---

### 📌 Interview Answer

> _"Pros: independent deployment, scalability, fault isolation, and team autonomy. Cons: network latency, data consistency issues, operational complexity, and distributed transaction challenges. I use microservices when the team and scale justify the complexity — not for small apps. Tools like Kubernetes, service mesh, and observability help manage the downsides."_  

---

## 3. What is an API Gateway? What responsibilities does it have?

> An **API Gateway** is a **single entry point** for all clients, routing requests to appropriate microservices.

It’s the **front door** to your system.

---

### 🔹 3.1 How It Works

```
[Client] → [API Gateway] → [User Service]
                          → [Order Service]
                          → [Payment Service]
```

---

### 🔹 3.2 Key Responsibilities

| Responsibility | Why It Matters |
|---------------|---------------|
| ✅ **Routing** | Direct `/users` → User Service, `/orders` → Order Service |
| ✅ **Authentication & Authorization** | Validate JWT, enforce access control |
| ✅ **Rate Limiting** | Prevent abuse (e.g., 1000 reqs/min per user) |
| ✅ **Caching** | Cache responses (e.g., `/products`) |
| ✅ **Request/Response Transformation** | Convert formats (XML → JSON) |
| ✅ **Load Balancing** | Distribute traffic across service instances |
| ✅ **Logging & Monitoring** | Track all requests in one place |
| ✅ **Circuit Breaking** | Stop calls to failing services |
| ✅ **SSL Termination** | Handle HTTPS, pass HTTP to services |

---

### 🔹 3.3 Example: API Gateway in Action

```http
GET /api/users/123
Authorization: Bearer xyz
```

API Gateway:
1. Validates JWT
2. Checks rate limit
3. Routes to `User Service`
4. Logs the request
5. Returns response

---

### 📌 Interview Answer

> _"An API Gateway is the single entry point for clients. It handles routing, auth, rate limiting, caching, and monitoring. It simplifies client logic and centralizes cross-cutting concerns. I use it in microservices to avoid repeating auth code in every service. Popular gateways: Kong, AWS API Gateway, Spring Cloud Gateway."_  

---

## 4. What is service discovery? How does it work?

> **Service Discovery** allows services to **find and communicate with each other** in a dynamic environment (e.g., cloud, containers).

Without it, you’d hardcode IPs — which change in dynamic environments.

---

### 🔹 4.1 Problem: Dynamic IPs

In cloud/Kubernetes:
- Services start/stop frequently
- IPs change
- You can’t hardcode `http://10.0.0.5:8080`

---

### 🔹 4.2 How Service Discovery Works

```
┌─────────────────┐       ┌─────────────────┐
│   Service A     │       │   Service B     │
│ "I'm alive!"    │──────▶│ "I'm alive!"    │
└─────────────────┘       └─────────────────┘
         │                         │
         ▼                         ▼
   ┌──────────────────┐    ┌──────────────────┐
   │                  │    │                  │
   │  Service Registry│◀───┤  Service Registry│
   │  (e.g., Eureka)  │    │  (e.g., Eureka)  │
   │                  │    │                  │
   └────────┬─────────┘    └──────────────────┘
            │
            │
            ▼
   ┌──────────────────┐
   │   Service C      │
   │ "Where is B?"    │──▶ "It's at 10.0.1.3:8080"
   └──────────────────┘
```

---

### 🔹 4.3 Two Models

| Model | How It Works | Example |
|------|--------------|--------|
| **Client-Side Discovery** | Client queries registry directly | Netflix Eureka |
| **Server-Side Discovery** | Load balancer or gateway does lookup | Kubernetes + DNS |

---

### 🔹 4.4 Common Tools

| Tool | Type |
|------|------|
| **Eureka** (Netflix) | Client-side |
| **Consul** (HashiCorp) | Both |
| **ZooKeeper** | Distributed coordination |
| **Kubernetes Services** | Built-in DNS-based discovery |

---

### 📌 Interview Answer

> _"Service discovery lets services find each other in dynamic environments like the cloud. When a service starts, it registers with a registry (e.g., Eureka). Others query the registry to find its IP. This avoids hardcoding endpoints. I use client-side discovery with Eureka or server-side with Kubernetes DNS. It’s essential for resilience and scaling."_  

---

## 5. What is the circuit breaker pattern? Why use it?

> The **Circuit Breaker** pattern **prevents cascading failures** by **stopping calls to a failing service** after a threshold.

Like a real circuit breaker — it "trips" to prevent damage.

---

### 🔹 5.1 Problem: Cascading Failures

```
Service A → Service B (down) → A waits, threads block → A becomes slow → Clients time out → A crashes
```

✅ One failing service brings down the whole system.

---

### 🔹 5.2 How Circuit Breaker Works

The circuit has **three states**:

| State | Behavior |
|------|---------|
| **Closed** | Normal operation — calls go through |
| **Open** | Failing — all calls fail immediately (no network call) |
| **Half-Open** | Testing — allows a few calls to check if service is back |

```
Failures > Threshold → Closed → Open
After Timeout → Open → Half-Open
Success → Half-Open → Closed
Failures → Half-Open → Open
```

---

### 🔹 5.3 Example: Using Resilience4j (Java)

```java
CircuitBreakerConfig config = CircuitBreakerConfig.custom()
    .failureRateThreshold(50)
    .waitDurationInOpenState(Duration.ofMillis(1000))
    .slidingWindowType(SlidingWindowType.COUNT_BASED)
    .slidingWindowSize(5)
    .build();

CircuitBreaker circuitBreaker = CircuitBreaker.of("paymentService", config);

Supplier<String> decorated = CircuitBreaker
    .decorateSupplier(circuitBreaker, () -> callPaymentService());

String result = Try.ofSupplier(decorated).get();
```

---

### 🔹 5.4 Benefits

| Benefit | Explanation |
|--------|-------------|
| ✅ **Prevents Cascading Failures** | Stops one failure from spreading |
| ✅ **Improves Responsiveness** | Fail fast instead of timeout |
| ✅ **Allows Recovery** | Gives failing service time to recover |
| ✅ **Better User Experience** | Return fallback (e.g., cached data) |

---

### 📌 Interview Answer

> _"The circuit breaker pattern prevents cascading failures by stopping calls to a failing service after a threshold. It has three states: Closed (normal), Open (failing), and Half-Open (testing). I use it with tools like Resilience4j or Hystrix to make systems resilient. When a service fails, the circuit opens — calls fail fast, and the system can return a fallback response instead of timing out."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine patterns**:
> _"I use microservices with an API Gateway, service discovery for dynamic lookups, and circuit breakers to prevent cascading failures."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---



Just say: _"Let’s do [topic]"_

You're mastering **distributed systems** like a senior architect. 💪
