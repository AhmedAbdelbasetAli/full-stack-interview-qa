# 🧠 Node.js Deep Dive (Part 3)  
**Modules, Events, Scaling & Security**

This document covers **advanced Node.js concepts** — essential for **senior backend roles**, **system design**, and **performance optimization**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 11️⃣ What is the difference between `require` and `import`?

> Both are used to **load modules**, but they belong to **different module systems**.

---

### ✅ `require` (CommonJS)

- ✅ **Synchronous** — blocks execution until module is loaded
- ✅ Used in **Node.js** (default for `.js` files)
- ✅ Dynamic: can be used inside functions, conditionally
- ✅ Loads entire module at once

```js
// math.js
function add(a, b) { return a + b; }
module.exports = { add };

// app.js
const { add } = require('./math');
console.log(add(2, 3));
```

---

### ✅ `import` (ES Modules / ESM)

- ✅ **Asynchronous** in browser, **static** in Node.js
- ✅ Part of **ES6+ standard**
- ✅ Must be at **top level** (not inside functions)
- ✅ Supports **tree-shaking** (unused code removed)
- ✅ Uses `await import()` for dynamic imports

```js
// math.mjs
export function add(a, b) { return a + b; }

// app.mjs
import { add } from './math.mjs';
console.log(add(2, 3));
```

---

### 🔹 Key Differences

| Feature | `require` | `import` |
|--------|----------|---------|
| **Module System** | CommonJS | ES Modules (ESM) |
| **Loading** | Synchronous | Static (parse-time) |
| **Syntax** | `const x = require('...')` | `import { x } from '...'` |
| **File Extension** | `.js` | `.mjs` or `"type": "module"` in `package.json` |
| **Dynamic Import** | `require(condition ? 'a' : 'b')` | `const mod = await import(pkg)` |
| **Default Export** | `module.exports = ...` | `export default ...` |

---

### ✅ How to Use ESM in Node.js

Add to `package.json`:
```json
{
  "type": "module"
}
```

Or use `.mjs` extension.

---

### 📌 Interview Answer

> _"require is CommonJS, synchronous, and Node.js default. import is ES6 standard, static, and supports tree-shaking. I use import for new projects with 'type': 'module', and require for legacy code. For dynamic loading, I use await import()."_  

---

## 12️⃣ What is the EventEmitter class in Node.js? How do you use it?

> **EventEmitter** is a **core Node.js class** that enables **event-driven programming** — objects emit events, and listeners react to them.

It’s the foundation of **asynchronous Node.js APIs**.

---

### ✅ Built-In Example: `fs.readFile`
```js
const fs = require('fs');

fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

✅ Under the hood: `fs.readFile` uses `EventEmitter` to emit `'data'`, `'end'`, `'error'`

---

### ✅ Create a Custom EventEmitter

```js
const EventEmitter = require('events');

class BankAccount extends EventEmitter {
  constructor(balance = 0) {
    super();
    this.balance = balance;
  }

  deposit(amount) {
    this.balance += amount;
    this.emit('deposit', { amount, balance: this.balance });
  }

  withdraw(amount) {
    if (amount > this.balance) {
      this.emit('error', new Error('Insufficient funds'));
      return;
    }
    this.balance -= amount;
    this.emit('withdraw', { amount, balance: this.balance });
  }
}

// Usage
const account = new BankAccount(100);

account.on('deposit', (event) => {
  console.log(`Deposited $${event.amount}, new balance: $${event.balance}`);
});

account.on('withdraw', (event) => {
  console.log(`Withdrew $${event.amount}, new balance: $${event.balance}`);
});

account.on('error', (err) => {
  console.error('Transaction failed:', err.message);
});

account.deposit(50);   // Event: deposit
account.withdraw(30);  // Event: withdraw
account.withdraw(200); // Event: error
```

---

### ✅ EventEmitter Methods

| Method | Purpose |
|-------|--------|
| `.on(event, listener)` | Register a listener |
| `.emit(event, data)` | Trigger an event |
| `.once(event, listener)` | Listen once, then auto-remove |
| `.removeListener()` | Remove a listener |
| `.off(event, listener)` | Node.js 10+ (alias for removeListener) |

---

### 📌 Interview Answer

> _"EventEmitter enables event-driven programming. I use it to decouple components — like emitting 'userCreated' after signup. Listeners can send email, update analytics, or log. It's used internally by streams, HTTP, and fs. I extend EventEmitter for custom event sources."_  

---

## 13️⃣ How can you scale a Node.js application?

> Node.js is **single-threaded**, so scaling requires **multiple processes** or **clustering**.

---

### ✅ Scaling Strategies

| Strategy | How | Tools |
|--------|-----|-------|
| ✅ **Clustering** | Run multiple Node.js processes (one per CPU core) | `cluster` module |
| ✅ **Load Balancing** | Distribute traffic across multiple instances | Nginx, AWS ELB |
| ✅ **Microservices** | Split app into smaller services | Express, NestJS |
| ✅ **Caching** | Reduce DB load with Redis or memory | `node-cache`, Redis |
| ✅ **Message Queues** | Offload heavy tasks | RabbitMQ, Kafka |
| ✅ **Horizontal Scaling** | Deploy multiple instances on different machines | Docker, Kubernetes |

---

### ✅ Example: Scale with Load Balancer

```
[User] → [Nginx Load Balancer] → [Node.js Instance 1]
                                 → [Node.js Instance 2]
                                 → [Node.js Instance 3]
```

✅ Each instance runs on a separate process/machine

---

## 14️⃣ What is clustering in Node.js? How does the cluster module work?

> **Clustering** allows you to **run multiple Node.js processes** that **share the same port** — one **master** process manages multiple **worker** processes.

It leverages **multiple CPU cores**.

---

### ✅ How Cluster Module Works

```
[Master Process] → Forks → [Worker 1] (PID 1001)
                 → Forks → [Worker 2] (PID 1002)
                 → Forks → [Worker 3] (PID 1003)
```

- ✅ **Master**: Listens on port, distributes connections to workers
- ✅ **Workers**: Handle actual requests (each is a separate process)

---

### ✅ Example: Cluster Setup

```js
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
  console.log(`Master ${process.pid} is running`);

  // Fork workers
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  // Restart worker if it dies
  cluster.on('exit', (worker, code, signal) => {
    console.log(`Worker ${worker.process.pid} died`);
    cluster.fork(); // Replace it
  });

} else {
  // Workers share port
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end(`Hello from worker ${process.pid}\n`);
  }).listen(8080);

  console.log(`Worker ${process.pid} started`);
}
```

✅ Run: `node server.js`  
✅ Visit `http://localhost:8080` — different worker responds each time

---

### ✅ Benefits of Clustering

| Benefit | Explanation |
|--------|-------------|
| ✅ **CPU Utilization** | Uses all CPU cores |
| ✅ **Fault Tolerance** | If one worker crashes, others continue |
| ✅ **Zero Downtime Restarts** | Restart workers one by one |

---

## 15️⃣ How do you secure a Node.js application?

> Security is **non-negotiable** — even small apps handle sensitive data.

---

### ✅ Key Security Practices

| Area | Best Practice |
|------|---------------|
| ✅ **Dependencies** | Use `npm audit`, `snyk`, keep packages updated |
| ✅ **Input Validation** | Validate all user input (e.g., with `Joi`, `express-validator`) |
| ✅ **Sanitization** | Prevent XSS with `xss-filters`, `DOMPurify` |
| ✅ **Authentication** | Use `bcrypt` for passwords, JWT with short expiry |
| ✅ **HTTPS** | Always use TLS (Let’s Encrypt) |
| ✅ **Headers** | Set security headers: `helmet` middleware |
| ✅ **Rate Limiting** | Prevent brute force with `express-rate-limit` |
| ✅ **CORS** | Configure `cors` middleware properly |
| ✅ **Error Handling** | Don’t leak stack traces in production |
| ✅ **Environment Variables** | Store secrets in `.env` (add to `.gitignore`) |

---

### ✅ Example: Secure Express App

```js
const express = require('express');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');
const cors = require('cors');
const app = express();

// Security Middleware
app.use(helmet()); // Secure headers
app.use(cors({ origin: 'https://trusted.com' }));
app.use(rateLimit({ windowMs: 15 * 60 * 1000, max: 100 })); // 100 requests per 15 mins
app.use(express.json({ limit: '10kb' })); // Prevent large payloads

// Routes
app.post('/login', (req, res) => {
  // Validate input
  if (!req.body.email || !req.body.password) {
    return res.status(400).json({ error: 'Email and password required' });
  }
  // Authenticate...
});

// Global error handler
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Internal server error' });
});

app.listen(3000);
```

---

### 📌 Interview Answer

> _"I secure Node.js apps by updating dependencies, validating input, using helmet for headers, rate limiting, and HTTPS. I use bcrypt for passwords, JWT for auth, and never expose stack traces. I store secrets in environment variables and use CORS carefully. For scaling, I use clustering to utilize all CPU cores."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use clustering to scale across CPU cores, EventEmitter for event-driven logic, import for modern modules, and secure the app with helmet and rate limiting."_  

That shows **deep, integrated Node.js mastery**.

---

