Absolutely! These are **classic system design and database modeling questions** that test your **creativity, scalability thinking, and real-world data handling skills**.

Let’s answer each like a **senior software engineer or architect**, using **What → How → Why + Real Project Examples**.

---

### 🔹 Question 31:  
> **How would you design a URL shortener (like bit.ly) from a database perspective?**

#### ✅ Strong Answer:

---

### 🎯 Requirements
- Convert long URL → short key (e.g., `bit.ly/abc123`)
- Redirect in < 100ms
- Scale to billions of URLs
- Support analytics (clicks, referrers)
- Optional: TTL, custom keys, user ownership

---

### ✅ Database Schema (PostgreSQL)

```sql
CREATE TABLE urls (
    id BIGSERIAL PRIMARY KEY,          -- Auto-increment ID
    long_url TEXT NOT NULL,            -- Original URL
    short_key CHAR(7) UNIQUE NOT NULL, -- e.g., 'abc123'
    created_at TIMESTAMPTZ DEFAULT NOW(),
    expires_at TIMESTAMPTZ,            -- Optional TTL
    user_id BIGINT,                    -- Optional owner
    click_count BIGINT DEFAULT 0
);

-- Index for fast lookup
CREATE INDEX idx_short_key ON urls(short_key);

-- For analytics
CREATE TABLE click_events (
    id BIGSERIAL,
    url_id BIGINT,
    clicked_at TIMESTAMPTZ DEFAULT NOW(),
    ip_address INET,
    user_agent TEXT,
    referrer TEXT
);
```

---

### ✅ Key Design Decisions

#### 1. **ID Generation**
- Use `BIGSERIAL` → auto-increment
- Convert `id` to **base-62** (a-z, A-Z, 0-9) → `abc123`
- Why not random? Avoids collisions

```java
String shortKey = Base62.encode(id); // 123456 → "abc123"
```

#### 2. **TTL (Time-to-Live)**
- Add `expires_at` → check on redirect
- Use cron job to archive old URLs

#### 3. **Scalability**
- **Shard by `id` % N** for horizontal scale
- **Cache hot keys** in Redis: `GET short:abc123` → long URL

#### 4. **Analytics**
- Async write to `click_events` (Kafka or batch)
- Don’t block redirect

---

### ✅ Real Project Example:

We built an internal **link tracker**:
- Used **PostgreSQL + Redis cache**
- Base-62 encoding for short keys
- Clicks logged via **Kafka** → analytics DB
- Redirect: **15ms p99**

Also supported **custom keys** (e.g., `company.com/campaign`)

---

### 💡 Pro Insight:
> The database is just one part — **caching and async logging** make it fast.

---

---

### 🔹 Question 32:  
> **What is the difference between synchronous and asynchronous replication?**

#### ✅ Strong Answer:

This is a **CAP Theorem trade-off**: **Consistency vs Availability**

---

### ✅ Synchronous (Sync) Replication

- **Wait** for replica to acknowledge write **before confirming success**
- Ensures **strong consistency**
- Higher latency

```text
Client → Primary → Replica (wait) → ACK to client
```

✅ Pros:
- No data loss if primary fails
- Strong consistency

❌ Cons:
- Slower (latency = network + replica write)
- If replica down → primary blocks

---

### ✅ Asynchronous (Async) Replication

- **Don’t wait** — confirm success immediately
- Replica gets changes later
- Risk of **data loss**

```text
Client → Primary (ACK) → Replica (eventually)
```

✅ Pros:
- Fast
- High availability

❌ Cons:
- If primary fails before replica gets data → **data loss**
- Replica may be stale

---

### ✅ When to Use Which?

| Use Case | Replication Type |
|--------|------------------|
| **Financial systems** | ✅ Synchronous (no data loss) |
| **Read replicas for analytics** | ✅ Asynchronous (stale OK) |
| **Global apps** | ✅ Async (sync latency too high) |
| **HA with low RPO** | ✅ Sync within region, Async across regions |

---

### ✅ Real Project Example:

We used:
- **Synchronous** replication within **same AWS region** (PostgreSQL streaming)
- **Asynchronous** to **DR region** (100ms lag)
- During outage: DR promoted → lost 2s of data (acceptable per RPO)

Also used **semi-synchronous** (MySQL) — wait for at least one replica.

---

### 💡 Pro Insight:
> **Synchronous** = "I promise it’s safe"  
> **Asynchronous** = "I’ll try my best"

Choose based on **RPO (Recovery Point Objective)**.

---

---

### 🔹 Question 33:  
> **How do you implement soft-schema or schema-less design in a relational database?**

#### ✅ Strong Answer:

You can have **flexible schemas** even in SQL databases.

---

### ✅ Option 1: **JSONB (PostgreSQL)**

Best for **semi-structured data**.

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name TEXT,
    metadata JSONB  -- { "color": "red", "size": "M", "tags": ["sale", "new"] }
);

-- Index
CREATE INDEX idx_metadata ON products USING GIN(metadata);

-- Query
SELECT * FROM products WHERE metadata @> '{"color": "red"}';
```

✅ Use for:
- Dynamic product attributes
- User preferences
- Configurations

---

### ✅ Option 2: **EAV (Entity-Attribute-Value)**

```sql
CREATE TABLE product_attributes (
    product_id INT,
    attribute_name VARCHAR(50),  -- 'color', 'weight'
    attribute_value TEXT
);
```

⚠️ Use with **extreme caution**:
- Hard to query
- No type safety
- Poor performance

✅ Only for **highly dynamic** cases (e.g., medical records)

---

### ✅ Option 3: **Separate Table per Tenant (Multi-tenancy)**

```sql
-- tenant_acme_products, tenant_xyz_products
```
- Schema can evolve per tenant
- But complex to manage

---

### ✅ Real Project Example:

We used **JSONB** for a **product catalog**:
- Each product had different attributes
- Used `metadata` to store `{"material": "cotton", "fit": "slim"}`
- Indexed with `GIN` for fast filtering
- Avoided EAV hell

Also used **schema versioning** for API compatibility.

---

### 💡 Pro Insight:
> Use **JSONB** when you need flexibility — but **keep core data in columns**.

---

---

### 🔹 Question 34:  
> **What is a self-join? Give a real-world example.**

#### ✅ Strong Answer:

---

### 🧠 What Is a Self-Join?

A **self-join** is when a table is joined **to itself** — used when the table has a **hierarchical or recursive relationship**.

---

### ✅ Real-World Example: Employee → Manager

```sql
-- Table: employees
| id | name    | manager_id |
|----|---------|------------|
| 1  | Alice   | NULL       |
| 2  | Bob     | 1          |
| 3  | Carol   | 1          |
| 4  | Dave    | 2          |
```

Find **each employee and their manager’s name**:

```sql
SELECT 
    e.name AS employee,
    m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

Result:
```
employee | manager
---------|--------
Bob      | Alice
Carol    | Alice
Dave     | Bob
Alice    | NULL
```

---

### ✅ Other Examples

| Use Case | How |
|--------|-----|
| **Category tree** | `categories(id, name, parent_id)` |
| **Threaded comments** | `comments(id, content, parent_id)` |
| **Bill of Materials** | `parts(id, name, parent_part_id)` |

---

### 💡 Pro Tip:
> Use **recursive CTEs** for deep hierarchies (e.g., org chart):

```sql
WITH RECURSIVE org AS (
    SELECT id, name, manager_id, 1 AS level
    FROM employees WHERE manager_id IS NULL
    UNION ALL
    SELECT e.id, e.name, e.manager_id, o.level + 1
    FROM employees e JOIN org o ON e.manager_id = o.id
)
SELECT * FROM org;
```

---

### 💡 Pro Insight:
> Self-joins turn **one table into two roles** — powerful for **hierarchical data**.

---

---

### 🔹 Question 35:  
> **How do you handle timezone-aware timestamps in a global application?**

#### ✅ Strong Answer:

This is **critical** for global apps — get it wrong, and your data is **garbage**.

---

### ✅ Best Practice: **Store in UTC, Convert on Display**

#### 1. **Store in UTC**
- Always use `TIMESTAMPTZ` (PostgreSQL), `TIMESTAMP WITH TIME ZONE`
- Never use `TIMESTAMP` (no zone)

```sql
INSERT INTO events (event_time) VALUES ('2023-10-01 12:00:00+00');
-- Or let DB use UTC: NOW() AT TIME ZONE 'UTC'
```

#### 2. **Convert on Read**
- App or frontend converts to user’s timezone

```java
// Java
ZonedDateTime utc = event.getEventTime().atZone(ZoneOffset.UTC);
ZonedDateTime local = utc.withZoneSameInstant(userZone);
```

```javascript
// JavaScript
new Date(event.event_time).toLocaleString('en-US', { timeZone: userTimezone })
```

#### 3. **Store User Timezone (Optional)**
```sql
ALTER TABLE users ADD COLUMN timezone TEXT DEFAULT 'UTC';
```

---

### ✅ Why This Works

| Issue | Solved By |
|------|---------|
| Daylight Saving Time | UTC doesn’t change |
| User in different zone | Convert at display |
| Server in different zone | Doesn’t matter — UTC is universal |
| Data consistency | All times in same zone |

---

### ✅ Real Project Example:

We had a **global scheduling app**:
- All events stored in `TIMESTAMPTZ`
- Frontend used `Intl.DateTimeFormat` to show local time
- Admin dashboard showed UTC
- No confusion during DST transitions

Also used **`AT TIME ZONE`** in PostgreSQL for reporting:
```sql
SELECT event_time AT TIME ZONE 'America/New_York' FROM events;
```

---

### 💡 Pro Insight:
> **"Time is hard" — store in UTC, display in local, never store local time.**

---

## ✅ Summary Table

| Topic | Key Takeaway |
|------|--------------|
| **URL Shortener** | Base-62 from auto-increment ID + Redis cache |
| **Sync vs Async Replication** | Sync = consistency; Async = availability |
| **Soft-Schema in SQL** | Use JSONB (PostgreSQL) — not EAV |
| **Self-Join** | Join table to itself — for hierarchies (e.g., org chart) |
| **Timezone Handling** | Store in UTC, convert on display — never store local time |

---

---

### 🔹 Question 36:  
> **What is a deadlock? How do databases detect and resolve it?**

#### ✅ Strong Answer:

---

### 🧠 What Is a Deadlock?

A **deadlock** occurs when **two or more transactions** are **each waiting for a resource** that the other holds — creating a **circular dependency**.

```text
Transaction A: holds Row 1, wants Row 2
Transaction B: holds Row 2, wants Row 1
→ Both wait forever
```

---

### ✅ How Databases Detect It

Databases use a **wait-for graph**:
- Nodes = transactions
- Edges = waiting for a lock
- If a **cycle** is detected → deadlock!

Detection methods:
- **Timeout-based**: Rollback if wait > N seconds
- **Immediate detection**: Constantly check for cycles (PostgreSQL, SQL Server)

---

### ✅ How Databases Resolve It

- **Choose a "victim"** (usually the one with least progress)
- **Roll back one transaction** with error:
  ```sql
  ERROR: deadlock detected
  DETAIL: Process 123 waits for Row 2; Process 456 waits for Row 1.
  ```
- Other transaction proceeds
- Application should **retry** the rolled-back transaction

---

### ✅ Example

```sql
-- Session A
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;

-- Session B
BEGIN;
UPDATE accounts SET balance = balance - 50 WHERE id = 2;
UPDATE accounts SET balance = balance + 50 WHERE id = 1; -- Waits for A

-- Session A
UPDATE accounts SET balance = balance + 100 WHERE id = 2; -- Waits for B → DEADLOCK!
```

Database kills one → the other completes.

---

### ✅ Real Project Example:

We had a **batch job** updating user balances:
- Deadlocked with **web requests**
- Fixed by:
  - Ordering updates by `user_id` (always low → high)
  - Reduced lock time
  - Added retry logic in app

Also used **`NOWAIT`** in PostgreSQL for non-blocking attempts.

---

### 💡 Pro Insight:
> Deadlocks are **inevitable** in high-concurrency systems — design your app to **retry gracefully**.

---

---

### 🔹 Question 37:  
> **How do you design a multi-tenant SaaS database? (Shared DB vs Isolated DB vs Schema-per-Tenant)**

#### ✅ Strong Answer:

This is a **critical architectural decision** with **trade-offs in cost, isolation, and complexity**.

---

### ✅ Three Main Patterns

| Pattern | Description | Pros | Cons |
|--------|-------------|------|------|
| **1. Shared DB, Shared Table** | All tenants in same tables, `tenant_id` column | ✅ Cheap, easy to manage | ❌ Risk of data leakage, harder to customize |
| **2. Shared DB, Schema-per-Tenant** | One schema per tenant | ✅ Good isolation, per-tenant extensions | ❌ Harder backups, max schemas limit |
| **3. Isolated DB per Tenant** | Separate database for each tenant | ✅ Maximum isolation, per-tenant tuning | ❌ Expensive, complex ops |

---

### ✅ When to Use Which?

| Use Case | Recommended Pattern |
|--------|----------------------|
| **Startup, low cost** | ✅ Shared DB, Shared Table |
| **Mid-size SaaS, balance** | ✅ Shared DB, Schema-per-Tenant |
| **Enterprise, high security** | ✅ Isolated DB |
| **Regulated data (HIPAA, GDPR)** | ✅ Isolated or Schema-per-Tenant |

---

### ✅ Real Project Example:

We built a **SaaS HR platform**:
- **Shared DB, Shared Table** for small tenants
- `tenant_id` indexed on all tables
- **Row-Level Security (RLS)** in PostgreSQL to prevent leaks
- For **enterprise clients**: **Isolated DB** with dedicated instance

Also used **feature flags** to enable per-tenant logic.

---

### 💡 Pro Insight:
> Start with **shared tables + RLS** — it scales well.  
> Only isolate when **compliance or performance demands it**.

---

---

### 🔹 Question 38:  
> **How do you design a multi-tenant SaaS database? (Shared DB vs Isolated DB vs Schema-per-Tenant)**

Wait — this is a duplicate of **Q37**.  
Let’s assume you meant:

> **"What is the impact of using `SELECT *` in production?"**

Let’s answer that.

---

### 🔹 Question 38:  
> **What is the impact of using `SELECT *` in production?**

#### ✅ Strong Answer:

`SELECT *` is a **performance anti-pattern** in production.

---

### ✅ Negative Impacts

| Issue | Explanation |
|------|-------------|
| **Network Overhead** | Fetches unused columns → more data over wire |
| **Memory Usage** | Larger result sets → higher app memory |
| **Caching Inefficiency** | Cache stores more data than needed |
| **Index Ineffectiveness** | Can’t use covering indexes |
| **Schema Fragility** | App breaks if column order changes |
| **Security Risk** | May expose sensitive fields (e.g., `password_hash`) |

---

### ✅ Example

```sql
-- Bad
SELECT * FROM users WHERE id = 123;
-- Returns: id, name, email, ssn, created_at, updated_at, password_hash...

-- Good
SELECT id, name, email FROM users WHERE id = 123;
```

✅ Saves 80%+ data transfer.

---

### ✅ Real Project Example:

We had a **dashboard** doing `SELECT * FROM audit_log`:
- 50 columns, many `TEXT`
- 10K rows → 500MB transfer
- Timed out

✅ Fixed:
```sql
SELECT user_id, action, timestamp FROM audit_log WHERE ...
```
→ 500MB → **5MB** → loads in 200ms

Also improved **query plan** (used covering index).

---

### 💡 Pro Insight:
> `SELECT *` is fine in **ad-hoc queries** — never in **application code**.

---

---

### 🔹 Question 39:  
> **How do you enforce data integrity without foreign keys (e.g., in sharded or NoSQL systems)?**

#### ✅ Strong Answer:

In **sharded SQL** or **NoSQL**, foreign keys are often **not supported** — so integrity must be enforced **elsewhere**.

---

### ✅ Strategies

#### 1. **Application-Level Checks**
- Before inserting `order`, verify `user_id` exists via API call
- Risk: **race conditions**, network failures

#### 2. **Event Sourcing / CQRS**
- Emit `UserCreated` event
- `Order Service` listens and maintains a **local user cache**
- On `createOrder`, checks cache

#### 3. **Service Coordination**
- `Order Service` calls `User Service` to validate user
- Use **circuit breakers**, retries

#### 4. **Eventual Consistency + Repair Jobs**
- Allow temporary inconsistency
- Run nightly job: "Find orders with invalid user_id → flag for review"

#### 5. **Soft References**
- Store `user_email` instead of `user_id`
- Email is immutable — safer for distributed systems

---

### ✅ Real Project Example:

We used **event-driven integrity** in a **microservices app**:
- `User Service` published `UserCreated`, `UserDeleted`
- `Order Service` subscribed → kept local `users` table
- On order creation: checked local table
- Used **Debezium** for CDC-based sync

✅ No FKs, but strong eventual integrity.

---

### 💡 Pro Insight:
> In distributed systems, **foreign keys are replaced by events and contracts**.

---

---

### 🔹 Question 40:  
> **What is a lagging index rebuild? How do you perform it with minimal downtime?**

#### ✅ Strong Answer:

---

### 🧠 What Is a Lagging Index Rebuild?

An **index rebuild** that happens **without blocking writes** — critical for large tables in production.

Traditional `CREATE INDEX` **locks the table** → downtime.

We want **zero-downtime index creation**.

---

### ✅ Solution: **Concurrent Index Creation**

#### PostgreSQL: `CREATE INDEX CONCURRENTLY`
```sql
CREATE INDEX CONCURRENTLY idx_users_email ON users(email);
```

✅ How it works:
- Builds index **without blocking DML** (INSERT/UPDATE/DELETE)
- Takes longer
- Can be canceled without corrupting data
- Runs in background

❌ Limitations:
- Cannot run during schema changes
- May fail — then index is "invalid" → must drop and retry

---

### ✅ Other Databases

| Database | Command |
|--------|--------|
| **MySQL (InnoDB)** | `CREATE INDEX ... ALGORITHM=INPLACE, LOCK=NONE` |
| **SQL Server** | `CREATE INDEX ... WITH (ONLINE = ON)` |
| **Oracle** | `CREATE INDEX ... ONLINE` |

---

### ✅ Real Project Example:

We added an index on `orders(user_id)` for a 100M-row table:
- Normal `CREATE INDEX` → 45min lock → not acceptable
- Used `CREATE INDEX CONCURRENTLY` → 90min, **zero downtime**
- Monitored with `pg_stat_progress_create_index`

Also used **online schema change tools** like **pt-online-schema-change** (MySQL).

---

### 💡 Pro Insight:
> In production, **never block writes** for index creation.  
> Use **concurrent/index-online** methods — they’re slower but safe.

---

## ✅ Summary Table

| Topic | Key Takeaway |
|------|--------------|
| **Deadlock** | Circular wait → DB kills one; app should retry |
| **Multi-Tenant DB** | Shared (cheap), Schema (balanced), Isolated (secure) |
| **SELECT \*** | Never in production — wastes resources |
| **Data Integrity (No FK)** | Use events, service calls, or local caches |
| **Lagging Index Rebuild** | Use `CONCURRENTLY` (PostgreSQL) or `ONLINE` (others) |

---

