Here are **10 real-world, high-pressure production scenario questions** — just like the one you faced — designed to test **how you think, prioritize, and act under pressure** in a **senior engineering or SRE role**.

These simulate **actual on-call incidents** and are used in interviews at **FAANG, fintech, and high-scale SaaS companies**.

---

### 🔥 1.  
> **Your login API suddenly returns 500s for 30% of users. No code was deployed today. The database CPU is at 98%. What do you do?**

💡 *Tests: DB performance, caching, incident triage*

---

### 🔥 2.  
> **After a new release, checkout success rate drops from 99% to 60%. The payment service shows no errors. You have 1 hour before Black Friday sales begin. Go.**

💡 *Tests: Observability, rollback strategy, external service debugging*

---

### 🔥 3.  
> **Your app works fine locally and in staging. In production, `/api/users` returns empty JSON. No errors in logs. What’s wrong?**

💡 *Tests: Configuration, environment differences, debugging skills*

---

### 🔥 4.  
> **Your microservice is consuming 100% CPU and not responding. You can’t SSH into the pod. How do you diagnose and fix it?**

💡 *Tests: Container debugging, observability, kubectl skills*

---

### 🔥 5.  
> **You get paged: "95% of API requests are taking over 10 seconds." You see a spike in garbage collection. What’s happening, and how do you fix it?**

💡 *Tests: JVM tuning, memory leaks, GC analysis*

---

### 🔥 6.  
> **Your CI/CD pipeline suddenly fails for all services with "Cannot pull image from registry." No config changed. What’s the root cause?**

💡 *Tests: Infrastructure knowledge, registry issues, network policies*

---

### 🔥 7.  
> **Users report they’re being logged out every 30 seconds. Session data is stored in Redis. What do you check?**

💡 *Tests: Session management, Redis health, TTL misconfiguration*

---

### 🔥 8.  
> **Your cron job that runs every 5 minutes is now taking 6 minutes. It’s starting to pile up. How do you fix it without breaking data consistency?**

💡 *Tests: Job design, concurrency, idempotency, performance*

---

### 🔥 9.  
> **After a database failover, your app returns stale data for 10 minutes. Replication lag is 0. Why?**

💡 *Tests: Connection pooling, read-from-replica logic, caching*

---

### 🔥 10.  
> **You deploy a change that adds a new column to a 500M-row table. The migration has been running for 6 hours. What do you do?**

💡 *Tests: DB operations, online schema changes, rollback strategy*

---

## 🎯 How to Use These

### For Interview Prep:
- Pick one per day
- Give yourself **10–15 minutes** to answer out loud
- Structure: **Triage → Isolate → Fix → Verify**
- Record yourself — listen for clarity and confidence

### For Team Training:
- Use in **fire drills** or **blameless post-mortems**
- Simulate with timers and dashboards
- Rotate who plays "incident commander"

---

## 💡 Pro Tip: The 4-Step Incident Response Framework

Use this every time:

| Step | Action |
|------|-------|
| 1. **Stabilize** | Rollback, scale, cache, circuit break |
| 2. **Observe** | Logs, metrics, traces, APM, thread dumps |
| 3. **Isolate** | Which service? Which user? Which region? |
| 4. **Fix & Verify** | Apply fix → confirm resolution → monitor |

---

## 🚨 Bonus: Real-World Debugging Checklist

Print this and keep it handy:

```text
✅ Check health endpoint
✅ Check logs (error, access, GC)
✅ Check metrics: CPU, memory, threads, DB connections
✅ Check external services (latency, errors)
✅ Check config differences (prod vs staging)
✅ Check recent deploys, config changes, data spikes
✅ Roll back if unsure
```

---
