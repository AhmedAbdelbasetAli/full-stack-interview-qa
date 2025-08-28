Absolutely! Let’s tackle these **three high-pressure production scenarios** with **clear, structured, senior-engineer-level answers** — exactly how you’d respond in a real on-call situation or technical interview.

We’ll use the **Triage → Isolate → Fix → Verify** framework for each.

---

## 🔥 4.  
> **Your microservice is consuming 100% CPU and not responding. You can’t SSH into the pod. How do you diagnose and fix it?**

### ✅ Strong Answer:

> "Even though I can’t SSH, I have other tools. My goal is to **diagnose the CPU spike** and **restore service** — fast."

---

### 🚨 Step 1: Triage & Stabilize (0–10 mins)
- **Scale down the affected deployment** → reduce blast radius
- **Scale up healthy instances** → maintain capacity
- **Check Kubernetes dashboard**:
  ```bash
  kubectl top pods --namespace=prod
  ```
  → Confirm which pod has 100% CPU
- **Check logs**:
  ```bash
  kubectl logs <pod-name> --previous  # in case it crashed
  kubectl logs <pod-name> | grep -i error
  ```

---

### 🔍 Step 2: Isolate (10–25 mins)
Likely causes:
1. **Infinite loop** in code (e.g., bad recursion)
2. **Memory leak → GC thrashing**
3. **Hot loop in async task** (e.g., polling with no sleep)
4. **Thread explosion** (e.g., unbounded thread pool)
5. **Bad regex** (ReDoS — catastrophic backtracking)

Check:
- **JVM metrics** (if exposed): GC, thread count, heap
- **APM tool** (Datadog, New Relic): flame graphs, hot methods
- **kubectl describe pod** → any OOMKills?
- **kubectl exec** (if container has `jstack`):
  ```bash
  kubectl exec <pod> -- jstack <pid> > thread-dump.txt
  ```
  → Look for threads in `RUNNABLE` state

---

### 🛠️ Step 3: Fix (25–40 mins)
- ✅ **Restart the pod** → immediate relief
- If recurring:
  - Add `ThreadFactory` with naming to find runaway threads
  - Fix infinite loop or bad regex
  - Tune thread pool (use `ThreadPoolTaskExecutor` with bounds)
- If Java: enable **async profiler** for next time

---

### ✅ Step 4: Verify (40–45 mins)
- Confirm CPU back to < 50%
- No 500s
- Add **alert** for CPU > 80% for 5 mins
- Document: "Avoid unbounded loops and thread pools"

> "Most likely: an **infinite loop or ReDoS**. Restarting buys time; fixing the code prevents recurrence."

---

---

## 🔥 5.  
> **You get paged: "95% of API requests are taking over 10 seconds." You see a spike in garbage collection. What’s happening, and how do you fix it?**

### ✅ Strong Answer:

> "GC spike = **JVM under memory pressure**. The app is spending more time cleaning memory than doing work. This is either a **memory leak** or **sudden load**."

---

### 🚨 Step 1: Triage & Stabilize (0–10 mins)
- **Scale out** (add more pods) → distribute load
- **Check GC logs**:
  ```bash
  # Look for full GC frequency
  grep "Full GC" gc.log
  ```
- **Check heap usage** (via JMX, Prometheus, or APM)
- **Enable heap dump on OOM** if not already:
  ```bash
  -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/dumps
  ```

---

### 🔍 Step 2: Isolate (10–25 mins)
Check:
1. **Heap trends**:
   - Is old gen growing? → memory leak
   - Spikes with traffic? → normal, but tune GC
2. **GC logs**:
   - Frequent `Full GC` → heap too small or leak
   - Long pauses (> 1s) → stop-the-world
3. **Common leak sources**:
   - Static collections (e.g., `static Map`)
   - Unclosed resources (InputStream, ResultSet)
   - Caches without TTL
   - Listener leaks

Use:
- `jstat -gc <pid>` → GC stats
- `jmap -histo <pid>` → top object counts
- Eclipse MAT (if heap dump available)

---

### 🛠️ Step 3: Fix (25–40 mins)
- ✅ **Restart the pod** → immediate relief
- Short-term:
  - Increase heap: `-Xmx4g`
  - Tune GC (e.g., G1GC: `-XX:+UseG1GC`)
- Long-term:
  - Fix memory leak (e.g., remove static cache)
  - Add TTL to in-memory caches
  - Use `WeakHashMap` or `Caffeine`

---

### ✅ Step 4: Verify (40–45 mins)
- Confirm GC pauses < 200ms
- Heap stable
- Latency < 500ms
- Add **GC monitoring alert**

> "Most likely: a **memory leak in a static cache**. Restarting helps; fixing the leak prevents it."

---

---

## 🔥 6.  
> **Your CI/CD pipeline suddenly fails for all services with "Cannot pull image from registry." No config changed. What’s the root cause?**

### ✅ Strong Answer:

> "Since no config changed, the issue is likely **infrastructure or authentication**. This affects all services — so it’s **centralized**: registry, network, or auth."

---

### 🚨 Step 1: Triage & Stabilize (0–10 mins)
- Check **registry status** (e.g., AWS ECR, Docker Hub, GCR)
- Is the registry **down** or **rate-limiting**?
- Check **CI/CD logs**:
  - Exact error: `unauthorized`, `timeout`, `not found`?
- Try pulling an image **manually**:
  ```bash
  docker login <registry>
  docker pull <image>:<tag>
  ```

---

### 🔍 Step 2: Isolate (10–25 mins)
Likely causes:
1. **Registry outage** (e.g., Docker Hub rate limits)
2. **Expired registry credentials** (e.g., ECR token lasts 12 hours)
3. **Network policy / firewall** blocking registry
4. **DNS failure** for registry domain
5. **Image cleanup policy** deleted old tags

Check:
- `kubectl describe pod` → `Failed to pull image: unauthorized`
- CI/CD logs: `Error response from daemon: Head https://...: net/http: request canceled`
- Can you resolve `your-registry.com`?

---

### 🛠️ Step 3: Fix (25–40 mins)
- ✅ **Renew registry credentials**:
  - For ECR: run `aws ecr get-login-password`
  - Update CI/CD secret
- Or:
  - **Whitelist IP** if registry has allowlist
  - **Fix DNS** or network policy
  - **Re-tag images** if deleted
- As workaround:
  - Use a **mirror registry**
  - Cache images in CI runner

---

### ✅ Step 4: Verify (40–45 mins)
- Run a test pipeline → success
- Confirm image pull works
- Add **monitoring** for registry health
- Rotate credentials **automatically** (e.g., GitHub Actions + AWS OIDC)

> "Most likely: **expired ECR token**. Fix: rotate credentials. Prevent: automate token refresh."

---

## ✅ Summary: What You’ve Shown

| Scenario | Key Skill |
|--------|----------|
| **100% CPU pod** | Container debugging, `kubectl`, thread dumps |
| **GC spike** | JVM internals, memory leak detection |
| **CI/CD pull failure** | Infrastructure, auth, registry knowledge |

---

## 💡 Pro Insight

> In distributed systems, **failures are inevitable** — but **how you respond defines you**.

You’ve now shown:
- **Speed** (triage fast)
- **Depth** (know tools and internals)
- **Ownership** (fix, verify, prevent)

---
