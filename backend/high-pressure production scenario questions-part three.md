
We’ll use the **Triage → Isolate → Fix → Verify** framework for each.

---

## 🔥 7.  
> **Users report they’re being logged out every 30 seconds. Session data is stored in Redis. What do you check?**

### ✅ Strong Answer:

> "Session expiration every 30 seconds is way too fast. Since sessions are in Redis, I’ll check **Redis TTLs, app config, and session propagation**."

---

### 🚨 Step 1: Triage & Stabilize (0–10 mins)
- Check **user reports**: all users or just some? (browser, region, tenant?)
- Confirm: are they **actually logged out** or just seeing auth prompts?
- Check **session cookie** in browser:
  - Is `Expires` set to 30s?
  - Is `Secure`, `HttpOnly` correct?

---

### 🔍 Step 2: Isolate (10–25 mins)
Likely causes:
1. **Redis TTL misconfigured** → session expires in 30s
2. **App sets short `maxInactiveInterval`**
3. **Clock drift** between app server and Redis
4. **Session not being written** (e.g., due to serialization error)
5. **Redis eviction policy** (`volatile-lru`) removing sessions
6. **Multiple app instances not sharing session store**

Check:
- `TTL session:abc123` in Redis → is it 30s?
- App config: `server.servlet.session.timeout=30m` (not 30s!)
- Redis `INFO memory` → high eviction rate?
- Logs: any `Could not serialize session`?

---

### 🛠️ Step 3: Fix (25–40 mins)
- ✅ Fix Redis TTL: ensure session keys use correct expiration
- Or fix app config: `spring.session.timeout=30m`
- If eviction: increase Redis memory or use `allkeys-lru`
- If clock drift: sync NTP on servers
- Ensure **all instances use same Redis**

---

### ✅ Step 4: Verify (40–45 mins)
- Log in → stay logged in for 5 mins
- Check Redis: `TTL` = 30+ mins
- Add **alert** if session TTL < 5 mins

> "Most likely: a **config typo** — `30` seconds instead of `30m`. Always validate configs in staging."

---

---

## 🔥 8.  
> **Your cron job that runs every 5 minutes is now taking 6 minutes. It’s starting to pile up. How do you fix it without breaking data consistency?**

### ✅ Strong Answer:

> "A job taking longer than its interval will **overlap**, risking duplicate work. I’ll **prevent concurrency** and **optimize performance**."

---

### 🚨 Step 1: Triage & Stabilize (0–10 mins)
- **Stop the cron temporarily** → prevent pile-up
- Check **job logs**: any errors or slow queries?
- Check **metrics**: DB latency, CPU, memory

---

### 🔍 Step 2: Isolate (10–25 mins)
Likely causes:
1. **Data growth** → more rows to process
2. **Slow query** (missing index, full scan)
3. **External API slowdown**
4. **No idempotency** → can’t run in parallel
5. **No locking** → multiple instances running

Check:
- Job log: how many records processed? time per batch?
- DB query plan: is it using an index?
- External service latency

---

### 🛠️ Step 3: Fix (25–40 mins)
- ✅ Add **distributed lock** (Redis, database):
  ```java
  if (lock.tryLock()) {
      try { runJob(); }
      finally { lock.unlock(); }
  }
  ```
- Or use **idempotent processing** (mark records as `processing`)
- Optimize:
  - Add index on `status = 'PENDING'`
  - Process in batches
  - Cache external calls
- Change schedule: run every 10 mins if needed

---

### ✅ Step 4: Verify (40–45 mins)
- Restart job → completes in < 4 mins
- No overlapping runs
- Data consistency intact
- Add **monitoring**: job duration, lock contention

> "Key: **idempotency + locking**. Never assume jobs are isolated."

---

---

## 🔥 9.  
> **After a database failover, your app returns stale data for 10 minutes. Replication lag is 0. Why?**

### ✅ Strong Answer:

> "Replication lag is 0, but app sees stale data? This points to **connection pooling or caching** — not the DB."

---

### 🚨 Step 1: Triage & Stabilize (0–10 mins)
- Confirm: **which queries** return stale data?
- Is it **all queries** or only reads?
- Check: are we reading from a **replica**?

---

### 🔍 Step 2: Isolate (10–25 mins)
Likely causes:
1. **Connection pool still pointing to old primary** (now read-only)
2. **DNS cache** in app not refreshed
3. **Application-level cache** (e.g., Caffeine, Redis) with TTL
4. **Read-from-replica logic** not updated after failover
5. **Hibernate 2nd-level cache** not invalidated

Check:
- `SELECT pg_is_in_recovery();` → is it read-only?
- Connection pool: are connections going to old IP?
- Cache: `GET user:123` → old value?

---

### 🛠️ Step 3: Fix (25–40 mins)
- ✅ **Restart app** → forces new connections
- Or: **clear connection pool** (Hikari: `evictAll()` via JMX)
- Or: **invalidate cache** (Redis `FLUSHDB`, Caffeine `invalidateAll`)
- Fix root cause:
  - Use **dynamic DNS or service discovery**
  - Use **connection pool with failover detection**
  - Invalidate cache on failover (via event)

---

### ✅ Step 4: Verify (40–45 mins)
- Query returns fresh data
- Connection pool points to new primary
- Add **health check** that fails if in recovery mode

> "Most likely: **connection pool stuck on old primary**. Fix: restart or evict. Prevent: use cloud-native connection managers."

---

---

## 🔥 10.  
> **You deploy a change that adds a new column to a 500M-row table. The migration has been running for 6 hours. What do you do?**

### ✅ Strong Answer:

> "A 6-hour blocking migration on a 500M-row table is dangerous. I’ll **assess risk, stop if needed, and switch to online migration**."

---

### 🚨 Step 1: Triage & Stabilize (0–10 mins)
- Check: is the migration **blocking DML**? (e.g., `ALTER TABLE ... ADD COLUMN`)
- Check DB load: CPU, I/O, replication lag
- Can I **safely cancel** without corruption?

---

### 🔍 Step 2: Isolate (10–25 mins)
- If `ALTER TABLE` → it’s **copying the entire table** → slow
- Risk: replication lag, downtime, failover issues
- Check progress:
  ```sql
  -- PostgreSQL
  SELECT * FROM pg_stat_progress_create_index;
  ```

---

### 🛠️ Step 3: Fix (25–40 mins)
#### Option A: **Let it finish** (if safe)
- Monitor closely
- Ensure replication can catch up

#### Option B: **Cancel and use online migration**
- **PostgreSQL**:
  ```sql
  CREATE COLUMN ... ADD COLUMN IF NOT EXISTS new_col;
  -- Or use `pg_repack`
  ```
- **MySQL**:
  ```bash
  pt-online-schema-change --alter "ADD COLUMN new_col INT" D=db,t=table
  ```
- **General**:
  1. Add column as nullable
  2. Backfill in batches
  3. Add index/constraint later

---

### ✅ Step 4: Verify (40–45 mins)
- Migration complete
- App works with new column
- Replication lag < 1s
- Add **process**: always use online tools for large tables

> "Never run blocking DDL on large tables. Use `pt-online-schema-change`, `gh-ost`, or `CREATE INDEX CONCURRENTLY`."

---

## ✅ Summary: What You’ve Shown

| Scenario | Key Skill |
|--------|----------|
| **Session timeout** | Redis, config, TTL debugging |
| **Cron job pile-up** | Idempotency, locking, performance |
| **Stale data after failover** | Connection pooling, caching, failover awareness |
| **Long-running migration** | DB operations, online schema changes |

---

## 💡 Pro Insight

> In production, **time is your enemy** — but **rushing is worse**.  
> The best engineers **assess, then act**.

You’ve now shown **deep operational maturity**.

---
