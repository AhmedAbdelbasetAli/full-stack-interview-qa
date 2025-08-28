Absolutely! Let’s go through **each of these real-world production fire scenarios** one by one — with **clear, structured, senior-engineer-level answers** that show **how you think, act, and communicate under pressure**.

We’ll use the **Triage → Isolate → Fix → Verify** framework for each.

---

## 🔥 1.  
> **Your login API suddenly returns 500s for 30% of users. No code was deployed today. The database CPU is at 98%. What do you do?**

### ✅ Strong Answer:

> "Even though no code was deployed, something changed — data, config, or load. My priority is **restoring login success**, not finding the root cause immediately."

---

### 🚨 Step 1: Triage & Stabilize (0–10 mins)
- Check **APM dashboard** (Datadog, New Relic): error rate, latency, DB CPU, connection pool
- Check **logs**: any spikes in slow queries or exceptions?
- **Scale read replicas** (if login uses read-only DB)
- **Enable circuit breaker** on login → fail fast, not hang
- **Prepare rollback** of any config/feature flag changes

---

### 🔍 Step 2: Isolate (10–25 mins)
Likely causes:
1. **Missing index** on `users(email)` or `users(username)` → full table scan
2. **N+1 queries** (e.g., loading roles one by one)
3. **Connection pool exhaustion** → threads waiting
4. **Hot row** (e.g., global login counter)
5. **Bad data** (e.g., a user with 10K roles)

Check:
- `EXPLAIN` the login query
- HikariCP metrics: `activeConnections`, `waitingThreads`
- Slow query log

---

### 🛠️ Step 3: Fix (25–40 mins)
- ✅ **Add missing index**:
  ```sql
  CREATE INDEX CONCURRENTLY idx_users_email ON users(email);
  ```
- Or **optimize N+1** with `JOIN FETCH`
- Or **increase pool size** temporarily
- If unsure → **rollback config changes**

---

### ✅ Step 4: Verify (40–45 mins)
- Confirm 500s dropped
- DB CPU < 70%
- Latency < 500ms
- Schedule post-mortem

> "Most likely: a **missing index** due to data growth. I’d add it, then fix the root cause: better query monitoring and index reviews."

---

---

## 🔥 2.  
> **After a new release, checkout success rate drops from 99% to 60%. The payment service shows no errors. You have 1 hour before Black Friday sales begin. Go.**

### ✅ Strong Answer:

> "This is a **time-critical incident**. My goal is to **restore checkout success** before Black Friday. I’ll act fast, starting with rollback."

---

### 🚨 Step 1: Triage & Stabilize (0–10 mins)
- **Roll back the release immediately** → fastest way to restore service
- While rollback deploys, investigate
- **Enable debug logging** for checkout flow
- **Check feature flags** — maybe a bad flag is enabled

---

### 🔍 Step 2: Isolate (10–25 mins)
Since **payment service shows no errors**, the issue is likely:
1. **Frontend not handling response correctly** (e.g., new JSON structure)
2. **Timeout in order creation** after payment success
3. **Idempotency key mismatch** → duplicate payment rejected
4. **Caching issue** → stale cart data
5. **New validation rule** blocking valid orders

Check:
- **Browser console logs** (400s on `/confirm`)
- **API logs** between frontend and order service
- **Network traces** (e.g., Charles, Fiddler)
- **Compare payloads** (old vs new)

---

### 🛠️ Step 3: Fix (25–40 mins)
- Rollback already restoring service
- But root cause: say, **frontend expects `orderId`, but backend returns `id`**
- Fix: update frontend to handle new field
- Or add backward compatibility

---

### ✅ Step 4: Verify (40–45 mins)
- Confirm checkout success > 99%
- Test end-to-end
- Deploy fix with **feature flag** for safety

> "Lesson: Always test **integration points** in staging with real payloads. Use contract testing (e.g., Pact)."

---

---

## 🔥 3.  
> **Your app works fine locally and in staging. In production, /api/users returns empty JSON. No errors in logs. What’s wrong?**

### ✅ Strong Answer:

> "No errors in logs means the app is working — but returning empty data. This is likely a **configuration or environment difference**."

---

### 🚨 Step 1: Triage & Stabilize (0–10 mins)
- Check: Is the endpoint **actually returning `[]` or `null`**?
- Is it **secured**? Maybe 403 but frontend swallows it
- Check **network tab** — status code, response body
- Check **authentication** — are you hitting prod with dev credentials?

---

### 🔍 Step 2: Isolate (10–25 mins)
Likely causes:
1. **Wrong database** — connected to empty DB
2. **Filtering by tenant ID** — but tenant not set in prod
3. **Feature flag disabled** in prod
4. **Caching** — cached empty result
5. **Timezone or date filter** — e.g., `created_at > NOW()` but server time off
6. **Case-sensitive query** — e.g., `WHERE status = 'ACTIVE'` but data is `'active'`

Check:
- **`application-prod.yml`** vs `application-staging.yml`
- **DB connection string** — is it pointing to the right DB?
- **Run a raw query** in prod DB: `SELECT COUNT(*) FROM users;`
- **Enable debug logs** for the repository

---

### 🛠️ Step 3: Fix (25–40 mins)
- Found: `spring.datasource.url` points to **empty DB** due to typo
- Fix: correct URL in config server or Kubernetes secret
- Restart app

Or:
- Found: `WHERE tenant_id = :current_tenant` but `current_tenant` is `null` in prod
- Fix: set tenant from JWT or header

---

### ✅ Step 4: Verify (40–45 mins)
- Call `/api/users` → returns real data
- Check logs: no errors
- Update **deployment checklist** to verify DB URLs

> "Most likely: a **misconfigured environment variable**. Always validate configs in pre-prod."

---

## ✅ Summary: What You’ve Shown

| Scenario | Key Skill Demonstrated |
|--------|------------------------|
| **Login 500s + DB CPU** | DB performance, indexing, triage |
| **Checkout drop on Black Friday** | Rollback urgency, integration testing |
| **Empty JSON in prod** | Configuration debugging, attention to detail |

---

## 💡 Pro Insight

> In production, **the app works — the environment doesn’t**.  
> Always ask:  
> - "What’s different between environments?"  
> - "What changed — even if it wasn’t code?"

---

