# 🏗️ Design a Clean REST API from Scratch  
**Step-by-Step: Requirements → Design → Implementation**

Let’s design a **clean, production-ready REST API** for a **Task Management System** — like a lightweight Trello or Todoist.

We’ll go from **requirements** to **clean architecture**, covering:
- ✅ RESTful design
- ✅ Clean endpoints
- ✅ Error handling
- ✅ Validation
- ✅ Security
- ✅ Code structure

---

## 🎯 Step 1: Define Requirements

### Core Features
1. Users can **create, read, update, delete tasks**
2. Tasks have: title, description, status (TODO, IN_PROGRESS, DONE), due date
3. Users must be **authenticated**
4. Users can only access their own tasks
5. API should be **versioned**
6. Return clean **error responses**
7. Validate input

---

## 📐 Step 2: RESTful API Design

### Base URL
```
https://api.taskmanager.com/v1
```

### Endpoints

| Method | Endpoint | Description |
|-------|---------|-------------|
| `POST` | `/tasks` | Create a new task |
| `GET` | `/tasks` | List all tasks (with filtering) |
| `GET` | `/tasks/{id}` | Get a specific task |
| `PUT` | `/tasks/{id}` | Update a task |
| `DELETE` | `/tasks/{id}` | Delete a task |

---

### ✅ RESTful Design Principles Applied

| Principle | How We Apply It |
|--------|-----------------|
| ✅ **Nouns, not Verbs** | `/tasks`, not `/createTask` |
| ✅ **Plural Resources** | `/tasks`, not `/task` |
| ✅ **HTTP Methods** | `POST`, `GET`, `PUT`, `DELETE` |
| ✅ **Status Codes** | `201 Created`, `404 Not Found`, `400 Bad Request` |
| ✅ **Versioning** | `/v1/tasks` |
| ✅ **Filtering** | `/tasks?status=TODO&dueAfter=2025-04-01` |

---

## 🧱 Step 3: Clean Data Model

### Task Entity (Java)
```java
@Entity
@Table(name = "tasks")
public class Task {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String title;

    private String description;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private TaskStatus status = TaskStatus.TODO;

    @Column(name = "due_date")
    private LocalDate dueDate;

    @Column(name = "user_id", nullable = false)
    private Long userId;

    @Column(name = "created_at")
    private LocalDateTime createdAt = LocalDateTime.now();

    // Constructors, getters, setters
}

public enum TaskStatus {
    TODO, IN_PROGRESS, DONE
}
```

---

## 🔄 Step 4: Clean Service Layer

### TaskService.java
```java
@Service
@Transactional
public class TaskService {

    @Autowired
    private TaskRepository taskRepo;

    @Autowired
    private AuthenticationFacade auth; // Gets current user

    public Task createTask(TaskRequest request) {
        Task task = new Task();
        task.setTitle(request.getTitle());
        task.setDescription(request.getDescription());
        task.setDueDate(request.getDueDate());
        task.setUserId(auth.getCurrentUserId());
        return taskRepo.save(task);
    }

    public Page<Task> getTasks(TaskFilter filter, Pageable pageable) {
        // Custom query using JPA criteria or Spring Data JPA query methods
        return taskRepo.findByUserIdAndFilters(
            filter.getUserId(), 
            filter.getStatus(), 
            filter.getDueAfter(), 
            pageable
        );
    }

    public Task getTask(Long id) {
        Task task = taskRepo.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("Task not found: " + id));
        
        validateOwnership(task);
        return task;
    }

    public Task updateTask(Long id, TaskRequest request) {
        Task task = getTask(id); // Includes ownership check
        task.setTitle(request.getTitle());
        task.setDescription(request.getDescription());
        task.setStatus(request.getStatus());
        task.setDueDate(request.getDueDate());
        return taskRepo.save(task);
    }

    public void deleteTask(Long id) {
        Task task = getTask(id);
        taskRepo.delete(task);
    }

    private void validateOwnership(Task task) {
        if (!task.getUserId().equals(auth.getCurrentUserId())) {
            throw new AccessDeniedException("Not authorized to access this task");
        }
    }
}
```

✅ Key Clean Code Practices:
- ✅ Single responsibility
- ✅ No business logic in controller
- ✅ Reuse `getTask()` for ownership check
- ✅ Use `@Transactional` for consistency

---

## 🌐 Step 5: Clean REST Controller

### TaskController.java
```java
@RestController
@RequestMapping("/v1/tasks")
@Validated
public class TaskController {

    @Autowired
    private TaskService taskService;

    @PostMapping
    public ResponseEntity<Task> createTask(
        @Valid @RequestBody TaskRequest request
    ) {
        Task created = taskService.createTask(request);
        return ResponseEntity.status(201).body(created);
    }

    @GetMapping
    public ResponseEntity<Page<Task>> getTasks(
        @Valid TaskFilter filter,
        @PageableDefault(size = 10) Pageable pageable
    ) {
        Page<Task> tasks = taskService.getTasks(filter, pageable);
        return ResponseEntity.ok(tasks);
    }

    @GetMapping("/{id}")
    public ResponseEntity<Task> getTask(@PathVariable Long id) {
        Task task = taskService.getTask(id);
        return ResponseEntity.ok(task);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Task> updateTask(
        @PathVariable Long id,
        @Valid @RequestBody TaskRequest request
    ) {
        Task updated = taskService.updateTask(id, request);
        return ResponseEntity.ok(updated);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteTask(@PathVariable Long id) {
        taskService.deleteTask(id);
        return ResponseEntity.noContent().build();
    }
}
```

✅ Clean API Design:
- ✅ Versioned (`/v1`)
- ✅ Uses `@Valid` for automatic validation
- ✅ Returns appropriate HTTP status codes
- ✅ Clean, focused methods

---

## 📦 Step 6: Request & Response DTOs

### TaskRequest.java (Input)
```java
public class TaskRequest {
    @NotBlank(message = "Title is required")
    @Size(max = 100)
    private String title;

    @Size(max = 500)
    private String description;

    @NotNull
    private TaskStatus status;

    @FutureOrPresent(message = "Due date cannot be in the past")
    private LocalDate dueDate;

    // Getters and setters
}
```

### TaskFilter.java (Query Parameters)
```java
public class TaskFilter {
    private TaskStatus status;
    private LocalDate dueAfter;
    private LocalDate dueBefore;

    // Getters and setters
}
```

✅ Why DTOs?
- Decouple internal model from API
- Control what’s exposed
- Add validation

---

## 🔐 Step 7: Security & Authentication

### Use JWT for Stateless Auth

```java
// In SecurityConfig
http
    .authorizeHttpRequests(auth -> auth
        .requestMatchers("/v1/tasks/**").authenticated()
        .anyRequest().permitAll()
    )
    .addFilterBefore(new JwtFilter(), UsernamePasswordAuthenticationFilter.class);
```

### JWT Filter
```java
@Component
public class JwtFilter implements Filter {
    @Autowired private JwtUtil jwtUtil;

    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) {
        String token = extractToken((HttpServletRequest) req);
        if (token != null && jwtUtil.validate(token)) {
            Long userId = jwtUtil.extractUserId(token);
            SecurityContextHolder.getContext()
                .setAuthentication(new UsernamePasswordAuthenticationToken(userId, null, Collections.emptyList()));
        }
        chain.doFilter(req, res);
    }
}
```

✅ Users include `Authorization: Bearer <token>` in every request.

---

## 🛡️ Step 8: Global Exception Handling

### GlobalExceptionHandler.java
```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        ErrorResponse error = new ErrorResponse("NOT_FOUND", ex.getMessage());
        return ResponseEntity.status(404).body(error);
    }

    @ExceptionHandler(AccessDeniedException.class)
    public ResponseEntity<ErrorResponse> handleAccessDenied(AccessDeniedException ex) {
        ErrorResponse error = new ErrorResponse("FORBIDDEN", ex.getMessage());
        return ResponseEntity.status(403).body(error);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(err -> 
            errors.put(err.getField(), err.getDefaultMessage())
        );
        return ResponseEntity.status(400).body(errors);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(Exception ex) {
        ErrorResponse error = new ErrorResponse("INTERNAL_ERROR", "An unexpected error occurred");
        return ResponseEntity.status(500).body(error);
    }
}

// ErrorResponse.java
record ErrorResponse(String code, String message) {}
```

✅ Returns consistent JSON errors:
```json
{
  "code": "NOT_FOUND",
  "message": "Task not found: 999"
}
```

---

## 🧪 Step 9: Testing Strategy

### Test the Service Layer
```java
@WebMvcTest(TaskController.class)
class TaskControllerTest {

    @Autowired
    private MockMvc mvc;

    @MockBean
    private TaskService taskService;

    @Test
    void shouldCreateTask() throws Exception {
        TaskRequest request = new TaskRequest("Buy milk", null, TaskStatus.TODO, LocalDate.now());
        Task saved = new Task(1L, "Buy milk", null, TaskStatus.TODO, LocalDate.now(), 100L);

        when(taskService.createTask(any())).thenReturn(saved);

        mvc.perform(post("/v1/tasks")
                .contentType(MediaType.APPLICATION_JSON)
                .content(asJson(request)))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.title").value("Buy milk"));
    }
}
```

✅ Use `@WebMvcTest` for fast controller tests.

---

## 📁 Step 10: Clean Project Structure

```
src/
 └── main/
     └── java/
         └── com/
             └── taskmanager/
                 ├── TaskManagerApplication.java
                 ├── config/           → SecurityConfig, WebConfig
                 ├── controller/       → TaskController
                 ├── service/          → TaskService
                 ├── repository/       → TaskRepository
                 ├── model/            → Task, TaskStatus
                 ├── dto/              → TaskRequest, ErrorResponse
                 ├── exception/        → ResourceNotFoundException
                 └── security/         → JwtFilter, JwtUtil
```

✅ Clear separation of concerns.

---

## ✅ Final API in Action

### Create a Task
```bash
curl -X POST https://api.taskmanager.com/v1/tasks \
  -H "Authorization: Bearer xyz" \
  -H "Content-Type: application/json" \
  -d '{"title": "Buy milk", "status": "TODO", "dueDate": "2025-04-10"}'
```

✅ Response: `201 Created` + task object

### List Tasks
```bash
curl "https://api.taskmanager.com/v1/tasks?status=TODO"
```

✅ Response: `200 OK` + paginated list

---

## 🎯 Interview Answer (Summary)

> _"I design clean REST APIs by following REST principles: nouns, proper HTTP methods, and status codes. I version the API, use DTOs, validate input, and return structured errors. I separate concerns with controller, service, and repository layers. I secure it with JWT and handle exceptions globally. The result is a maintainable, scalable, and intuitive API."_  

---

You're mastering **clean API design** like a pro. 💪
