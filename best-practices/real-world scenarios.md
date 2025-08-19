# 🛠️ Real-World Scenarios Deep Dive  
**Refactoring, Configuration, and Clean Code Habits**

This document tackles **practical, real-world challenges** developers face — with **actionable solutions** and **proven patterns**.

Each section includes:
- ✅ Clear problem
- ✅ Step-by-step solution
- ✅ Code examples (Java)
- ✅ Best practices
- ✅ Interview-ready answers

---

## 1. How would you improve a long, messy function with many conditionals?

> A **long function with nested conditionals** is hard to read, test, and maintain.

It often violates:
- Single Responsibility Principle
- DRY (Don’t Repeat Yourself)
- High cohesion

---

### 🔹 1.1 Example: Messy Function

```java
public String processUserRequest(User user, String action, boolean isAdmin) {
    if (user == null) {
        return "User not found";
    }
    if (!user.isActive()) {
        return "User is inactive";
    }
    if ("delete".equals(action)) {
        if (!isAdmin) {
            return "Only admins can delete";
        }
        if (user.isProtected()) {
            return "Cannot delete protected user";
        }
        deleteUser(user);
        return "User deleted";
    } else if ("promote".equals(action)) {
        if (user.getRole() == Role.ADMIN) {
            return "Already admin";
        }
        if (!isAdmin) {
            return "Only admins can promote";
        }
        user.setRole(Role.ADMIN);
        saveUser(user);
        return "User promoted";
    } else {
        return "Invalid action";
    }
}
```

❌ Problems:
- 40+ lines
- Deep nesting
- Mixed concerns (validation, logic, error messages)
- Hard to test
- Hard to add new actions

---

### 🔹 1.2 Step-by-Step Refactoring

#### ✅ Step 1: Apply Guard Clauses (Early Returns)

```java
if (user == null) return "User not found";
if (!user.isActive()) return "User is inactive";
```

✅ Flattens the structure.

---

#### ✅ Step 2: Extract Validation Logic

```java
private String validateUser(User user) {
    if (user == null) return "User not found";
    if (!user.isActive()) return "User is inactive";
    return null; // No error
}
```

---

#### ✅ Step 3: Replace Conditional with Strategy Pattern

Create an interface for actions:

```java
interface UserAction {
    String execute(User user, boolean isAdmin);
    boolean supports(String action);
}
```

Implement strategies:

```java
@Component
public class DeleteAction implements UserAction {
    public boolean supports(String action) {
        return "delete".equals(action);
    }

    public String execute(User user, boolean isAdmin) {
        if (!isAdmin) return "Only admins can delete";
        if (user.isProtected()) return "Cannot delete protected user";
        deleteUser(user);
        return "User deleted";
    }
}

@Component
public class PromoteAction implements UserAction {
    public boolean supports(String action) {
        return "promote".equals(action);
    }

    public String execute(User user, boolean isAdmin) {
        if (user.getRole() == Role.ADMIN) return "Already admin";
        if (!isAdmin) return "Only admins can promote";
        user.setRole(Role.ADMIN);
        saveUser(user);
        return "User promoted";
    }
}
```

---

#### ✅ Step 4: Use a Handler (Spring Autowire All)

```java
@Service
public class UserActionHandler {

    private final List<UserAction> actions;

    public UserActionHandler(List<UserAction> actions) {
        this.actions = actions;
    }

    public String process(User user, String action, boolean isAdmin) {
        String validationError = validateUser(user);
        if (validationError != null) return validationError;

        return actions.stream()
            .filter(a -> a.supports(action))
            .findFirst()
            .map(a -> a.execute(user, isAdmin))
            .orElse("Invalid action");
    }
}
```

✅ Now:
- Main logic is 10 lines
- Easy to add new actions
- Each action is testable in isolation
- No conditionals in main method

---

### 📌 Interview Answer

> _"I improve long, conditional-heavy functions by using guard clauses to reduce nesting, extracting validation, and replacing conditionals with the Strategy Pattern. This makes the code modular, testable, and extensible. For example, I’d turn a giant `if-else` block into a registry of action handlers — each responsible for one operation. This follows Open/Closed and Single Responsibility principles."_  

---

## 2. How do you handle configuration and environment-specific values cleanly?

> Hardcoding values like DB URLs, API keys, or feature flags leads to:
- ❌ Bugs when deploying to different environments
- ❌ Security risks (secrets in code)
- ❌ Need to recompile for config changes

---

### 🔹 2.1 Best Practices

| Rule | How to Apply |
|------|-------------|
| ✅ **Never hardcode** | No `String dbUrl = "localhost:3306"` |
| ✅ **Use external config files** | `application-dev.yml`, `application-prod.yml` |
| ✅ **Use environment variables** | For secrets and cloud deployments |
| ✅ **Use Spring `@ConfigurationProperties`** | Type-safe config access |
| ✅ **Use feature flags** | Toggle features without redeploy |

---

### 🔹 2.2 Example: Clean Configuration in Spring Boot

#### 📁 `application.yml`
```yaml
app:
  jwt-secret: ${JWT_SECRET:default123}  # Use env var, fallback to default
  debug-mode: false

spring:
  datasource:
    url: ${DB_URL:jdbc:h2:mem:devdb}
    username: ${DB_USER:sa}
    password: ${DB_PASSWORD:}
```

#### 📄 `AppProperties.java`
```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private String jwtSecret;
    private boolean debugMode;
    // Getters and setters
}
```

#### 📄 `SecurityConfig.java`
```java
@Autowired
private AppProperties appProps;

@Bean
public JwtUtil jwtUtil() {
    return new JwtUtil(appProps.getJwtSecret());
}
```

✅ Benefits:
- Type-safe access
- IDE autocomplete
- Validation support
- Centralized config

---

### 🔹 2.3 For Feature Flags

Use **Spring Cloud Config**, **LaunchDarkly**, or simple properties:

```java
@Value("${features.new-login-flow:false}")
private boolean newLoginFlowEnabled;

public String login() {
    if (newLoginFlowEnabled) {
        return newLoginService.login();
    } else {
        return oldLoginService.login();
    }
}
```

✅ Deploy the code, then toggle the feature via config.

---

### 📌 Interview Answer

> _"I externalize configuration using `application.yml`, environment variables, and `@ConfigurationProperties` for type-safe access. I never hardcode values — especially secrets. I use Spring Profiles for environment-specific settings and feature flags to toggle behavior without redeploying. This makes the app portable, secure, and flexible."_  

---

## 3. What are some habits of developers who write clean code?

> Clean code isn’t just skill — it’s **daily habits**.

Here are **7 habits of developers who consistently write clean, maintainable code**.

---

### 🔹 3.1 Habit 1: **They Refactor Continuously (Boy Scout Rule)**

> _"Leave the code cleaner than you found it."_

- Rename variables when they’re unclear
- Extract methods from long functions
- Remove duplication as they see it
- Don’t wait for a “refactor sprint”

---

### 🔹 3.2 Habit 2: **They Write Code for Humans First**

> Code is **read 10x more than it’s written**.

- Use **meaningful names**: `userCount` vs `x`
- Write **self-documenting code**: avoid comments that explain "what"
- Structure code so it **tells a story**

---

### 🔹 3.3 Habit 3: **They Write Tests — and Use Them as Design Tools**

- Write tests **before or with** the code
- Use tests to **explore API design**
- If a test is hard to write, the code is probably too coupled

---

### 🔹 3.4 Habit 4: **They Use Automation Relentlessly**

- Linters (ESLint, Checkstyle) → catch issues early
- Formatters (Prettier, Black) → no style debates
- CI/CD → every commit is tested and checked

✅ They **don’t waste time on manual checks**.

---

### 🔹 3.5 Habit 5: **They Seek Feedback Early**

- Open PRs early — even if incomplete
- Ask: _"Is this the right approach?"_
- Learn from code reviews — don’t take them personally

---

### 🔹 3.6 Habit 6: **They Think About the Next Developer**

> _"Code is a conversation between programmers."_

- Would a new hire understand this?
- Is there a simpler way?
- Am I making the next change easier or harder?

---

### 🔹 3.7 Habit 7: **They Say No to Shortcuts (Mostly)**

- They avoid `// TODO` without a ticket
- They don’t check in broken tests
- They fix tech debt quickly

✅ But they **take intentional debt when needed** — and **track it**.

---

### 📌 Interview Answer

> _"Developers who write clean code treat it like a craft. They refactor continuously, write for humans, use automation, and write tests as a design tool. They care about the next person who reads their code. They don’t just ship — they ship well. I try to follow these habits every day."_  

---

## ✅ Final Tip

> 🎯 In interviews, **show self-awareness**:
> _"I’m not perfect — I’ve written messy code. But I’ve learned to refactor it, use tools, and ask for feedback. Now I write cleaner code by default."_  

That shows **growth and professionalism**.

---
