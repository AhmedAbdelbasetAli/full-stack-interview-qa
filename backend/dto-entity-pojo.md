# 🧱 DTO vs Entity vs POJO – When to Use Which?  
**Clear Guide for Clean, Maintainable Java Applications**

This document explains the **key differences** between **POJO**, **Entity**, and **DTO** — three fundamental concepts in Java backend development.

You’ll learn:
- ✅ What each is
- ✅ How they differ
- ✅ Real code examples
- ✅ Best practices
- ✅ When to use which
- ✅ Interview-ready answers

---

## 🔹 TL;DR Summary

| Type | Purpose | Used In | Mutability | Annotations |
|------|--------|--------|-----------|------------|
| **POJO** | Plain Java object — foundation | All layers | Any | None (pure) |
| **Entity** | Maps to a database table | Data layer (JPA/Hibernate) | Usually mutable | `@Entity`, `@Id`, `@Table` |
| **DTO** | Transfers data between layers or systems | API layer (request/response) | Often immutable | None or Lombok |

> ✅ **Rule of Thumb**:  
> - Use **Entity** for database  
> - Use **DTO** for API  
> - Use **POJO** as the base for both

---

## 1️⃣ What is a POJO?

> **POJO (Plain Old Java Object)** is a **simple Java class** with no framework dependencies.

It’s the **foundation** of all Java objects.

### ✅ POJO Example
```java
public class User {
    private Long id;
    private String name;
    private String email;

    // Constructors, getters, setters
    public User() {}

    public User(Long id, String name, String email) {
        this.id = id;
        this.name = name;
        this.email = email;
    }

    // Getters and setters...
}
```

### ✅ Characteristics
- No framework annotations
- No inheritance from special classes
- Just a clean, reusable Java class

---

## 2️⃣ What is an Entity?

> An **Entity** is a **POJO enhanced with JPA annotations** that maps to a **database table**.

It represents **persistent data**.

### ✅ Entity Example
```java
import javax.persistence.*;

@Entity
@Table(name = "users")
public class UserEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(unique = true)
    private String email;

    // Constructors, getters, setters
}
```

### ✅ Characteristics
- Used in **data layer** (repository)
- Managed by **JPA/Hibernate**
- Tied to **database schema**
- Often mutable (for JPA proxying)

---

## 3️⃣ What is a DTO?

> A **DTO (Data Transfer Object)** is a **simple object used to transfer data** between:
- ✅ Layers (service → controller)
- ✅ Systems (API request/response)
- ✅ Microservices

It **hides internal structure** and **controls what data is exposed**.

### ✅ DTO Example
```java
// Used in API response
public class UserResponseDto {
    private Long id;
    private String name;
    private String email;
    private String role; // derived, not in entity

    public UserResponseDto(Long id, String name, String email, String role) {
        this.id = id;
        this.name = email;
        this.email = email;
        this.role = role;
    }

    // Getters only (immutable)
}
```

### ✅ Characteristics
- Used in **API layer**
- No business logic
- Can include **computed fields** (e.g., `fullName`, `isAdmin`)
- Often **immutable** (getters only)

---

## 🔍 Key Differences

| Feature | **POJO** | **Entity** | **DTO** |
|--------|--------|----------|--------|
| **Purpose** | General-purpose object | Map to database | Transfer data |
| **Layer** | Any | Data (repository) | API (controller) |
| **Annotations** | None | `@Entity`, `@Id`, etc. | Usually none |
| **Mutability** | Any | Usually mutable | Often immutable |
| **Exposed via API?** | ❌ No | ❌ Never (security risk) | ✅ Yes |
| **Contains Sensitive Data?** | Maybe | Often (passwords, roles) | ❌ Never |
| **Used for Persistence?** | ❌ No | ✅ Yes | ❌ No |

---

## 🔄 How They Work Together

```text
[API Request] → UserCreateDto → Service → UserEntity ↔ Database
                                           ↓
                                   UserResponseDto ← [API Response]
```

### ✅ Example Flow

1. **Request**: `POST /users` with JSON → `UserCreateDto`
2. **Service**: Maps DTO to `UserEntity`, saves to DB
3. **Response**: Maps `UserEntity` to `UserResponseDto`, returns JSON

---

### ✅ Mapping Example
```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public UserResponseDto createUser(UserCreateDto createDto) {
        // Map DTO to Entity
        UserEntity user = new UserEntity();
        user.setName(createDto.getName());
        user.setEmail(createDto.getEmail());
        user.setPassword(hashPassword(createDto.getPassword()));

        UserEntity saved = userRepository.save(user);

        // Map Entity to Response DTO
        return new UserResponseDto(
            saved.getId(),
            saved.getName(),
            saved.getEmail(),
            "USER"
        );
    }
}
```

---

## ✅ Why Not Use Entity as DTO?

❌ **Never expose Entity directly in API!**

| Risk | Why |
|------|-----|
| ❌ **Security Risk** | Exposes sensitive fields (password, roles) |
| ❌ **Tight Coupling** | API tied to DB schema — can’t change DB without breaking API |
| ❌ **Serialization Issues** | Lazy loading exceptions, circular references |
| ❌ **Poor Control** | Can’t add computed fields or hide internal logic |

✅ Example:  
If your `UserEntity` has `password`, exposing it in JSON is a **major security flaw**.

---

## ✅ When to Use Which?

| Scenario | Choice |
|--------|--------|
| ✅ **Database Table Mapping** | `UserEntity` |
| ✅ **API Request Body** | `UserCreateDto` |
| ✅ **API Response** | `UserResponseDto` |
| ✅ **Internal Data Holder** | POJO (e.g., `ReportData`) |
| ✅ **Microservices Communication** | DTO |
| ✅ **Domain Logic** | Entity (or Domain Object in DDD) |

---

## ✅ Best Practices

| Rule | Why |
|------|-----|
| ✅ **Never expose Entity in API** | Security and decoupling |
| ✅ **Use separate DTOs for request and response** | Different needs (e.g., no password in response) |
| ✅ **Use immutability in DTOs** | Safer, thread-safe |
| ✅ **Use MapStruct or ModelMapper** | Avoid manual mapping |
| ✅ **Use Lombok** | Reduce boilerplate in POJOs/DTOs |

### ✅ With Lombok
```java
import lombok.Data;
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class UserResponseDto {
    private Long id;
    private String name;
    private String email;
    private String role;
}
```

✅ `@Data` = `@Getter` + `@Setter` + `toString` + `equals` + `hashCode`

---

## 📌 Interview Answer

> _"A POJO is a plain Java object — the base for everything. An Entity is a POJO with JPA annotations that maps to a database table. A DTO is a lightweight object used to transfer data, especially in APIs. I never expose Entities directly — I map them to DTOs to avoid security risks and decouple my API from the database. This makes the system secure, flexible, and maintainable."_  

---

## ✅ Final Tip

> 🎯 In interviews, **draw the flow**:
> ```
> [JSON] → DTO → Service → Entity → DB
> ```
> And say:  
> _"I keep layers separate: DTO for API, Entity for DB, and map between them."_  

That shows **clean architectural thinking**.

---

