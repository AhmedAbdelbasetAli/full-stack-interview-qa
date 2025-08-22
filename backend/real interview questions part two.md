---

### 🔹 Question 1:  
> **What is the difference between SOAP and REST? When would you use one over the other?**

#### ✅ Strong Answer:

| Feature | **SOAP** | **REST** |
|--------|---------|---------|
| **Protocol** | Strict protocol (uses HTTP as transport) | Architectural style (uses HTTP fully) |
| **Format** | XML only | JSON, XML, HTML, plain text |
| **Standards** | WS-Security, WS-ReliableMessaging, etc. | No enforced standards |
| **State** | Stateless by default | Stateless (recommended) |
| **Caching** | Not built-in | Supported via HTTP headers |
| **WSDL** | Yes — contract-first, tooling support | No — but OpenAPI/Swagger fills gap |
| **Firewall Friendly** | ❌ Often blocked (uses POST only) | ✅ Yes — uses standard HTTP methods |

---

### ✅ When to Use Which?

| Use Case | Recommended |
|--------|-------------|
| **Banking, healthcare, legacy systems** | ✅ **SOAP** — needs security, reliability, ACID-like guarantees |
| **Web/mobile apps, microservices, public APIs** | ✅ **REST** — lightweight, fast, JSON-friendly |
| **Internal system integration (WebSphere to mainframe)** | ✅ **SOAP** — strong contracts, tooling |
| **Real-time dashboards, mobile apps** | ✅ **REST** — fast, cacheable, scalable |

---

### ✅ Real Project Example:

We had a **payment gateway integration**:
- **SOAP** for **SWIFT transfers** (bank-to-bank) → needed **WS-Security**, **reliable messaging**
- **REST** for **mobile app balance checks** → fast, JSON, cacheable

---

### 💡 Why This Matters:
- **SOAP** = **enterprise-grade, secure, heavy**
- **REST** = **flexible, scalable, modern**

Choosing the right one depends on **requirements**, not trends.

---

---

### 🔹 Question 2:  
> **What are the HTTP methods? Explain `PUT` vs `PATCH`.**

#### ✅ Strong Answer:

The main HTTP methods:

| Method | Purpose | Idempotent? |
|-------|--------|------------|
| `GET` | Retrieve data | ✅ Yes |
| `POST` | Create a resource | ❌ No |
| `PUT` | Update/replace entire resource | ✅ Yes |
| `PATCH` | Partial update of resource | ❌ No (but should be) |
| `DELETE` | Remove resource | ✅ Yes |

---

### 🔁 `PUT` vs `PATCH` – Key Difference

| `PUT` | `PATCH` |
|------|--------|
| Replaces the **entire resource** | Updates **only specified fields** |
| Must send all fields (even unchanged) | Send only changed fields |
| Idempotent: calling 10 times = same result | Should be idempotent, but not guaranteed |
| Example: `PUT /users/123` with `{name: "John", age: 30}` | Example: `PATCH /users/123` with `{age: 31}` |

---

### ✅ Example:

```json
// Current user: { "id": 123, "name": "John", "age": 30, "email": "john@example.com" }
```

- `PUT /users/123`
  ```json
  { "name": "John", "age": 31 }  // Missing email → might wipe it!
  ```
  → Risk of **data loss** if server doesn't preserve missing fields

- `PATCH /users/123`
  ```json
  { "age": 31 }
  ```
  → Only updates `age` — safer

---

### ✅ Real Project Example:

We used:
- `PUT` for **profile updates** where frontend sends full object
- `PATCH` for **admin tools** where only one field changes (e.g., status)

Fixed a bug where `PUT` was clearing optional fields — switched to `PATCH` for partial edits.

---

### 💡 Pro Tip:
> Use `PATCH` for **partial updates**, `PUT` for **full replacements** — and validate on server side.

---

---

### 🔹 Question 3:  
> **How do you secure a REST API? (JWT, OAuth, Basic Auth)**

#### ✅ Strong Answer:

Security depends on **use case**, but here are the main methods:

---

### 🔐 1. **JWT (JSON Web Token)**
- Stateless token containing user info (claims)
- Signed (HMAC) or encrypted (RSA)
- Sent in `Authorization: Bearer <token>`
- Validated on every request

✅ Use: **Single-page apps, mobile apps, microservices**

```java
// In Spring Security
@PreAuthorize("hasRole('USER')")
@GetMapping("/profile")
public User getProfile() { ... }
```

---

### 🔐 2. **OAuth 2.0 / OpenID Connect**
- Delegated authorization (e.g., "Login with Google")
- Roles: Resource Owner, Client, Authorization Server, Resource Server
- Flows: Authorization Code (web), Implicit (SPA), Client Credentials (services)

✅ Use: **Third-party access, SSO, public APIs**

---

### 🔐 3. **Basic Auth**
- `Authorization: Basic base64(username:password)`
- Simple but **insecure over HTTP**
- Always use with **HTTPS**

✅ Use: **Internal APIs, scripts, legacy systems**

---

### ✅ Real Project Example:

We secured a **customer portal**:
- Login → OAuth 2.0 with Identity Provider (Okta)
- Access token → JWT
- APIs validated JWT using `spring-security-jwt`
- Admin APIs used **client credentials flow** for service-to-service auth

---

### 💡 Best Practices:
- ✅ Always use **HTTPS**
- ✅ Set short **token expiry** + refresh tokens
- ✅ Validate and **sanitize input** even after auth
- ✅ Use `spring-security` for role-based access (`@PreAuthorize`)

---

---

### 🔹 Question 4:  
> **How do you handle versioning in REST APIs?**

#### ✅ Strong Answer:

You **must version APIs** to avoid breaking clients when you change them.

---

### ✅ Versioning Strategies

| Method | Example | Pros | Cons |
|-------|--------|------|------|
| **URL Path** | `/api/v1/users` | Simple, visible, cacheable | Ugly, not truly RESTful |
| **Query Param** | `/api/users?version=1` | Easy to implement | Hard to cache, not standard |
| **Custom Header** | `X-API-Version: 1` | Clean URL | Hidden, hard to debug |
| **Accept Header** | `Accept: application/vnd.myapp.v1+json` | RESTful, flexible | Complex, less tooling support |

---

### ✅ Recommendation:
> Use **URL path versioning** — it’s **simple, clear, and widely adopted**.

```http
GET /api/v1/users
GET /api/v2/users  // with new fields
```

---

### ✅ Real Project Example:

We launched `v2` of our **product API**:
- Added `category` and `tags`
- Kept `v1` running for 6 months
- Redirected docs to `/api/v2/...`
- Used `@RequestMapping("/api/v1/users")` and `@RequestMapping("/api/v2/users")`

Used **feature toggles** to gradually migrate.

---

### 💡 Pro Tip:
> Deprecate old versions gracefully:
> - Return `Deprecation: true` header
> - Log usage
> - Announce sunsetting

---

---

### 🔹 Question 5:  
> **How do you validate input in a Spring REST controller?**

#### ✅ Strong Answer:

Use **Bean Validation (JSR-380)** with `@Valid` and annotations.

---

### ✅ Step-by-Step

1. **Add dependency**:
   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-validation</artifactId>
   </dependency>
   ```

2. **Annotate DTO**:
   ```java
   public class CreateUserRequest {
       @NotBlank(message = "Name is required")
       private String name;

       @Email(message = "Invalid email")
       private String email;

       @Min(18) @Max(100)
       private int age;

       // getters, setters
   }
   ```

3. **Use `@Valid` in controller**:
   ```java
   @PostMapping("/users")
   public ResponseEntity<User> createUser(@Valid @RequestBody CreateUserRequest request) {
       // if validation fails, Spring throws MethodArgumentNotValidException
   }
   ```

4. **Handle errors globally**:
   ```java
   @ControllerAdvice
   public class ValidationHandler {
       @ExceptionHandler(MethodArgumentNotValidException.class)
       public ResponseEntity<Map<String, String>> handle(…){
           // return field errors
       }
   }
   ```

---

### ✅ Real Project Example:

We had a **loan application API**:
- Used `@DecimalMin("1000.00")` for amount
- Custom validator `@ValidSSN` for social security number
- Returned structured error JSON:
  ```json
  { "email": "must be a valid email", "age": "must be between 18 and 100" }
  ```

---

### 💡 Pro Tip:
> Always validate **on server side** — never trust client input.

---

---

### 🔹 Question 6:  
> **What is HATEOAS? Have you used it?**

#### ✅ Strong Answer:

**HATEOAS** = **Hypermedia As The Engine Of Application State**

It means: **API responses include links** to related actions, so clients can **navigate without hardcoding URLs**.

---

### ✅ Example (without HATEOAS):
```json
{
  "id": 123,
  "name": "John"
}
```

Client must know:  
→ `GET /users/123/orders` to get orders

---

### ✅ With HATEOAS:
```json
{
  "id": 123,
  "name": "John",
  "_links": {
    "self": { "href": "/api/v1/users/123" },
    "orders": { "href": "/api/v1/users/123/orders" },
    "update": { "href": "/api/v1/users/123", "method": "PUT" }
  }
}
```

Now client can **discover** what to do next.

---

### ✅ Real Project Example:

We used **Spring HATEOAS** in an **internal admin API**:

```java
@Entity
public class User {
    @Id private Long id;
    private String name;
}

// Resource
public class UserResource extends RepresentationModel<UserResource> {
    public UserResource(User user) {
        add(linkTo(methodOn(UserController.class).getById(user.getId())).withSelfRel());
        add(linkTo(methodOn(OrderController.class).getOrders(user.getId())).withRel("orders"));
    }
}
```

---

### 💡 When to Use It?
- ✅ **Public APIs** where clients should be decoupled
- ✅ **Discoverable APIs** (e.g., API portals)
- ❌ Not needed for **simple internal APIs** or mobile apps

Many teams skip it due to complexity — but it’s **true REST**.

---

## ✅ Summary Table

| Topic | Key Takeaway |
|------|--------------|
| **SOAP vs REST** | SOAP = enterprise, secure; REST = modern, flexible |
| **PUT vs PATCH** | PUT = full replace; PATCH = partial update |
| **REST Security** | JWT (stateless), OAuth (delegated), Basic (simple) |
| **Versioning** | Prefer `/api/v1/...` — simple and clear |
| **Input Validation** | `@Valid` + `@NotBlank`, `@Email`, etc. + global exception handler |
| **HATEOAS** | Links in response → self-discoverable APIs — ideal but complex |

---


---

### 🔹 Question 1:  
> **Explain the JSF lifecycle in detail.**

#### ✅ Strong Answer:

JSF has a **well-defined 6-phase lifecycle** that runs on every HTTP request (whether initial view or postback). Understanding it is critical for debugging and state management.

---

### 🔄 The 6 Phases of JSF Lifecycle

| Phase | Purpose | Key Actions |
|------|--------|-----------|
| 1. **Restore View** | Reconstruct the component tree | If it's a postback, restore view from state; else create new |
| 2. **Apply Request Values** | Capture raw request data | Set component values (e.g., input fields), process events |
| 3. **Process Validations** | Validate input | Convert & validate values (e.g., `required`, `@Size`) |
| 4. **Update Model Values** | Sync UI with backing beans | Set values into managed beans |
| 5. **Invoke Application** | Handle action events | Call action methods (e.g., `action="#{userBean.save}"`) |
| 6. **Render Response** | Generate HTML | Render view for next page (could be same or redirect) |

---

### ✅ Real Project Example:

We had a **user registration form**:

```xhtml
<h:inputText value="#{userBean.name}" required="true"/>
<h:commandButton value="Save" action="#{userBean.save}"/>
```

**Flow**:
1. **Restore View**: Build form UI
2. **Apply Request Values**: Capture input text
3. **Process Validations**: Check `required`, convert types
4. **Update Model Values**: Set `userBean.name = "John"`
5. **Invoke Application**: Call `userBean.save()`
6. **Render Response**: Show success page

If validation fails → skip to **Render Response** with error messages.

---

### 💡 Key Insight:
- **Phases 2–5 are skipped** if it’s a `renderResponse` or `renderView` (e.g., navigation)
- **Partial state saving** (JSF 2+) reduces memory usage
- `f:ajax` triggers **partial lifecycle** (only relevant phases)

---

### ⚠️ Common Pitfall:
If you access `@ViewScoped` bean in **Phase 1 or 2**, it might not be initialized → `NullPointerException`

---

---

### 🔹 Question 2:  
> **What is a managed bean in JSF? How is it different from a Spring bean?**

#### ✅ Strong Answer:

### 🧠 What Is a Managed Bean?

A **JSF Managed Bean** is a **POJO** managed by the **JSF framework** — used to hold UI state and handle actions.

```java
@ManagedBean(name = "userBean")
@ViewScoped
public class UserBean {
    private String name;
    public String save() { ... }
}
```

Or with CDI:
```java
@Named("userBean")
@ViewScoped
public class UserBean { ... }
```

---

### 🔁 JSF Managed Bean vs Spring Bean

| Feature | JSF Managed Bean | Spring Bean |
|--------|------------------|-------------|
| **Managed by** | JSF or CDI container | Spring IoC container |
| **Scopes** | `@RequestScoped`, `@ViewScoped`, `@SessionScoped`, `@ApplicationScoped` | `@Scope("request")`, `@Scope("session")`, etc. |
| **Dependency Injection** | `@ManagedProperty`, `@Inject` | `@Autowired`, `@Qualifier` |
| **Use Case** | UI state, form handling | Business logic, services, repositories |
| **Lifecycle** | Tied to JSF lifecycle | Tied to Spring context |

---

### ✅ Real Project Example:

We had:
```java
// JSF Bean (UI Layer)
@Named("userView")
@ViewScoped
public class UserView {
    @Inject
    private UserService service; // ← Spring bean injected

    private User user;
    public String save() {
        service.save(user);
        return "success?faces-redirect=true";
    }
}
```

```java
// Spring Bean (Service Layer)
@Service
public class UserService {
    @Autowired
    private UserRepository repo;
}
```

✅ Clean separation: **JSF for UI**, **Spring for business logic**

---

### 💡 Pro Tip:
> Prefer **CDI (`@Named`)** over `@ManagedBean` — it’s part of Java EE standard and integrates better with Spring.

---

---

### 🔹 Question 3:  
> **How do you pass data between JSF pages?**

#### ✅ Strong Answer:

There are **several ways**, depending on **scope and use case**.

---

### ✅ Methods to Pass Data

| Method | Scope | Use Case |
|-------|------|---------|
| **Managed Property (`@ManagedProperty`)** | Any | Inject value into bean (e.g., from `faces-config.xml`) |
| **Flash Scope** | One redirect | Pass data to **next page only** (e.g., success message) |
| **Session Scope** | Entire session | User preferences, login info |
| **URL Parameters** | Request | `?id=123` → use `@ManagedProperty("#{param.id}")` |
| **Conversation Scope (CDI)** | Multi-step flow | Wizard, shopping cart |
| **View Scope (`@ViewScoped`)** | Same view | Retain data while user stays on page |

---

### ✅ Real Project Example:

We used **Flash Scope** for **success messages**:

```java
// In action method
public String save() {
    service.save(user);
    FacesContext.getCurrentInstance()
                .getExternalContext()
                .getFlash().put("message", "User saved successfully!");
    return "list?faces-redirect=true";
}
```

On target page:
```xhtml
<h:messages rendered="#{not empty flash.message}"/>
```

Also used **URL params**:
```java
@ManagedProperty("#{param.id}")
private Long id;
```

✅ Safe for navigation.

---

### 💡 Best Practice:
- Use **Flash** for messages after redirect
- Avoid **Session** for temporary data
- Use **ViewScoped** for AJAX-heavy forms

---

---

### 🔹 Question 4:  
> **What is `h:commandButton` vs `h:commandLink`? How do they trigger actions?**

#### ✅ Strong Answer:

Both are **JSF UI components** that submit forms and trigger **action methods** — but they render differently.

---

### 🔹 `h:commandButton`

```xhtml
<h:commandButton value="Save" action="#{userBean.save}" />
```

- Renders as an **HTML `<button>` or `<input type="submit">`**
- Looks like a **button**
- Default behavior: **submit form via POST**

---

### 🔹 `h:commandLink`

```xhtml
<h:commandLink action="#{userBean.delete}">
    <h:outputText value="Delete"/>
</h:commandLink>
```

- Renders as an **HTML `<a>` tag with JavaScript to submit form**
- Looks like a **hyperlink**
- Underlying: `onclick="document.forms[0].submit()"`

---

### 🔁 How Actions Are Triggered

1. User clicks button/link
2. Form is submitted (POST)
3. JSF lifecycle runs
4. In **Invoke Application** phase, calls:
   ```java
   userBean.save()  // returns String (navigation case)
   ```
5. Returns outcome (e.g., `"success"`), mapped to a page

---

### ✅ Real Project Example:

We used:
- `h:commandButton` for **Save, Cancel** (standard actions)
- `h:commandLink` for **Delete** in table (looks like link)
- Added `onclick="return confirm('Sure?')"` for safety

Also used `f:ajax`:
```xhtml
<h:commandButton value="Refresh">
    <f:ajax execute="@form" render="@form" />
</h:commandButton>
```

✅ Prevents full page reload.

---

### 💡 Pro Tip:
> Use `h:button` and `h:link` (JSF 2+) for **GET navigation** — they don’t submit forms.

---

---

### 🔹 Question 5:  
> **How do you integrate Spring beans into JSF managed beans?**

#### ✅ Strong Answer:

You **inject Spring beans (e.g., `@Service`, `@Repository`) into JSF managed beans** using **Spring’s JSF integration**.

---

### ✅ Step-by-Step Integration

#### 1. Add Dependency
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
</dependency>
```

#### 2. Configure `ContextLoaderListener` in `web.xml`
```xml
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/spring-context.xml</param-value>
</context-param>
```

#### 3. Register `SpringBeanFacesELResolver`
```xml
<faces-config>
    <application>
        <el-resolver>org.springframework.web.jsf.el.SpringBeanFacesELResolver</el-resolver>
    </application>
</faces-config>
```

#### 4. Inject Spring Bean into JSF Bean
```java
@Named("userBean")
@ViewScoped
public class UserBean {

    @Inject  // or @Autowired
    private UserService userService;

    public String save() {
        userService.save(user);
        return "success";
    }
}
```

---

### ✅ Real Project Example:

In a **WebSphere-hosted JSF + Spring app**, we:
- Used `SpringBeanFacesELResolver` to inject `@Service` beans
- Kept transaction management in Spring (`@Transactional`)
- Used JSF for UI, Spring for business logic and DB access

✅ Clean separation of concerns.

---

### 💡 Alternative: `@Configurable` (AspectJ)
```java
@Configurable
@ViewScoped
public class UserBean {
    @Autowired
    private UserService service;
}
```

Requires **AspectJ weaving** — less common.

---

### ✅ Best Practice:
> Use **CDI (`@Named`) + `SpringBeanFacesELResolver`** — it’s clean, standard, and works well in WebSphere.

---

## ✅ Summary Table

| Topic | Key Takeaway |
|------|--------------|
| **JSF Lifecycle** | 6 phases — critical for debugging, validation, and AJAX |
| **Managed Bean vs Spring Bean** | JSF: UI state; Spring: business logic — integrate via `SpringBeanFacesELResolver` |
| **Passing Data** | Flash → redirect; Session → global; URL params → navigation |
| **h:commandButton vs h:commandLink** | Button = form submit; Link = link that submits — both trigger POST |
| **Spring + JSF Integration** | Use `SpringBeanFacesELResolver` to inject Spring services into JSF beans |

---



---

### 🔹 Question 1:  
> **What is a stored procedure vs function? Can a function be used in a SELECT statement?**

#### ✅ Strong Answer:

| Feature | **Stored Procedure** | **Function** |
|--------|------------------------|-------------|
| **Purpose** | Perform actions (DML, logic, etc.) | Return a value (scalar or table) |
| **Return Value** | Optional (via `OUT` parameters) | **Mandatory** — one return value |
| **Called In** | `EXEC`, `CALL`, or from code | `SELECT`, `WHERE`, `PL/SQL` blocks |
| **DML Allowed** | ✅ Yes (`INSERT`, `UPDATE`, `DELETE`) | ✅ Yes (but not in SQL contexts) |
| **Use in SELECT** | ❌ No | ✅ Yes (if schema-level, pure) |

---

### ✅ Example

#### 📌 Function (Can be used in `SELECT`)
```sql
CREATE OR REPLACE FUNCTION get_employee_age(emp_id NUMBER)
RETURN NUMBER
IS
    age NUMBER;
BEGIN
    SELECT FLOOR(MONTHS_BETWEEN(SYSDATE, hire_date)/12)
    INTO age
    FROM employees
    WHERE id = emp_id;
    
    RETURN age;
END;
/
```

✅ Can be used in `SELECT`:
```sql
SELECT name, get_employee_age(id) AS age FROM employees;
```

#### 📌 Procedure (Cannot be used in `SELECT`)
```sql
CREATE OR REPLACE PROCEDURE update_salary(emp_id NUMBER, new_salary NUMBER)
IS
BEGIN
    UPDATE employees SET salary = new_salary WHERE id = emp_id;
    COMMIT;
END;
/
```

❌ Cannot be used in `SELECT`.

Call via:
```sql
EXEC update_salary(101, 75000);
-- or
BEGIN
    update_salary(101, 75000);
END;
```

---

### ✅ Real Project Example:

We used:
- **Functions** in `SELECT` for **computed fields** in Jasper Reports (e.g., `calculate_tax(amount)`)
- **Procedures** for **batch updates** and **ETL jobs**

---

### 💡 Pro Tip:
> Use **functions** when you need to **compute values in queries**  
> Use **procedures** for **business logic with side effects**

---

---

### 🔹 Question 2:  
> **Explain `CURSOR`, `REF CURSOR`, and when you’d use them.**

#### ✅ Strong Answer:

---

### 🧠 What Is a `CURSOR`?

A **cursor** is a pointer to a result set from a `SELECT` query.

#### **Implicit Cursor** (Auto-created)
```sql
BEGIN
    UPDATE employees SET salary = salary * 1.1 WHERE dept = 'IT';
    IF SQL%ROWCOUNT > 0 THEN
        DBMS_OUTPUT.PUT_LINE('Updated ' || SQL%ROWCOUNT || ' rows');
    END IF;
END;
```

#### **Explicit Cursor**
```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT id, name FROM employees WHERE salary > 50000;
    emp_id employees.id%TYPE;
    emp_name employees.name%TYPE;
BEGIN
    OPEN emp_cursor;
    LOOP
        FETCH emp_cursor INTO emp_id, emp_name;
        EXIT WHEN emp_cursor%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(emp_name);
    END LOOP;
    CLOSE emp_cursor;
END;
```

✅ Use: When you need **row-by-row processing** (e.g., validation, logging)

---

### 🧠 What Is a `REF CURSOR`?

A **`REF CURSOR`** is a **pointer to a cursor** that can be **returned from a function/procedure**.

```sql
TYPE emp_ref_cursor IS REF CURSOR RETURN employees%ROWTYPE;

CREATE OR REPLACE FUNCTION get_high_earners RETURN emp_ref_cursor
IS
    cur emp_ref_cursor;
BEGIN
    OPEN cur FOR SELECT * FROM employees WHERE salary > 100000;
    RETURN cur;
END;
/
```

Now, Java (or another PL/SQL block) can fetch from this cursor.

---

### ✅ Real Project Example:

We used `REF CURSOR` in a **Jasper Report**:
- Report called a function that returned `REF CURSOR`
- Jasper used it as a **data source** via JDBC
- Enabled **dynamic, parameterized reports**

Also used in **Spring JDBC**:
```java
SqlQuery sqlQuery = new SqlQuery() {
    protected ResultSetExtractor createResultSetExtractor() {
        return new ResultSetExtractor() {
            public Object extractData(ResultSet rs) { ... }
        };
    }
};
```

---

### 💡 When to Use:
- **`CURSOR`**: Row-by-row logic in PL/SQL
- **`REF CURSOR`**: Return result sets to **Java apps, reports, APIs**

---

---

### 🔹 Question 3:  
> **What is a trigger? Write a trigger that logs changes to a table.**

#### ✅ Strong Answer:

A **trigger** is a **PL/SQL block that automatically executes** in response to DML events (`INSERT`, `UPDATE`, `DELETE`) on a table.

---

### ✅ Use Cases:
- Audit logging
- Data validation
- Enforce business rules
- Sync with other systems

---

### ✅ Example: Audit Trigger

```sql
-- Audit table
CREATE TABLE emp_audit (
    log_id NUMBER GENERATED ALWAYS AS IDENTITY,
    emp_id NUMBER,
    action VARCHAR2(10), -- 'INSERT', 'UPDATE', 'DELETE'
    old_salary NUMBER,
    new_salary NUMBER,
    changed_by VARCHAR2(30),
    change_date TIMESTAMP
);

-- Trigger
CREATE OR REPLACE TRIGGER trg_emp_audit
    AFTER INSERT OR UPDATE OR DELETE ON employees
    FOR EACH ROW
BEGIN
    IF INSERTING THEN
        INSERT INTO emp_audit (emp_id, action, new_salary, changed_by, change_date)
        VALUES (:NEW.id, 'INSERT', :NEW.salary, USER, SYSTIMESTAMP);
        
    ELSIF UPDATING THEN
        INSERT INTO emp_audit (emp_id, action, old_salary, new_salary, changed_by, change_date)
        VALUES (:OLD.id, 'UPDATE', :OLD.salary, :NEW.salary, USER, SYSTIMESTAMP);
        
    ELSIF DELETING THEN
        INSERT INTO emp_audit (emp_id, action, old_salary, changed_by, change_date)
        VALUES (:OLD.id, 'DELETE', :OLD.salary, USER, SYSTIMESTAMP);
    END IF;
END;
/
```

---

### ✅ Real Project Example:

We used triggers to:
- Log **salary changes** for compliance
- Prevent **invalid updates** (e.g., negative salary)
- Sync **customer status** to a legacy system

⚠️ Used cautiously — triggers can make debugging hard.

---

### 💡 Best Practice:
- Keep triggers **simple and focused**
- Avoid **mutating table** errors (`ORA-04091`)
- Use `COMPOUND TRIGGER` for complex logic

---

---

### 🔹 Question 4:  
> **How do you optimize a slow SQL query?**

#### ✅ Strong Answer:

Optimization is a **systematic process** — not guesswork.

---

### ✅ Step-by-Step Optimization

#### 1. **Get Execution Plan**
```sql
EXPLAIN PLAN FOR
SELECT * FROM employees e JOIN departments d ON e.dept_id = d.id
WHERE e.salary > 50000;

SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
```

Look for:
- `FULL TABLE SCAN` on large tables → bad
- `NESTED LOOPS` vs `HASH JOIN`
- High `COST`, `CARDINALITY` estimates

---

#### 2. **Add Indexes**
```sql
-- Missing index on join or filter
CREATE INDEX idx_emp_salary ON employees(salary);
CREATE INDEX idx_emp_dept ON employees(dept_id);
```

✅ Use **composite indexes** for multiple filters

---

#### 3. **Rewrite Query**
- Avoid `SELECT *` → select only needed columns
- Replace subqueries with `JOIN`s
- Use `EXISTS` instead of `IN` for large sets

---

#### 4. **Use Hints (Carefully)**
```sql
SELECT /*+ INDEX(e idx_emp_salary) */ name, salary
FROM employees e
WHERE salary > 50000;
```

Only when optimizer chooses wrong plan.

---

#### 5. **Analyze Statistics**
```sql
EXEC DBMS_STATS.GATHER_TABLE_STATS('SCHEMA', 'EMPLOYEES');
```

Outdated stats → bad execution plans.

---

### ✅ Real Project Example:

A report took **5 minutes** — we:
1. Found `FULL SCAN` on 10M-row table
2. Added index on `status` and `created_date`
3. Rewrote subquery as `JOIN`
4. Reduced time to **2 seconds**

---

### 💡 Pro Tip:
> Use **AWR reports**, **SQL Trace**, and **TOAD/SQL Developer** for deep analysis.

---

---

### 🔹 Question 5:  
> **What is MERGE statement? How is it useful in ETL processes?**

#### ✅ Strong Answer:

`MERGE` (also called **UPSERT**) performs **INSERT or UPDATE in a single statement** based on whether a row exists.

---

### ✅ Syntax
```sql
MERGE INTO target_table t
USING source_table s
ON (t.id = s.id)
WHEN MATCHED THEN
    UPDATE SET t.name = s.name, t.salary = s.salary
    WHERE t.salary != s.salary
WHEN NOT MATCHED THEN
    INSERT (id, name, salary)
    VALUES (s.id, s.name, s.salary);
```

---

### ✅ Why It’s Powerful in ETL

In **data integration jobs**, you often:
- Load data from source (e.g., CSV, API)
- If record exists → **update**
- If new → **insert**

Without `MERGE`:
```sql
FOR EACH ROW:
    IF EXISTS → UPDATE
    ELSE → INSERT
```
→ **Row-by-row processing** → slow

With `MERGE`:
- **Set-based operation**
- **Atomic**
- **High performance**

---

### ✅ Real Project Example:

We used `MERGE` in a **daily customer sync job**:
- Source: External CRM (10K records)
- Target: Oracle `customers` table (1M rows)
- Used `MERGE` to **insert new customers** and **update changed ones**
- Reduced job time from **15 mins → 45 seconds**

Also used in **Jasper ETL** scripts.

---

### 💡 Best Practice:
- Index the `ON` clause columns
- Use `WHEN MATCHED ... WHERE` to avoid unnecessary updates
- Wrap in `BATCH` for large loads

---

## ✅ Summary Table

| Topic | Key Takeaway |
|------|--------------|
| **Procedure vs Function** | Function returns value, can be in `SELECT`; Procedure does actions |
| **CURSOR / REF CURSOR** | CURSOR for row-by-row; REF CURSOR to return result sets to apps |
| **Trigger** | Auto-run on DML; great for audit, but use carefully |
| **Query Optimization** | Execution plan → Indexes → Rewrite → Stats |
| **MERGE** | Upsert in one statement — essential for fast ETL |

---

