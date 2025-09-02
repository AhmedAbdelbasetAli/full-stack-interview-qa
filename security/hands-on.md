# 🛠️ Hands-On: Fix a Vulnerable Web App  
**From Insecure to Secure – Step-by-Step Lab**

Welcome to your **interactive security lab**!  
You’ll take a **deliberately vulnerable web app** and **fix real-world security flaws**.

We’ll use:
- ✅ A simple **Node.js + Express + EJS** app
- ✅ Real vulnerabilities (XSS, SQLi, IDOR, weak auth)
- ✅ Step-by-step fixes
- ✅ Secure coding best practices

---

## 🧪 The Vulnerable App: `SecureBank Lite`

A minimal banking app with:
- User registration & login
- View profile
- Transfer money
- Admin panel (insecure)

👉 **GitHub Repo**:  
[https://github.com/secure-coding-lab/vulnerable-bank-app](https://github.com/secure-coding-lab/vulnerable-bank-app) (simulated)

---

## 🔻 Step 1: Clone & Run the App

```bash
git clone https://github.com/secure-coding-lab/vulnerable-bank-app.git
cd vulnerable-bank-app
npm install
npm start
```

🌐 Visit: `http://localhost:3000`

You’ll see:
- Login / Register
- After login: Profile, Transfer, Admin (if admin)

---

## 🔥 Vulnerability 1: **Stored XSS in User Bio**

> Users can set a bio — attacker injects JavaScript.

### 🔍 Find the Bug
1. Register a user
2. Go to **Profile → Edit Bio**
3. Enter:
   ```html
   <script>alert('XSS!');</script>
   ```
4. Save → Refresh → Alert pops!

✅ **Vulnerable**: No input sanitization, unsafe output.

---

### ✅ Fix: Sanitize Input & Escape Output

#### 1. Install `DOMPurify` (Sanitizer)
```bash
npm install dompurify
```

#### 2. Sanitize on Save (`routes/profile.js`)
```js
const createDOMPurify = require('dompurify');
const { JSDOM } = require('jsdom');
const window = new JSDOM('').window;
const DOMPurify = createDOMPurify(window);

// When saving bio
app.post('/profile', (req, res) => {
  const cleanBio = DOMPurify.sanitize(req.body.bio);
  db.updateUserBio(req.user.id, cleanBio); // Save clean version
  res.redirect('/profile');
});
```

#### 3. Escape on Render (`views/profile.ejs`)
```ejs
<!-- ❌ Bad -->
<div><%= user.bio %></div>

<!-- ✅ Good -->
<div><%- user.bio %></div>  <!-- EJS: unescaped but already sanitized -->
```

✅ Now `<script>` tags are stripped.

---

## 🔥 Vulnerability 2: **SQL Injection in Login**

> Login form vulnerable to `' OR '1'='1`

### 🔍 Find the Bug
In `routes/auth.js`:
```js
const query = `SELECT * FROM users WHERE email = '${email}' AND password = '${password}'`;
db.raw(query).then(...);
```

❌ String concatenation → SQLi

Try logging in with:
- **Email**: `admin@bank.com' --`
- **Password**: anything

✅ Logs in as admin!

---

### ✅ Fix: Use Parameterized Queries

```js
// ✅ Safe
const query = 'SELECT * FROM users WHERE email = ? AND password = ?';
db.raw(query, [email, password]).then(...);
```

Or better: **Use bcrypt + proper auth**

```js
// Hash password on register
const hashed = await bcrypt.hash(password, 10);

// On login
const user = await db.select('*').from('users').where({ email }).first();
if (user && await bcrypt.compare(password, user.password)) {
  // Login success
}
```

✅ No SQLi, secure password storage.

---

## 🔥 Vulnerability 3: **IDOR – Access Any User’s Profile**

> Change URL from `/profile/1` to `/profile/2` → see others’ data.

### 🔍 Find the Bug
In `routes/profile.js`:
```js
app.get('/profile/:id', (req, res) => {
  const user = db.getUserById(req.params.id);
  res.render('profile', { user });
});
```

❌ No ownership check.

---

### ✅ Fix: Add Access Control

```js
app.get('/profile/:id', (req, res) => {
  const requestedId = parseInt(req.params.id);
  
  // Check if user is accessing their own profile
  if (requestedId !== req.user.id && !req.user.isAdmin) {
    return res.status(403).send('Access Denied');
  }

  const user = db.getUserById(requestedId);
  res.render('profile', { user });
});
```

✅ Now users can only view their own profile (unless admin).

---

## 🔥 Vulnerability 4: **Insecure Admin Panel**

> Admin panel at `/admin` — but no role check.

### 🔍 Find the Bug
```js
app.get('/admin', (req, res) => {
  const users = db.getAllUsers();
  res.render('admin', { users });
});
```

❌ No `isAdmin` check.

Go to `/admin` → see all users!

---

### ✅ Fix: Add Role-Based Access

```js
function requireAdmin(req, res, next) {
  if (!req.user || !req.user.isAdmin) {
    return res.status(403).send('Admins only');
  }
  next();
}

app.get('/admin', requireAdmin, (req, res) => {
  const users = db.getAllUsers();
  res.render('admin', { users });
});
```

✅ Only admins can access.

---

## 🔥 Vulnerability 5: **Weak Session Management**

> Session cookie is not secure.

### 🔍 Find the Bug
In `app.js`:
```js
app.use(session({
  secret: 'weak-secret',  // ❌ Hardcoded, weak
  resave: false,
  saveUninitialized: false
}));
```

❌ No `secure`, `httpOnly`, or `sameSite`

---

### ✅ Fix: Secure Session Configuration

```js
app.use(session({
  secret: process.env.SESSION_SECRET || 'strong-random-secret-here',
  resave: false,
  saveUninitialized: false,
  cookie: {
    httpOnly: true,      // ✅ JS can't read
    secure: true,        // ✅ HTTPS only (in production)
    sameSite: 'lax',     // ✅ Prevents CSRF
    maxAge: 30 * 60 * 1000 // 30 minutes
  }
}));
```

✅ Set `secure: true` only in production (HTTPS)

---

## 🔥 Vulnerability 6: **No Input Validation**

> Can set negative balance, invalid emails.

### 🔍 Find the Bug
In transfer form:
```js
app.post('/transfer', (req, res) => {
  const { to, amount } = req.body;
  db.transfer(req.user.id, to, amount); // No validation!
});
```

Try transferring `-1000` → balance goes up!

---

### ✅ Fix: Add Server-Side Validation

```js
app.post('/transfer', (req, res) => {
  const { to, amount } = req.body;

  // Validate
  if (!to || isNaN(amount) || amount <= 0) {
    return res.status(400).send('Invalid input');
  }

  if (amount > 10000) {
    return res.status(400).send('Transfer limit exceeded');
  }

  db.transfer(req.user.id, to, amount);
  res.redirect('/profile');
});
```

✅ Fail fast, clear error messages.

---

## 🔐 Bonus: Add Security Headers

In `app.js`:
```js
app.use((req, res, next) => {
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Content-Security-Policy', 
    "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'"
  );
  next();
});
```

✅ Blocks MIME sniffing, clickjacking, inline scripts.

---

## ✅ Final Secure Checklist

| Fix | Status |
|-----|--------|
| ✅ Stored XSS fixed with DOMPurify | ✔️ |
| ✅ SQL Injection fixed with parameterized queries | ✔️ |
| ✅ IDOR fixed with ownership check | ✔️ |
| ✅ Admin panel protected with role check | ✔️ |
| ✅ Sessions secured with HttpOnly, SameSite | ✔️ |
| ✅ Input validation added | ✔️ |
| ✅ Passwords hashed with bcrypt | ✔️ |
| ✅ Security headers added | ✔️ |

---

## 📌 What You’ve Learned

You’ve just:
- 🔍 Found real vulnerabilities
- 💉 Exploited them (in a safe lab)
- 🛠️ Fixed them with industry best practices
- 🛡️ Added defense-in-depth (CSP, headers, validation)

This is **exactly what real security engineers do**.

---

## 🎯 Interview Answer

> _"I fixed a vulnerable Node.js banking app. I prevented XSS with DOMPurify, SQLi with parameterized queries, IDOR with access checks, and secured sessions with HttpOnly and SameSite cookies. I also added input validation and security headers. I never trust the client — all security happens on the server."_  

---
