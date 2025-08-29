# 🧠 Node.js Deep Dive (Part 4)  
**Non-Blocking I/O, Child Processes, Debugging, Paths & Environment Variables**

This document covers **advanced Node.js concepts** — essential for **performance**, **system integration**, **debugging**, and **production readiness**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 16️⃣ What is non-blocking I/O in Node.js?

> **Non-blocking I/O** means that **I/O operations (file, network, DB) do not block the main thread** — instead, they run **asynchronously**, allowing Node.js to handle other requests.

This is the **core of Node.js performance**.

---

### ✅ How It Works

```js
console.log("1. Start");

// Non-blocking file read
fs.readFile('data.txt', 'utf8', (err, data) => {
  console.log("3. File data:", data);
});

console.log("2. This runs before the file is read");
```

✅ **Output**:
```
1. Start
2. This runs before the file is read
3. File data: ...
```

---

### 🔹 Why Non-Blocking I/O Matters

| Blocking I/O | Non-Blocking I/O |
|-------------|------------------|
| ❌ One request blocks others | ✅ Handle thousands of concurrent requests |
| ❌ Poor scalability | ✅ High throughput |
| ❌ Slow for I/O-heavy apps | ✅ Ideal for APIs, real-time systems |

---

### 🔹 Under the Hood

```
[JS Thread] → [Libuv] → [Thread Pool (4 threads) or OS Kernel]
```

- ✅ **File I/O, Network, Crypto** → offloaded to **thread pool or OS**
- ✅ **Event Loop** → continues processing other events
- ✅ When I/O completes → callback is **queued** and executed

---

### 📌 Interview Answer

> _"Non-blocking I/O means I/O operations don’t block the main thread. Instead, Node.js uses libuv and a thread pool to handle them asynchronously. This allows one Node.js process to handle thousands of concurrent connections — perfect for I/O-heavy apps like APIs and real-time systems."_  

---

## 17️⃣ How does Node.js handle child processes?

> Node.js can **spawn child processes** to run **external commands**, **CPU-heavy tasks**, or **separate Node.js scripts** — avoiding blocking the main thread.

---

### ✅ 4 Ways to Create Child Processes

| Method | Purpose | Sync? |
|-------|--------|------|
| `child_process.spawn()` | Launch long-running process (e.g., `tail -f`) | ❌ No |
| `child_process.exec()` | Run command, buffer output | ✅ Yes (but async callback) |
| `child_process.execFile()` | Run executable directly (faster than `exec`) | ❌ No |
| `child_process.fork()` | Spawn new Node.js process with IPC channel | ❌ No |

---

### ✅ Example: `spawn` for Streaming Logs

```js
const { spawn } = require('child_process');

const tail = spawn('tail', ['-f', '/var/log/app.log']);

tail.stdout.on('data', (data) => {
  console.log('LOG:', data.toString());
});

tail.stderr.on('data', (data) => {
  console.error('ERROR:', data.toString());
});
```

✅ Streams output in real-time — perfect for logs

---

### ✅ Example: `fork` for CPU-Intensive Task

```js
// main.js
const { fork } = require('child_process');

const compute = fork('compute.js');

compute.send({ numbers: [1, 2, 3, 4, 5] });

compute.on('message', (result) => {
  console.log('Result:', result);
  compute.kill();
});

// compute.js
process.on('message', (data) => {
  const sum = data.numbers.reduce((a, b) => a + b * b, 0); // CPU-heavy
  process.send(sum);
});
```

✅ Offloads CPU work — keeps main thread responsive

---

### 📌 Best Practices

| Rule | Why |
|------|-----|
| ✅ Use `spawn` for streaming | Low memory |
| ✅ Use `exec` for small output | Simple |
| ✅ Use `fork` for Node.js scripts | IPC with `send()` and `on('message')` |
| ✅ Handle `error`, `exit` events | Robust error handling |

---

## 18️⃣ What are some ways to debug a Node.js application?

> Debugging Node.js goes beyond `console.log()`.

---

### ✅ 1. Built-in Debugger
```bash
node inspect app.js
```
- Use `c` (continue), `n` (next), `repl` (inspect variables)

---

### ✅ 2. Chrome DevTools
```bash
node --inspect app.js
```
- Open `chrome://inspect` → Click "Open dedicated DevTools"
- Full debugging: breakpoints, call stack, scope

---

### ✅ 3. VS Code Debugger
Add `.vscode/launch.json`:
```json
{
  "type": "node",
  "request": "launch",
  "name": "Launch Program",
  "program": "${workspaceFolder}/index.js"
}
```
✅ Most popular dev setup

---

### ✅ 4. Logging
- ✅ `console.log()` → dev only
- ✅ Use structured logging in prod: `winston`, `pino`
```js
const logger = require('pino')();
logger.info({ user: 'alice' }, 'User logged in');
```

---

### ✅ 5. Error Tracking
- ✅ `try/catch` for sync
- ✅ `next(err)` for Express
- ✅ Tools: **Sentry**, **Datadog**, **New Relic**

---

### ✅ 6. Profiling
```bash
node --prof app.js
node --prof-process isolate-0xnnnnnnnnnnnn-v8.log
```
✅ Analyze performance bottlenecks

---

### 📌 Interview Answer

> _"I use VS Code for debugging with breakpoints. In production, I use pino for structured logging and Sentry for error tracking. For CPU profiling, I use --prof. I avoid console.log in prod — it’s slow and unstructured."_  

---

## 19️⃣ What is the difference between `__dirname` and `.` in Node.js?

> This is a **common source of bugs** — especially in file operations.

---

### ✅ `__dirname`

- ✅ **Absolute path** to the **directory of the current file**
- ✅ Always correct, regardless of how script is run
- ✅ Determined at **compile time**

```js
// /home/user/app/utils/logger.js
console.log(__dirname);
// Output: /home/user/app/utils
```

---

### ✅ `.` (Current Directory)

- ✅ **Relative path** to the **current working directory**
- ✅ Depends on **where you run `node` from**
- ✅ Can change at runtime

```bash
cd /home/user/app
node utils/logger.js
# Here, '.' = /home/user/app

cd /tmp
node /home/user/app/utils/logger.js
# Here, '.' = /tmp → BUG if you use './config.json'
```

---

### ✅ Example: Safe vs Unsafe File Read

```js
// ❌ UNSAFE: breaks if run from different directory
fs.readFile('./config.json', callback);

// ✅ SAFE: always works
fs.readFile(path.join(__dirname, 'config.json'), callback);

// ✅ Or with ES6+ and ESM
import { fileURLToPath } from 'url';
import { dirname } from 'path';
const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
```

---

### 📌 Best Practice

> ✅ **Always use `__dirname` for file paths**  
> ❌ Never rely on `.` in production code

---

## 20️⃣ How do you manage environment variables in Node.js?

> **Environment variables** store **configuration** (API keys, DB URLs, feature flags) — separate from code.

---

### ✅ 1. Using `process.env`

```js
const dbUrl = process.env.DB_URL;
const port = process.env.PORT || 3000;
```

---

### ✅ 2. `.env` File + `dotenv`

Install: `npm install dotenv`

```bash
# .env
DB_URL=mongodb://localhost:27017/app
API_KEY=abc123
NODE_ENV=development
```

```js
require('dotenv').config();

console.log(process.env.DB_URL); // 'mongodb://localhost:27017/app'
```

✅ **Rules**:
- Add `.env` to `.gitignore`
- Create `.env.example` for docs
- Never commit secrets

---

### ✅ 3. Validation

```js
if (!process.env.DB_URL) {
  throw new Error('DB_URL is required');
}
```

Or use `joi`:
```js
const schema = Joi.object({
  DB_URL: Joi.string().uri().required(),
  API_KEY: Joi.string().required()
});
```

---

### ✅ 4. Environment-Specific Files

```bash
.env          # Default
.env.local    # Local overrides (gitignored)
.env.development
.env.production
```

`dotenv` loads them automatically based on `NODE_ENV`.

---

### ✅ 5. In Production

| Platform | How |
|--------|-----|
| ✅ **Docker** | `ENV DB_URL=...` in Dockerfile or `docker run -e DB_URL=...` |
| ✅ **Kubernetes** | `env:` in Pod spec or ConfigMap |
| ✅ **AWS** | Environment variables in Lambda, ECS |
| ✅ **Heroku** | `heroku config:set DB_URL=...` |

---

### 📌 Best Practices

| Rule | Why |
|------|-----|
| ✅ Use `dotenv` in dev | Easy setup |
| ✅ Validate required vars | Fail fast |
| ✅ Never commit `.env` | Security |
| ✅ Use upper case names | Convention |
| ✅ Use `NODE_ENV` | Control behavior (dev/prod) |

---

### 📌 Interview Answer

> _"I use environment variables for config like DB_URL and API_KEY. In dev, I use dotenv with .env files. In prod, I set them via Docker or cloud platform. I validate required variables at startup and never commit .env to git. I use __dirname for file paths to avoid relative path bugs."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use non-blocking I/O for high concurrency, child processes for CPU tasks, __dirname for safe paths, and dotenv for environment management."_  

That shows **deep, practical Node.js knowledge**.

---

