# 🧠 Node.js Deep Dive (Part 2)  
**Streams, Express Middleware, Error Handling & package.json**

This document covers **advanced Node.js concepts** — essential for **backend development**, **performance optimization**, and **system design interviews**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 6️⃣ What are streams in Node.js? What are the different types?

> **Streams** are **objects that let you read data from a source or write data to a destination **(like reading a file, HTTP requests, or network sockets).

They process data **in chunks**, not all at once — making them **memory-efficient** and **fast**.

---

### ✅ Why Use Streams?

| Without Streams | With Streams |
|----------------|-------------|
| `fs.readFile()` → loads entire file into memory | `fs.createReadStream()` → processes in chunks |
| ❌ High memory usage | ✅ Low memory usage |
| ❌ Slow for large files | ✅ Fast, scalable |

---

### ✅ 4 Types of Streams

| Type | Purpose | Example |
|------|--------|--------|
| ✅ **Readable** | Source of data | `fs.createReadStream()` |
| ✅ **Writable** | Destination for data | `fs.createWriteStream()` |
| ✅ **Duplex** | Both readable and writable | TCP socket, `zlib` |
| ✅ **Transform** | Modify data as it passes through | `zlib.createGzip()` |

---

### ✅ Example: Read & Compress a Large File
```js
const fs = require('fs');
const zlib = require('zlib');

// Read → Compress → Write (all in chunks)
const readStream = fs.createReadStream('huge-file.txt');
const writeStream = fs.createWriteStream('huge-file.txt.gz');
const gzip = zlib.createGzip();

readStream
  .pipe(gzip)        // Transform: compress
  .pipe(writeStream); // Write compressed data

console.log('Compression started...');
```

✅ Processes file in **small chunks** — uses minimal memory  
✅ Can handle **gigabyte-sized files**

---

## 7️⃣ When would you use a stream instead of reading a file entirely into memory?

> Use **streams** when dealing with **large data** to **avoid memory overflow**.

---

### ✅ Use Cases for Streams

| Scenario | Why Streams? |
|--------|-------------|
| ✅ **Large Files** (logs, videos, backups) | Avoid `JavaScript heap out of memory` |
| ✅ **Real-Time Processing** (video, audio) | Process as data arrives |
| ✅ **File Uploads/Downloads** | Pipe directly to response |
| ✅ **Data Pipelines** | Transform data (e.g., CSV → JSON → DB) |
| ✅ **Log Processing** | Read and analyze logs line-by-line |

---

### ❌ When Not to Use Streams
| Scenario | Use Sync/Async |
|--------|--------------|
| ❌ Small config files | `fs.readFileSync('config.json')` |
| ❌ Simple data manipulation | `JSON.parse(fs.readFileSync(...))` |

---

### ✅ Example: Process a 10GB Log File
```js
const fs = require('fs');
const readline = require('readline');

const rl = readline.createInterface({
  input: fs.createReadStream('server.log'),
  crlfDelay: Infinity
});

let errorCount = 0;

rl.on('line', (line) => {
  if (line.includes('ERROR')) {
    errorCount++;
  }
});

rl.on('close', () => {
  console.log(`Found ${errorCount} errors`);
});
```

✅ Uses **constant memory** regardless of file size

---

## 8️⃣ What is middleware in Express.js? How does it work?

> **Middleware** is a **function that has access to the request (`req`), response (`res`), and the next middleware function (`next`)**.

It sits between the **incoming request** and the **final route handler**.

---

### ✅ How Middleware Works

```
[Request] → [Middleware 1] → [Middleware 2] → [Route Handler] → [Response]
```

Each middleware can:
- ✅ Modify `req` or `res`
- ✅ End the response (e.g., send 401)
- ✅ Call `next()` to pass control to the next function

---

### ✅ Example: Logging Middleware
```js
function logger(req, res, next) {
  console.log(`${new Date().toISOString()} ${req.method} ${req.path}`);
  next(); // Pass to next middleware
}

app.use(logger);
```

---

### ✅ Common Middleware Examples

| Middleware | Purpose |
|----------|--------|
| `express.json()` | Parse JSON body |
| `cors()` | Enable CORS |
| `morgan('dev')` | HTTP request logging |
| `authMiddleware` | Check JWT token |
| `errorHandler` | Handle errors globally |

---

### ✅ Custom Auth Middleware
```js
function authMiddleware(req, res, next) {
  const token = req.headers['authorization'];
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  
  // Verify token
  if (isValidToken(token)) {
    req.user = decodeToken(token); // Attach to req
    next(); // Proceed
  } else {
    res.status(403).json({ error: 'Invalid token' });
  }
}

app.get('/profile', authMiddleware, (req, res) => {
  res.json({ user: req.user });
});
```

---

## 9️⃣ How do you handle errors in Express (synchronous and asynchronous)?

> Express has **two types of errors** — and they must be handled differently.

---

### ✅ 1. Synchronous Errors
Thrown in route handler or middleware.

```js
app.get('/error-sync', (req, res) => {
  throw new Error('Sync error!');
});
```

❌ Crashes app if not caught.

✅ **Fix: Use `try/catch` or let Express catch it**
```js
// Express automatically catches sync errors
// But better to use error-handling middleware
```

---

### ✅ 2. Asynchronous Errors (Callbacks, Promises)
Not automatically caught.

```js
app.get('/error-async', (req, res, next) => {
  fs.readFile('nonexistent.txt', (err, data) => {
    if (err) {
      return next(err); // Pass to error handler
    }
    res.send(data);
  });
});
```

✅ Use `next(err)` to trigger error middleware

---

### ✅ Async/Await Errors
```js
app.get('/error-async-await', async (req, res, next) => {
  try {
    const data = await fs.promises.readFile('missing.txt');
    res.send(data);
  } catch (err) {
    next(err); // Pass to error handler
  }
});
```

---

### ✅ Global Error Handling Middleware
```js
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ 
    error: 'Something broke!',
    message: err.message 
  });
});
```

✅ Must have **4 parameters**: `(err, req, res, next)`  
✅ Place **last** in middleware chain

---

## 🔟 What is the purpose of `package.json`?

> `package.json` is the **manifest file** for a Node.js project.

It contains **metadata**, **dependencies**, **scripts**, and **configuration**.

---

### ✅ Key Fields in `package.json`

| Field | Purpose |
|------|--------|
| `name` | Project name |
| `version` | Semantic version (e.g., `1.0.0`) |
| `description` | Brief description |
| `main` | Entry point (e.g., `index.js`) |
| `scripts` | npm scripts (`start`, `test`, `build`) |
| `dependencies` | Production dependencies |
| `devDependencies` | Dev-only tools (testing, linting) |
| `engines` | Node.js version requirement |
| `keywords` | For npm search |
| `author` | Creator info |

---

### ✅ Example `package.json`
```json
{
  "name": "my-api",
  "version": "1.0.0",
  "description": "A simple REST API",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.0",
    "mongoose": "^7.0.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.0",
    "jest": "^29.0.0"
  },
  "engines": {
    "node": ">=18.0.0"
  }
}
```

---

### ✅ Why `package.json` Matters

| Use Case | How |
|--------|-----|
| ✅ **Install Dependencies** | `npm install` reads `dependencies` |
| ✅ **Run Scripts** | `npm start` → runs `"start"` script |
| ✅ **Version Control** | `package-lock.json` ensures consistent installs |
| ✅ **Publish to npm** | Required for public packages |
| ✅ **CI/CD** | Used in build pipelines |

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use streams to process large files without memory issues, Express middleware for logging and auth, and global error handling to catch async errors. All configured in package.json."_  

That shows **deep, integrated Node.js knowledge**.

---

