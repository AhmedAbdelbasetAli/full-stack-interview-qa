# 🌐 Backend Web Development Networking Questions  
**HTTP, TLS, Load Balancing, APIs & Performance**

Here are **25+ targeted networking questions** for **backend web developers**, covering **HTTP, TLS, load balancing, API design, performance, and security** — with **in-depth explanations** and **interview-ready answers**.

Perfect for **system design interviews**, **backend roles**, and **cloud-native development**.

---

## 🔹 HTTP & HTTPS

### 1. What happens when you type a URL in the browser and press Enter?

**Answer**:  
The browser:
1. Parses the URL
2. DNS lookup to get IP
3. Establishes TCP connection (3-way handshake)
4. Sends HTTPS request (with TLS handshake if HTTPS)
5. Server processes and returns response
6. Browser renders HTML, loads assets

> ✅ Emphasize DNS → TCP → TLS → HTTP flow.

---

### 2. What is the difference between HTTP/1.1 and HTTP/2?

| Feature | HTTP/1.1 | HTTP/2 |
|--------|--------|--------|
| **Multiplexing** | ❌ One request per connection | ✅ Multiple requests over one connection |
| **Head of Line Blocking** | ✅ Yes | ❌ No |
| **Binary Format** | ❌ Text-based | ✅ Binary |
| **Header Compression** | ❌ No | ✅ HPACK |
| **Server Push** | ❌ No | ✅ Yes |
| **Performance** | Slower for many assets | Faster, fewer connections |

> ✅ Use in: High-performance APIs, SPAs with many assets.

---

### 3. How does TLS/SSL work? Explain the handshake.

**Answer**:  
1. **Client Hello**: Supported versions, cipher suites
2. **Server Hello**: Chooses cipher, sends cert
3. **Certificate Validation**: Client verifies cert
4. **Key Exchange**: Client generates session key, encrypts with server’s public key
5. **Encrypted Communication**: All data encrypted with session key

> ✅ Ensures confidentiality, integrity, authentication.

---

### 4. What is HSTS? Why is it important?

**Answer**:  
**HTTP Strict Transport Security** forces the browser to use HTTPS only.

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```

✅ Prevents SSL stripping attacks  
✅ Auto-upgrades `http://` to `https://`

---

### 5. What are the most important HTTP headers for a backend API?

| Header | Purpose |
|--------|--------|
| `Content-Type` | `application/json` |
| `Authorization` | `Bearer <token>` |
| `Content-Length` | Size of body |
| `Cache-Control` | Caching policy |
| `X-Request-ID` | For logging/tracing |
| `X-Forwarded-For` | Original client IP (behind proxy) |
| `User-Agent` | Client info |
| `Accept` | Preferred response format |

---

## 🔹 APIs & REST

### 6. How do you design a RESTful API?

**Answer**:  
- Use **nouns**, not verbs: `/users`, not `/getUsers`
- Use proper **HTTP methods**: `GET`, `POST`, `PUT`, `DELETE`
- Use **status codes**: `201 Created`, `404 Not Found`, `429 Too Many Requests`
- **Version APIs**: `/v1/users`
- **Filtering**: `/users?status=active&limit=10`
- **Pagination**: `Link` header or `offset/limit`

> ✅ Example: `GET /v1/orders?status=pending&page=2`

---

### 7. What is the difference between PUT and PATCH?

| Method | Purpose |
|--------|--------|
| `PUT` | **Replace** the entire resource |
| `PATCH` | **Partial update** — only fields sent are updated |

✅ Use `PUT` for full updates, `PATCH` for efficiency.

---

### 8. How do you handle API rate limiting?

**Answer**:  
- Use **token bucket** or **leaky bucket** algorithm
- Track requests per user/IP/API key
- Return `429 Too Many Requests`
- Use Redis for distributed rate limiting
- Headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`

✅ Example: 100 requests/minute per API key.

---

### 9. What is idempotency? Which HTTP methods are idempotent?

**Answer**:  
**Idempotent** = Multiple identical requests have the same effect as one.

| Method | Idempotent? |
|--------|------------|
| `GET` | ✅ Yes |
| `PUT` | ✅ Yes |
| `DELETE` | ✅ Yes |
| `POST` | ❌ No |
| `PATCH` | ⚠️ Sometimes (depends on implementation) |

✅ Important for retries in distributed systems.

---

## 🔹 Load Balancing & Proxies

### 10. How does a load balancer work?

**Answer**:  
A load balancer distributes incoming traffic across multiple backend servers.

Types:
- **L4 (Transport)**: Balances on IP + port (e.g., TCP)
- **L7 (Application)**: Balances on HTTP headers, path, cookies

Algorithms:
- Round Robin
- Least Connections
- IP Hash
- Weighted

✅ Used for **scalability**, **high availability**, **SSL termination**.

---

### 11. What is a reverse proxy? How is it different from a forward proxy?

| | Reverse Proxy | Forward Proxy |
|--|---------------|---------------|
| **Location** | In front of servers | In front of clients |
| **Purpose** | Load balancing, security, caching | Privacy, filtering, bypass restrictions |
| **Client sees** | Proxy IP | Destination IP |
| **Example** | Nginx, API Gateway | Corporate proxy, VPN |

✅ Backend devs use reverse proxies (Nginx, ALB) daily.

---

### 12. What is session affinity (sticky sessions)? When would you use it?

**Answer**:  
**Session affinity** routes a user’s requests to the same backend server.

Used when:
- Server stores session in **memory** (not Redis)
- App is **not stateless**

❌ Avoid in stateless microservices (use JWT, Redis)

✅ Configured via cookies or IP hash.

---

## 🔹 Performance & Optimization

### 13. How do you reduce latency in a backend API?

**Answer**:  
- Use **caching** (Redis, CDN)
- **Compress responses** (gzip)
- **Use HTTP/2 or HTTP/3**
- **Optimize database queries** (indexes, pagination)
- **Minimize round trips** (batching, GraphQL)
- **Deploy close to users** (multi-region)

> ✅ Latency = Distance + Processing + Queuing

---

### 14. What is a CDN? How does it improve performance?

**Answer**:  
**Content Delivery Network** caches static assets (JS, CSS, images) at edge locations.

✅ Reduces latency by serving from nearest location  
✅ Lowers origin load  
✅ Improves availability

Used by: Netflix, Amazon, Facebook

---

### 15. How do you monitor API performance?

**Answer**:  
Track:
- **Latency** (p50, p95, p99)
- **Error rate** (5xx, 4xx)
- **Throughput** (requests/sec)
- **Saturation** (CPU, memory)

Tools:
- Prometheus + Grafana
- Datadog, New Relic
- AWS CloudWatch
- ELK Stack

✅ Use **distributed tracing** (OpenTelemetry) for microservices.

---

## 🔹 Security

### 16. What is CORS? How do you configure it securely?

**Answer**:  
**Cross-Origin Resource Sharing** controls which domains can call your API.

Secure config:
```http
Access-Control-Allow-Origin: https://trusted-app.com
Access-Control-Allow-Credentials: true
Access-Control-Allow-Methods: GET, POST
Access-Control-Allow-Headers: Content-Type, Authorization
```

❌ Never use `*` with `Allow-Credentials: true`

---

### 17. What is CSRF? How is it different from XSS?

| | CSRF | XSS |
|--|------|-----|
| **Goal** | Make user perform unwanted action | Steal data, hijack session |
| **Attack Vector** | Trusted site | Malicious script injection |
| **Defense** | Anti-CSRF tokens, SameSite cookies | CSP, input validation, output encoding |
| **Requires Auth** | ✅ Yes | ❌ No |

✅ CSRF exploits trust in user’s session; XSS exploits trust in site.

---

### 18. How do you secure API keys?

**Answer**:  
- Never hardcode
- Store in **secrets manager** (AWS Secrets Manager, HashiCorp Vault)
- Use **short-lived tokens** (JWT) instead when possible
- Rotate regularly
- Restrict by IP or referrer
- Log access

✅ Prefer **OAuth 2.0 / OIDC** over static API keys.

---

## 🔹 WebSockets & Real-Time

### 19. When would you use WebSockets instead of REST?

**Answer**:  
Use WebSockets when:
- **Real-time updates** needed (chat, live dashboard)
- **Frequent updates** (stock prices)
- **Bidirectional communication** (gaming, collaboration)

❌ REST is better for CRUD, infrequent polling.

✅ WebSockets keep a persistent connection; REST is request-response.

---

### 20. How do you scale WebSockets?

**Answer**:  
- Use **message broker** (Redis Pub/Sub, Kafka)
- **Fan-out** messages from one instance to all connected clients
- Use **sticky sessions** or **externalize session state**
- **Shard by room/user** if needed

✅ Tools: Socket.IO, Pusher, AWS AppSync

---

## 🔹 DNS & Infrastructure

### 21. What is DNS? How does it work?

**Answer**:  
**Domain Name System** translates `example.com` → `93.184.216.34`

Process:
1. Browser checks cache
2. OS → Resolver (ISP or public DNS)
3. Resolver → Root → TLD → Authoritative DNS
4. Returns IP

✅ Use **TTL** to control caching.

---

### 22. What is a 301 vs 302 redirect?

| Code | Meaning | SEO Impact |
|------|--------|----------|
| `301 Moved Permanently` | Old URL is gone, use new one | ✅ Transfers SEO ranking |
| `302 Found (Temporary)` | Temporary move | ❌ Does not transfer SEO |

✅ Use 301 for permanent rebranding, 302 for A/B testing.

---

## 🔹 Advanced

### 23. What is gRPC? When would you use it over REST?

**Answer**:  
**gRPC** is a high-performance RPC framework using **Protocol Buffers** and **HTTP/2**.

Use gRPC when:
- **Microservices** communicate internally
- **Low latency**, **high throughput** needed
- **Strong typing** (via `.proto` files)
- **Streaming** (client, server, bidirectional)

❌ REST is better for public APIs, browsers, simplicity.

---

### 24. What is service mesh? How does it help backend services?

**Answer**:  
A **service mesh** (e.g., Istio, Linkerd) handles **service-to-service communication**:

✅ mTLS (mutual TLS)  
✅ Load balancing  
✅ Retries, timeouts  
✅ Observability (metrics, logs, traces)  
✅ Circuit breaking

✅ Runs as sidecar proxies — no code changes.

---

### 25. How do you debug high latency in a distributed system?

**Answer**:  
1. Use **distributed tracing** (OpenTelemetry, Jaeger)
2. Check **database queries** (slow logs, missing indexes)
3. Analyze **network hops** (CDN, LB, microservices)
4. Monitor **external dependencies** (APIs, queues)
5. Look for **lock contention**, **GC pauses**

✅ Start with p99 latency, drill down per service.

---

## ✅ Final Tip

> 🎯 In interviews, **draw the architecture**:  
> `User → CDN → LB → API → DB`  
> Then explain each layer’s role.

That shows **systems thinking** — exactly what senior roles want.

---
