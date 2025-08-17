# 🌱 Spring Data JPA & Transactions Deep Dive  
**Repositories, Entities, Queries, and Isolation Levels**

This document dives into **advanced Spring Data JPA** and **transaction management** — critical for **enterprise Java applications** and **senior backend roles**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 16. What is `CrudRepository` vs `JpaRepository`?

Both are **Spring Data JPA interfaces** that reduce boilerplate, but they differ in **features and capabilities**.

| Feature | `CrudRepository` | `JpaRepository` |
|--------|------------------|-----------------|
| **Purpose** | Basic CRUD operations | JPA-specific features |
| **Extends** | `Repository` | `CrudRepository` + `PagingAndSortingRepository` |
| **Batch Operations** | ❌ No | ✅ Yes (`flush`, `saveAndFlush`) |
| **Pagination & Sorting** | ❌ No | ✅ Yes (via `PagingAndSortingRepository`) |
| **JPA-Specific** | ❌ No | ✅ Yes (`getOne`, `flush`) |
| **Use Case** | Any Spring Data module (Mongo, Redis) | JPA/Hibernate only |

---

### 🔹 16.1 `CrudRepository` – Generic CRUD

```java
public interface UserRepository extends CrudRepository<User, Long> {
    // Inherited methods:
    // save(T), findById(ID), findAll(), deleteById(ID), count(), etc.
}
```

✅ Part of **Spring Data Commons** — works with **any datastore** (Mongo, Redis, JDBC)

---

### 🔹 16.2 `JpaRepository` – JPA-Specific Features

```java
public interface UserRepository extends JpaRepository<User, Long> {
    // All CrudRepository methods +:
    // flush() – flush changes to DB
    // saveAndFlush() – save + flush
    // getOne(ID) – lazy-loading proxy (deprecated in favor of `getReference`)
    // findAll(Pageable) – pagination
    // findAll(Sort) – sorting
}
```

✅ Extends `PagingAndSortingRepository` → supports:
```java
Page<User> findAll(Pageable pageable);
List<User> findAll(Sort sort);
```

---

### 🔹 16.3 Which One Should You Use?

| Use Case | Choice |
|--------|--------|
| ✅ General CRUD, multiple data stores | `CrudRepository` |
| ✅ JPA with pagination, sorting, flush | `JpaRepository` |
| ✅ REST API with pagination | `JpaRepository` |

✅ **In practice, `JpaRepository` is used 90% of the time** in JPA projects.

---

### 📌 Interview Answer

> _"`CrudRepository` provides basic CRUD operations and is datastore-agnostic. `JpaRepository` extends it with JPA-specific features like `flush`, `saveAndFlush`, and built-in pagination and sorting. I use `JpaRepository` in JPA projects because I need pagination and immediate persistence control. `CrudRepository` is more generic — useful if I ever switch data stores."_  

---

## 17. What is the `@Entity` annotation used for?

> `@Entity` marks a **Java class as a JPA entity** — meaning it’s **mapped to a database table**.

It’s part of **JPA (Java Persistence API)**, not Spring.

---

### 🔹 17.1 Example

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "full_name", nullable = false)
    private String name;

    @Column(unique = true)
    private String email;

    // Constructors, getters, setters
}
```

---

### 🔹 17.2 Key Annotations

| Annotation | Purpose |
|----------|--------|
| `@Entity` | Marks class as JPA entity |
| `@Table` | Specifies table name |
| `@Id` | Primary key |
| `@GeneratedValue` | Auto-generate ID (IDENTITY, SEQUENCE, AUTO) |
| `@Column` | Map field to column (name, nullable, unique, length) |
| `@Transient` | Field not persisted |
| `@Enumerated` | Store enum as string or ordinal |
| `@Temporal` | For `Date`, `Calendar` (legacy — use `LocalDateTime` now) |

---

### 🔹 17.3 How It Works

- At startup, JPA provider (e.g., Hibernate) scans for `@Entity` classes
- Creates **mapping metadata**
- Generates SQL for CRUD operations
- If `ddl-auto=update`, creates/updates tables

---

### 📌 Interview Answer

> _"`@Entity` tells JPA that a class is mapped to a database table. Combined with `@Id`, `@Column`, and `@Table`, it defines the ORM mapping. I use it on domain models like `User`, `Order`, or `Account`. Hibernate uses this metadata to generate SQL and manage persistence. It’s the foundation of JPA-based data access."_  

---

## 18. How do you define a custom query in Spring Data JPA?

There are **three main ways** to write custom queries:

| Method | When to Use |
|-------|-------------|
| ✅ **Query Methods by Convention** | Simple queries (findBy, findByAnd, etc.) |
| ✅ **`@Query` Annotation** | Complex JPQL or native SQL |
| ✅ **Query by Example (QBE)** | Dynamic, optional filters |

---

### 🔹 18.1 Query Methods (Recommended for Simple Cases)

```java
public interface UserRepository extends JpaRepository<User, Long> {

    List<User> findByRole(String role);
    List<User> findByRoleAndActive(String role, boolean active);
    User findByEmail(String email);
    List<User> findByAgeGreaterThan(int age);
    List<User> findTop3ByOrderByCreatedAtDesc();
    boolean existsByEmail(String email);
}
```

✅ Spring parses method names and generates JPQL automatically.

---

### 🔹 18.2 `@Query` Annotation (For Complex Queries)

#### 🟦 JPQL (Recommended)
```java
@Query("SELECT u FROM User u WHERE u.email LIKE %:domain%")
List<User> findByDomain(@Param("domain") String domain);

@Query("SELECT u.name, u.email FROM User u WHERE u.active = true")
List<Object[]> findActiveUserNamesAndEmails();
```

#### 🟦 Native SQL
```java
@Query(value = "SELECT * FROM users WHERE age > ?1", nativeQuery = true)
List<User> findByAgeGreaterThanNative(int age);
```

✅ Use native SQL for:
- Database-specific functions
- Performance-critical queries
- Complex joins

---

### 🔹 18.3 Query by Example (QBE)

```java
@Service
public class UserService {

    @Autowired
    private UserRepository repo;

    public List<User> search(User userExample) {
        Example<User> example = Example.of(userExample);
        return repo.findAll(example);
    }
}
```

✅ Great for **admin search panels** with optional filters.

---

### 📌 Interview Answer

> _"I define custom queries using Spring Data JPA's method naming convention for simple cases — like `findByEmail`. For complex logic, I use `@Query` with JPQL or native SQL. I prefer JPQL for portability, but use native SQL when I need database-specific features. For dynamic filters, I use Query by Example. This keeps data access clean and maintainable."_  

---

## 19. What is the `@Transactional` annotation? When would you use it?

> `@Transactional` defines the **scope of a database transaction** — ensuring **ACID properties** (Atomicity, Consistency, Isolation, Durability).

It’s **essential for data integrity**.

---

### 🔹 19.1 Example: Money Transfer

```java
@Service
public class TransferService {

    @Autowired
    private AccountRepository accountRepo;

    @Transactional
    public void transfer(Long fromId, Long toId, BigDecimal amount) {
        Account from = accountRepo.findById(fromId).orElseThrow();
        Account to = accountRepo.findById(toId).orElseThrow();

        if (from.getBalance().compareTo(amount) < 0) {
            throw new InsufficientFundsException();
        }

        from.setBalance(from.getBalance().subtract(amount));
        to.setBalance(to.getBalance().add(amount));

        accountRepo.save(from);
        accountRepo.save(to);
        // If any step fails → entire transaction rolls back
    }
}
```

✅ If `save(to)` fails → `save(from)` is **undone**

---

### 🔹 19.2 Key Attributes

| Attribute | Purpose |
|---------|--------|
| `propagation` | Transaction flow (REQUIRED, REQUIRES_NEW, etc.) |
| `isolation` | Isolation level (READ_COMMITTED, SERIALIZABLE, etc.) |
| `rollbackFor` | Which exceptions trigger rollback |
| `noRollbackFor` | Which exceptions do **not** trigger rollback |
| `readOnly` | Optimizes read-only transactions |

```java
@Transactional(
    rollbackFor = {InsufficientFundsException.class},
    noRollbackFor = {IllegalArgumentException.class},
    isolation = Isolation.READ_COMMITTED,
    propagation = Propagation.REQUIRED
)
```

---

### 🔹 19.3 Where to Use It?

| Location | Use Case |
|--------|---------|
| ✅ **Service Layer** | Business logic with multiple DB operations |
| ❌ **Controller Layer** | Too broad — use in service |
| ✅ **Public Methods** | Only works on public methods by default |

---

### 📌 Interview Answer

> _"`@Transactional` ensures a block of code runs in a single database transaction — all or nothing. I use it in the service layer for operations like money transfers, where multiple updates must succeed together. It rolls back on exceptions and supports propagation, isolation, and rollback rules. It’s critical for data consistency in production systems."_  

---

## 20. What are the transaction isolation levels in Spring?

> **Isolation level** controls **how transactions interact** with each other — balancing **consistency** vs **performance**.

Spring supports all standard JPA isolation levels.

---

### 🔹 20.1 Isolation Levels (Low to High)

| Level | Dirty Read | Non-Repeatable Read | Phantom Read | Use Case |
|------|-----------|---------------------|--------------|---------|
| `READ_UNCOMMITTED` | ✅ Yes | ✅ Yes | ✅ Yes | Rare — dirty reads allowed |
| `READ_COMMITTED` | ❌ No | ✅ Yes | ✅ Yes | ✅ **Default in most databases** |
| `REPEATABLE_READ` | ❌ No | ❌ No | ✅ Yes | MySQL default |
| `SERIALIZABLE` | ❌ No | ❌ No | ❌ No | Maximum safety — low concurrency |

---

### 🔹 20.2 Example: Setting Isolation

```java
@Transactional(isolation = Isolation.READ_COMMITTED)
public void updateBalance(Long id, BigDecimal amount) {
    // Only sees committed data
}
```

---

### 🔹 20.3 When to Use Which?

| Level | When to Use |
|------|-------------|
| `READ_COMMITTED` | ✅ Most apps — good balance |
| `REPEATABLE_READ` | ✅ When you need consistent reads in a transaction |
| `SERIALIZABLE` | ✅ Financial systems, audit trails — but expect locks |
| `READ_UNCOMMITTED` | ❌ Avoid — can read uncommitted data |

---

### 🔹 20.4 Common Issues

| Issue | Caused By | Prevented By |
|------|----------|-------------|
| **Dirty Read** | Reading uncommitted data | `READ_COMMITTED+` |
| **Non-Repeatable Read** | Same row changes during tx | `REPEATABLE_READ+` |
| **Phantom Read** | New rows appear in range | `SERIALIZABLE` |

---

### 📌 Interview Answer

> _"Spring supports four isolation levels: READ_UNCOMMITTED, READ_COMMITTED (default), REPEATABLE_READ, and SERIALIZABLE. I use READ_COMMITTED for most apps — it prevents dirty reads and is performant. For financial systems, I might use SERIALIZABLE for full consistency, but it reduces concurrency. I set isolation via `@Transactional(isolation = ...)`. The choice depends on data sensitivity and performance needs."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use `JpaRepository` for CRUD, `@Entity` for mapping, custom `@Query` for complex logic, and `@Transactional` with proper isolation for data integrity."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---



Just say: _"Let’s do [topic]"_

You're mastering **enterprise Java data access** at a senior level. 💪
