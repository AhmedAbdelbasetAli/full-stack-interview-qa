# 🐳 Docker & Kubernetes Interview Questions Deep Dive  
**25+ Advanced Questions with In-Depth Explanations**

This document covers **Docker & Kubernetes** at a **senior level** — perfect for **DevOps**, **SRE**, and **platform engineer** interviews.

Each question includes:
- ✅ Clear explanation
- ✅ Real-world examples
- ✅ Diagrams where needed
- ✅ Best practices
- ✅ Interview-ready answers

---

## 🔹 1. What is Docker? How does it work under the hood?

> **Docker** is a **containerization platform** that packages applications and their dependencies into **lightweight, portable containers**.

---

### ✅ How Docker Works

```
[App] → [Docker Image] → [Container] → [Host OS (Linux)]
```

- **Image**: Immutable template (layers of filesystem changes)
- **Container**: Runnable instance of an image
- **Docker Engine**: Daemon (`dockerd`) + CLI (`docker`)

---

### 🔹 Under the Hood: Linux Kernel Features

| Feature | Purpose |
|--------|--------|
| ✅ **Namespaces** | Isolation (PID, Network, Mount, User, etc.) |
| ✅ **cgroups (Control Groups)** | Resource limits (CPU, memory) |
| ✅ **Union File Systems (e.g., overlay2)** | Layered images (read-only + writable layer) |

---

### ✅ Example: Dockerfile Layers
```dockerfile
FROM ubuntu:22.04         # Layer 1: Base OS
RUN apt-get update        # Layer 2: Update cache
RUN apt-get install curl  # Layer 3: Install curl
COPY app.py /app/         # Layer 4: Copy app
CMD ["python", "app.py"]  # Layer 5: Command
```

✅ Each `RUN`, `COPY` creates a **new layer**  
✅ Layers are **cached** — rebuilds are fast

---

### 📌 Interview Answer

> _"Docker uses Linux kernel features: namespaces for isolation, cgroups for resource limits, and union file systems for layered images. An image is built from immutable layers; a container is a runnable instance with a writable layer on top. Docker Engine manages containers via daemon and CLI."_  

---

## 🔹 2. What is a Docker image? How is it different from a container?

| Concept | Description |
|--------|-------------|
| **Image** | Immutable template (like a class) — built from Dockerfile |
| **Container** | Runnable instance of an image (like an object) — has writable layer |

---

### ✅ Key Differences

| Feature | Image | Container |
|--------|------|----------|
| **State** | Immutable | Mutable (writable layer) |
| **Storage** | Read-only layers | Read-only + writable top layer |
| **Running** | ❌ No | ✅ Yes |
| **Created From** | Dockerfile | `docker run` from image |
| **Size** | Smaller | Slightly larger (writable layer) |

---

### ✅ Example
```bash
# Build image
docker build -t myapp:v1 .

# Run container
docker run -d myapp:v1

# List images
docker images

# List containers
docker ps
```

---

### 📌 Interview Answer

> _"An image is a read-only template built from a Dockerfile. A container is a running instance of that image with a writable layer. Images are shared and immutable; containers are ephemeral and can be started, stopped, or deleted."_  

---

## 🔹 3. What is the difference between `CMD` and `ENTRYPOINT` in Docker?

| Directive | Purpose | Can be overridden? |
|----------|--------|-------------------|
| `CMD` | Default command (arguments) | ✅ Yes, by `docker run` args |
| `ENTRYPOINT` | Executable (main command) | ❌ Hard to override |

---

### ✅ Examples

#### `CMD` Only
```dockerfile
CMD ["echo", "Hello"]
```
```bash
docker run myapp           # → echo Hello
docker run myapp hi        # → hi (overrides CMD)
```

#### `ENTRYPOINT` + `CMD`
```dockerfile
ENTRYPOINT ["echo"]
CMD ["Hello"]
```
```bash
docker run myapp           # → echo Hello
docker run myapp hi        # → echo hi (CMD overridden)
```

#### Only `ENTRYPOINT`
```dockerfile
ENTRYPOINT ["echo", "Hello"]
```
```bash
docker run myapp hi        # → echo Hello hi (args appended)
```

---

### 📌 Best Practice

> Use `ENTRYPOINT` for **executable wrappers** (e.g., `java -jar app.jar`)  
> Use `CMD` for **default arguments**

---

### 📌 Interview Answer

> _"ENTRYPOINT sets the main executable and is hard to override. CMD sets default arguments and can be replaced. I use ENTRYPOINT for the main command (like 'java -jar') and CMD for default args. This makes containers behave like binaries."_  

---

## 🔹 4. What is Kubernetes? Why use it over Docker Compose?

> **Kubernetes (K8s)** is an **open-source platform** for **automating deployment, scaling, and management** of containerized applications.

---

### ✅ Kubernetes vs Docker Compose

| Feature | Docker Compose | Kubernetes |
|--------|----------------|-----------|
| **Use Case** | Local dev, single host | Production, multi-node clusters |
| **Scaling** | Manual | Auto-scaling (HPA) |
| **Self-Healing** | ❌ No | ✅ Yes (restarts failed pods) |
| **Service Discovery** | Internal DNS | Built-in DNS + Services |
| **Rolling Updates** | Limited | Full support |
| **Multi-Cloud** | ❌ No | ✅ Yes |
| **Learning Curve** | Low | High |

---

### ✅ When to Use Which?

| Scenario | Choice |
|--------|--------|
| ✅ Local development | Docker Compose |
| ✅ CI/CD pipelines | Docker Compose |
| ✅ Production microservices | Kubernetes |
| ✅ Multi-region apps | Kubernetes |
| ✅ Serverless containers | Knative (on K8s) |

---

### 📌 Interview Answer

> _"Docker Compose is for local development on a single host. Kubernetes is for production — it provides auto-scaling, self-healing, rolling updates, and service discovery across clusters. I use Compose for dev, K8s for production."_  

---

## 🔹 5. Explain the Kubernetes Architecture

```
[User] → [kubectl] → [API Server] ←→ [etcd]
                          ↓
          [Controller Manager] [Scheduler]
                          ↓
                  [Worker Nodes]
                  /      |       \
         [Kubelet] [Kube Proxy] [Container Runtime (Docker/containerd)]
```

---

### ✅ Control Plane Components

| Component | Purpose |
|---------|--------|
| **API Server** | Front-end for K8s API (REST) |
| **etcd** | Consistent, distributed key-value store (cluster state) |
| **Controller Manager** | Runs controllers (Node, ReplicaSet, Deployment) |
| **Scheduler** | Assigns pods to nodes based on resources, affinity |
| **Cloud Controller Manager** | Integrates with cloud providers (AWS, GCP) |

---

### ✅ Node Components

| Component | Purpose |
|---------|--------|
| **Kubelet** | Ensures containers are running in a Pod |
| **Kube Proxy** | Network proxy (implements Services) |
| **Container Runtime** | Runs containers (Docker, containerd, CRI-O) |

---

### 📌 Interview Answer

> _"Kubernetes has a control plane (API Server, etcd, Scheduler, Controller Manager) and worker nodes. The API Server is the front-end. etcd stores cluster state. Scheduler places pods. Kubelet runs on each node to manage pods. Kube Proxy handles networking. It's distributed and highly available."_  

---

## 🔹 6. What is a Pod? Why not run containers directly?

> A **Pod** is the **smallest deployable unit** in Kubernetes — it can contain **one or more containers**.

---

### ✅ Why Pods?

| Reason | Explanation |
|-------|-------------|
| ✅ **Shared Network** | All containers in a Pod share IP and port space |
| ✅ **Shared Storage** | Volumes mounted to all containers in Pod |
| ✅ **Tightly Coupled Containers** | e.g., app + sidecar (logging, proxy) |
| ✅ **Co-location & Scheduling** | All containers in a Pod are scheduled together |

---

### ✅ Example: App + Sidecar
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-with-logging
spec:
  containers:
  - name: app
    image: myapp:v1
    ports:
    - containerPort: 8080
  - name: log-shipper
    image: fluentd
    volumeMounts:
    - name: logs
      mountPath: /var/log
  volumes:
  - name: logs
    emptyDir: {}
```

✅ App writes logs → sidecar ships them

---

### 📌 Interview Answer

> _"A Pod is the smallest unit — it can have multiple containers. I use it for tightly coupled containers, like an app and a logging sidecar. They share network and storage. Kubernetes schedules Pods, not containers, so they're co-located and share IP."_  

---

## 🔹 7. What are Kubernetes Services? Types?

> A **Service** is an **abstraction** that defines a **logical set of Pods** and a **policy to access them**.

It provides **stable networking** despite pod churn.

---

### ✅ Service Types

| Type | Purpose |
|------|--------|
| **ClusterIP** | Internal service (default) — only accessible within cluster |
| **NodePort** | Exposes service on a static port on each node (30000-32767) |
| **LoadBalancer** | Creates external load balancer (AWS ELB, GCP Load Balancer) |
| **ExternalName** | Maps to external DNS name (e.g., `api.external.com`) |

---

### ✅ Example: LoadBalancer Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: frontend
```

✅ Creates external LB → routes to Pods with `app=frontend`

---

### 📌 Interview Answer

> _"Services provide stable networking for Pods. ClusterIP for internal, NodePort for node access, LoadBalancer for external traffic. I use LoadBalancer for public APIs, ClusterIP for internal microservices. Services use selectors to route to Pods."_  

---

## 🔹 8. What is an Ingress Controller?

> An **Ingress** is an API object that manages **external access** to services, typically HTTP/HTTPS.

An **Ingress Controller** (e.g., NGINX, Traefik) **implements** the Ingress rules.

---

### ✅ Example: Ingress Rule
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: myapp.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-svc
            port:
              number: 80
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-svc
            port:
              number: 80
```

✅ Routes:
- `myapp.com/api` → `api-svc`
- `myapp.com/` → `web-svc`

---

### 📌 Interview Answer

> _"Ingress manages external HTTP(S) access. It defines routing rules based on host and path. The Ingress Controller (like NGINX) implements them. I use it to route traffic to different services (e.g., /api → backend, / → frontend) with one external IP."_  

---

## 🔹 9. What is a ConfigMap and Secret?

| Resource | Purpose | Storage |
|--------|--------|--------|
| **ConfigMap** | Store non-sensitive config (e.g., env vars, config files) | Plaintext |
| **Secret** | Store sensitive data (e.g., passwords, tokens) | Base64-encoded (not encrypted by default) |

---

### ✅ Example: ConfigMap
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  LOG_LEVEL: "debug"
  DB_URL: "postgres://db:5432/app"
```

### ✅ Mount as Volume or Env Var
```yaml
env:
  - name: LOG_LEVEL
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: LOG_LEVEL
```

---

### ✅ Secret (Base64-encoded)
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  password: MWYyZDFlMmU2N2Rm # echo -n "my-secret" | base64
```

> ⚠️ Use **Encryption at Rest** or **external secret managers** (HashiCorp Vault) for production.

---

### 📌 Interview Answer

> _"ConfigMap stores non-sensitive config like log levels. Secret stores sensitive data like passwords — base64-encoded, but I enable encryption at rest or use Vault. I inject them as environment variables or volumes. Never hardcode secrets in manifests."_  

---

## 🔹 10. What is a PersistentVolume (PV) and PersistentVolumeClaim (PVC)?

> **PersistentVolume (PV)**: Cluster-wide storage resource (e.g., AWS EBS, GCP PD)  
> **PersistentVolumeClaim (PVC)**: Request for storage by a Pod

---

### ✅ Flow
```
[Admin] → Create PV (10GB EBS)
                     ↓
[Developer] → Create PVC (Request 5GB)
                     ↓
[Pod] → Mount PVC → Uses PV
```

---

### ✅ Example
```yaml
# PV
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0001
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  awsElasticBlockStore:
    volumeID: vol-123456
    fsType: ext4

# PVC
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi

# Pod
spec:
  containers:
    - name: app
      volumeMounts:
        - name: data
          mountPath: /data
  volumes:
    - name: data
      persistentVolumeClaim:
        claimName: my-pvc
```

---

### 📌 Interview Answer

> _"PV is physical storage (e.g., EBS), PVC is a request for storage. Admins create PVs, developers request with PVCs. Pod uses PVC, which binds to a PV. It decouples storage from pods — great for stateful apps like databases."_  

---

## 🔹 11. What is Helm? Why use it?

> **Helm** is a **package manager for Kubernetes** — like `apt` or `yum` for K8s.

It uses **charts** (pre-packaged K8s manifests).

---

### ✅ Why Use Helm?

| Benefit | Explanation |
|--------|-------------|
| ✅ **Versioned Deployments** | Rollback to previous chart version |
| ✅ **Templating** | Use values.yaml to customize |
| ✅ **Reusable Charts** | Share across teams |
| ✅ **Dependency Management** | Charts can depend on others |
| ✅ **Upgrade & Rollback** | `helm upgrade`, `helm rollback` |

---

### ✅ Example
```bash
# Install Redis
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-redis bitnami/redis

# Upgrade
helm upgrade my-redis bitnami/redis --set password=12345

# Rollback
helm rollback my-redis 1
```

---

### 📌 Interview Answer

> _"Helm is a package manager for Kubernetes. It uses charts to package K8s manifests with templating. I use it to deploy apps like Redis, PostgreSQL, or my own services. It supports versioning, rollback, and parameterization — much better than raw YAML."_  

---

## 🔹 12. How do you secure a Kubernetes cluster?

| Layer | Security Measure |
|------|------------------|
| ✅ **Network** | Network Policies (Calico, Cilium), Service Mesh (Istio) |
| ✅ **Authentication** | RBAC, OIDC, LDAP |
| ✅ **Authorization** | Role-Based Access Control (RBAC) |
| ✅ **Secrets** | Encryption at rest, external secret managers |
| ✅ **Pod Security** | Pod Security Policies (PSP) or Pod Security Admission |
| ✅ **Image Security** | Scan images (Trivy, Clair), use private registries |
| ✅ **Audit Logging** | Enable audit logs for compliance |
| ✅ **Minimize Attack Surface** | Disable unused APIs, use minimal base images |

---

### ✅ Example: RBAC
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: dev
subjects:
- kind: User
  name: alice
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

---

### 📌 Interview Answer

> _"I secure K8s with RBAC for least privilege, network policies to restrict pod traffic, and secret encryption. I scan images for vulnerabilities, use minimal base images, and enable audit logging. I also use Pod Security Admission to block privileged containers."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I package apps with Docker, deploy to Kubernetes with Helm, expose via Ingress, and secure with RBAC and network policies."_  

That shows **deep, integrated DevOps knowledge**.

