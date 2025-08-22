---

### 🔹 Question 1:  
> **How do you integrate Jasper Reports with Java?**  
> *(Tests: `JasperFillManager`, `JasperPrint`, `JRXxxxDatasource`)*

#### ✅ Strong Answer:

JasperReports is integrated into Java via its **APIs** — primarily using:
- `JasperCompileManager` → compile `.jrxml` to `.jasper`
- `JasperFillManager` → fill report with data
- `JasperPrint` → in-memory representation
- `JRXxxxDatasource` → data source (DB, collection, etc.)

---

### ✅ Step-by-Step Integration

#### 1. **Add Dependency (Maven)**
```xml
<dependency>
    <groupId>net.sf.jasperreports</groupId>
    <artifactId>jasperreports</artifactId>
    <version>6.20.0</version>
</dependency>
```

#### 2. **Compile Report (Once)**
```java
JasperCompileManager.compileReportToFile("src/main/resources/report.jrxml");
// Output: report.jasper
```

#### 3. **Fill Report with Data**
```java
// Data source (e.g., from DB or List)
JRDataSource dataSource = new JREmptyDataSource(); // or real data

// Parameters (optional)
Map<String, Object> parameters = new HashMap<>();
parameters.put("reportTitle", "Monthly Sales");

// Fill report
JasperPrint jasperPrint = JasperFillManager.fillReport(
    "report.jasper", 
    parameters, 
    dataSource
);
```

#### 4. **Export or View**
```java
JasperViewer.viewReport(jasperPrint, false); // Desktop
```

---

### ✅ Real Project Example:

In a **Spring + JSF app on WebSphere**, we:
- Pre-compiled `.jrxml` → `.jasper` at build time
- Used `JasperFillManager` in a **Spring service**
- Passed `JRBeanCollectionDataSource` from a `List<Sale>`
- Rendered PDF in browser

---

### 💡 Pro Tip:
> Always **pre-compile** `.jrxml` → `.jasper` in production — compiling on-the-fly is slow.

---

---

### 🔹 Question 2:  
> **Can you generate a report from a `List<Object>` instead of a database?**  
> *(Tests: `JRBeanCollectionDataSource` usage)*

#### ✅ Strong Answer:

✅ **Yes — absolutely.**  
Use `JRBeanCollectionDataSource` to generate reports from any `List<T>` of **POJOs**.

---

### ✅ How It Works

Your POJO must have **public getters**:

```java
public class Sale {
    private String product;
    private double amount;
    private LocalDate date;

    // Getters and setters
    public String getProduct() { return product; }
    public double getAmount() { return amount; }
    public LocalDate getDate() { return date; }
}
```

Then:

```java
List<Sale> sales = saleService.getSales(); // from DB or API

JRDataSource dataSource = new JRBeanCollectionDataSource(sales);

JasperPrint jasperPrint = JasperFillManager.fillReport(
    "sales_report.jasper",
    new HashMap<>(),
    dataSource
);
```

In **JasperSoft Studio** or `.jrxml`:
```xml
<textField>
    <textFieldExpression><![CDATA[$F{product}]]></textFieldExpression>
</textField>
<textField>
    <textFieldExpression><![CDATA[$F{amount}]]></textFieldExpression>
</textField>
```

✅ `$F{}` refers to **fields** from the bean.

---

### ✅ Real Project Example:

We generated **monthly invoices** from:
```java
List<InvoiceItem> items = cart.getItems();
```
→ Passed via `JRBeanCollectionDataSource`  
→ No DB query needed — used business objects directly

Also used in **offline reporting** (no DB connection).

---

### 💡 Pro Tip:
> You can even use `Map<String, Object>` as data source with `new JRMapCollectionDataSource(listOfMaps)`

---

---

### 🔹 Question 3:  
> **How do you pass parameters to a Jasper report?**  
> *(Tests: Dynamic reporting)*

#### ✅ Strong Answer:

Use **parameters** to make reports **dynamic** — e.g., titles, filters, logos.

---

### ✅ Step 1: Define in `.jrxml`
```xml
<parameter name="reportTitle" class="java.lang.String"/>
<parameter name="minAmount" class="java.lang.Double"/>
<parameter name="companyLogo" class="java.awt.Image"/>

<title>
    <textField>
        <textFieldExpression><![CDATA[$P{reportTitle}]]></textFieldExpression>
    </textField>
</title>
```

---

### ✅ Step 2: Pass from Java
```java
Map<String, Object> parameters = new HashMap<>();
parameters.put("reportTitle", "Q4 Sales Report");
parameters.put("minAmount", 1000.0);

// Image parameter
Image logo = ImageIO.read(new File("logo.png"));
parameters.put("companyLogo", logo);

JasperPrint jasperPrint = JasperFillManager.fillReport(
    "report.jasper",
    parameters,
    dataSource
);
```

---

### ✅ Real Project Example:

We used parameters for:
- **Dynamic titles** based on user input
- **Filtering** (e.g., `WHERE amount > $P{minAmount}`)
- **Company branding** (logos per client)
- **Date ranges** in financial reports

Also passed `Locale` for **internationalization**.

---

### 💡 Pro Tip:
> Use parameters in **SQL queries**:
> ```sql
> SELECT * FROM sales WHERE sale_date BETWEEN $P{startDate} AND $P{endDate}
> ```

---

---

### 🔹 Question 4:  
> **How do you export a Jasper report to PDF, Excel, or HTML?**  
> *(Tests: Export APIs — `JRPdfExporter`, etc.)*

#### ✅ Strong Answer:

Use **Jasper’s exporter APIs** (`JRPdfExporter`, `JRXlsExporter`, etc.) to export `JasperPrint` to various formats.

---

### ✅ Export Examples

#### 📄 PDF
```java
JRPdfExporter exporter = new JRPdfExporter();
exporter.setExporterInput(new SimpleExporterInput(jasperPrint));
exporter.setExporterOutput(new SimpleOutputStreamExporterOutput("report.pdf"));
exporter.exportReport();
```

#### 📊 Excel (XLS)
```java
JRXlsExporter exporter = new JRXlsExporter();
exporter.setExporterInput(new SimpleExporterInput(jasperPrint));
exporter.setExporterOutput(new SimpleOutputStreamExporterOutput("report.xls"));
exporter.exportReport();
```

#### 🌐 HTML
```java
JRHtmlExporter exporter = new JRHtmlExporter();
exporter.setExporterInput(new SimpleExporterInput(jasperPrint));
exporter.setExporterOutput(new SimpleWriterExporterOutput("report.html"));
exporter.exportReport();
```

#### 📊 Modern: XLSX (via `JRXlsxExporter`)
```java
JRXlsxExporter exporter = new JRXlsxExporter();
exporter.setConfiguration(new SimpleXlsxReportConfiguration());
```

---

### ✅ Real Project Example:

In a **WebSphere web app**, we:
- Generated **PDF invoices** for download
- Exported **Excel reports** for finance team
- Showed **HTML preview** in JSF page using `OutputStream`

Also used **Spring’s `ResponseEntity`** to stream PDF to browser:

```java
ByteArrayOutputStream baos = new ByteArrayOutputStream();
JRPdfExporter exporter = new JRPdfExporter();
exporter.setExporterInput(new SimpleExporterInput(print));
exporter.setExporterOutput(new SimpleOutputStreamExporterOutput(baos));
exporter.exportReport();

return ResponseEntity.ok()
    .header("Content-Disposition", "attachment; filename=report.pdf")
    .contentType(MediaType.APPLICATION_PDF)
    .body(baos.toByteArray());
```

---

### 💡 Pro Tip:
> Use `SimpleXxxReportConfiguration` to set:
> - Page size
> - Fonts
> - Sheet names (Excel)
> - Image handling

---

---

### 🔹 Question 5:  
> **Have you used subreports or charts in Jasper? Explain.**  
> *(Tests: Advanced reporting)*

#### ✅ Strong Answer:

Yes — **subreports** and **charts** are essential for **complex, professional reports**.

---

### ✅ Subreports

Use when:
- You need **drill-down** (e.g., order + order lines)
- Avoid **cartesian product** in SQL
- Modularize report design

#### Example: Sales Report with Line Items

**Main Report** (`sales_summary.jrxml`):
```xml
<subreport>
    <dataSourceExpression>
        <![CDATA[new net.sf.jasperreports.engine.data.JRBeanCollectionDataSource($F{lineItems})]]>
    </dataSourceExpression>
    <subreportExpression><![CDATA["line_items.jasper"]]></subreportExpression>
</subreport>
```

Pass data via **field or parameter**.

---

### ✅ Charts

Jasper supports:
- Bar, Pie, Line, Area, etc.
- Interactive in HTML
- Configurable via GUI (JSS) or XML

#### Example: Monthly Sales Chart
```xml
<chart>
    <chartType>Bar</chartType>
    <categoryDataset>
        <categorySeries>
            <seriesExpression><![CDATA["Sales"]]></seriesExpression>
            <categoryExpression><![CDATA[$F{month}]]></categoryExpression>
            <valueExpression><![CDATA[$F{amount}]]></valueExpression>
        </categorySeries>
    </categoryDataset>
</chart>
```

---

### ✅ Real Project Example:

We built a **financial dashboard** with:
- **Main report**: Summary by region
- **Subreport**: Detailed transactions (drill-down)
- **Pie chart**: Revenue by product category
- **Bar chart**: Monthly trend

Used in **executive reporting** — exported to PDF and PowerPoint.

Also used **crosstabs** for pivot-style analysis.

---

### 💡 Best Practice:
- Keep subreports **small and focused**
- Use **lazy loading** for performance
- Test charts in **HTML preview** first

---

## ✅ Summary Table

| Topic | Key Takeaway |
|------|--------------|
| **Jasper + Java** | Use `JasperFillManager`, `JasperPrint`, compile `.jrxml` → `.jasper` |
| **List<Object> as Data** | `JRBeanCollectionDataSource` — no DB needed |
| **Parameters** | Pass dynamic values (title, filters, images) via `$P{}` |
| **Export Formats** | `JRPdfExporter`, `JRXlsExporter`, `JRHtmlExporter` |
| **Subreports & Charts** | Essential for complex, professional reports |

---


---

### 🔹 Question 1:  
> **How do you deploy a WAR file in WebSphere?**  
> *(Tests: Deployment process — console vs scripts)*

#### ✅ Strong Answer:

You can deploy a WAR file in WebSphere using **GUI (Admin Console)** or **scripts (wsadmin)**.

---

### ✅ Method 1: Web Console (GUI)

1. Log in to **WebSphere Integrated Solutions Console**  
   `https://<host>:9043/ibm/console`

2. Go to:  
   **Applications → New Application → Install New Applications**

3. Upload your WAR file (e.g., `myapp.war`)

4. Follow wizard:
   - Choose **install as enterprise application**
   - Map to server (e.g., `server1`)
   - Set context root (e.g., `/myapp`)
   - Enable class reloading (dev only)

5. Click **Finish** → **Save to master configuration**

6. **Start** the application from **Application Details**

---

### ✅ Method 2: Script (wsadmin - Python/Jython)

```python
# Connect to server
connect('adminUser', 'password', 'localhost:8880')

# Install application
AdminApp.install('/path/to/myapp.war', 
    ['-appname', 'MyApp', 
     '-contextroot', '/myapp', 
     '-server', 'server1'])

# Save config
AdminConfig.save()

# Start application
appManager = AdminControl.queryNames('type=ApplicationManager,*')
AdminControl.invoke(appManager, 'startApplication', 'MyApp')
```

Run with:
```bash
wsadmin.sh -lang jython -f deploy.py
```

---

### ✅ Real Project Example:

In production, we used **Jenkins + wsadmin scripts** to:
- Deploy WARs automatically
- Promote builds from dev → test → prod
- Avoid human error

Also used **rolling updates** with clusters.

---

### 💡 Pro Tip:
> Always **save to master configuration** — otherwise changes are lost on restart.

---

---

### 🔹 Question 2:  
> **What is a datasource in WebSphere? How do you configure it?**  
> *(Tests: JNDI, connection pooling)*

#### ✅ Strong Answer:

A **datasource** is a **JNDI-bound connection pool** that provides database connections to applications — managed by WebSphere for **performance, security, and reliability**.

---

### ✅ How to Configure

1. **Install JDBC Provider**  
   - Go to: **Resources → JDBC → JDBC Providers**  
   - Choose: **Oracle JDBC Driver** (or DB2, SQL Server, etc.)  
   - Class path: `/opt/oracle/ojdbc8.jar`  
   - Implementation: `Connection pool data source`

2. **Create Datasource**  
   - Name: `MyAppDS`  
   - JNDI Name: `jdbc/MyAppDB` ← this is what your app uses  
   - Data store helper: `Oracle12`  
   - Component-managed authentication alias: set DB credentials

3. **Set Connection Pool Settings**  
   - Initial/Min connections: 5  
   - Max connections: 50  
   - Reap time, unused timeout, aged timeout → tune for load

4. **Test Connection** → Verify it works

---

### ✅ In Your Java App (Spring)

```java
@Value("${jdbc.jndi.name}") // "jdbc/MyAppDB"
private String jndiName;

@Bean
public DataSource dataSource() throws NamingException {
    JndiTemplate jndi = new JndiTemplate();
    return jndi.lookup(jndiName, DataSource.class);
}
```

Or in `web.xml`:
```xml
<resource-ref>
    <res-ref-name>jdbc/MyAppDB</res-ref-name>
    <jndi-name>jdbc/MyAppDB</jndi-name>
</resource-ref>
```

---

### ✅ Real Project Example:

We configured a **high-availability Oracle RAC datasource** with:
- **Multi-datasource** (failover across nodes)
- **SSL connection** to DB
- **Test connection on reserve** → catch stale connections

Also used **XA datasource** for distributed transactions.

---

### 💡 Why Use WebSphere Datasource?
- ✅ **Connection pooling** → reuse connections
- ✅ **Centralized management** → no passwords in code
- ✅ **Monitoring** via PMI
- ✅ **Security** → JAAS authentication

---

---

### 🔹 Question 3:  
> **How do you monitor application performance in WebSphere?**  
> *(Tests: PMI, logs, JVM monitoring)*

#### ✅ Strong Answer:

Use **three layers of monitoring**:

---

### ✅ 1. **Performance Monitoring Infrastructure (PMI)**

WebSphere’s built-in performance tracking.

#### Enable:
- Go to: **Servers → Server Types → WebSphere application servers → server1 → Performance Monitoring Infrastructure**
- Enable **modules**: `JVM`, `ServletSession`, `ConnectionPool`, `ThreadPools`

#### View:
- **Tivoli Performance Viewer** (in console): Real-time charts
- Or access via JMX

#### Key Metrics:
- Heap usage
- Thread pool utilization
- Servlet response time
- DB connection wait time

---

### ✅ 2. **Logs & Traces**

#### Log Files:
- `SystemOut.log` → app output
- `SystemErr.log` → errors
- `trace.log` → detailed traces (if enabled)

#### Enable Tracing:
```bash
com.ibm.ws.webcontainer.*=all=enabled
```

Use **Log Analyzer** or **grep** for patterns.

---

### ✅ 3. **JVM Monitoring**

Use:
- **JConsole** or **VisualVM** → connect via JMX
- Monitor:
  - Heap / Non-heap memory
  - GC frequency and duration
  - Thread count and states
  - Class loading

Also use **heap dumps** on `OutOfMemoryError`:
```bash
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/dumps
```

---

### ✅ Real Project Example:

We diagnosed a **memory leak** by:
1. Seeing heap grow in PMI
2. Taking heap dump via `wsadmin`
3. Analyzing in **Eclipse MAT** → found `static List` holding sessions
4. Fixed → reduced memory from 2GB → 500MB

Also set up **Grafana + Prometheus** (via JMX exporter) for dashboards.

---

### 💡 Pro Tip:
> Set up **alerts** for:
> - Heap > 80%
> - Thread pool full
> - DB connections exhausted

---

---

### 🔹 Question 4:  
> **What is classloader isolation in WebSphere? How do you handle ClassNotFoundException?**  
> *(Tests: Classloading hierarchy, parent-first vs parent-last)*

#### ✅ Strong Answer:

WebSphere uses a **hierarchical classloading model** — each app has its own classloader, with **isolation** to prevent conflicts.

---

### 🧠 Classloader Hierarchy

```
Bootstrap ClassLoader
  ↓
System ClassLoader
  ↓
WebSphere Extensions
  ↓
Application Server (server1)
  ↓
Enterprise Application (EAR)
  ↓
Web Module (WAR) ← Each can have its own classloader
```

---

### 🔁 Parent-First vs Parent-Last

| Mode | Behavior | Use Case |
|------|--------|---------|
| **Parent-First (default)** | Look in parent first → then app | Safe, avoids conflicts |
| **Parent-Last** | Look in app first → then parent | When you need newer version of a lib (e.g., Jackson, Spring) than WebSphere provides |

---

### ✅ How to Set

In `WEB-INF/ibm-web-bnd.xml`:
```xml
<web-app-bnd>
    <class-loader delegation="false"/> <!-- parent-last -->
</web-app-bnd>
```

Or in Admin Console:
- App → Manage Modules → Class loader settings

---

### ✅ Handle `ClassNotFoundException`

#### Common Causes:
- Missing JAR in `WEB-INF/lib`
- WebSphere provides old version of a library
- Classloader delegation conflict

#### Solutions:
1. **Add JAR to `WEB-INF/lib`**
2. **Switch to `parent-last`** if you need newer version
3. **Use `shared libraries`** for common dependencies
4. **Check classloader viewer** in console to see what’s loaded

---

### ✅ Real Project Example:

We had `ClassNotFoundException: javax.json.JsonObject`  
→ WebSphere 8.5 didn’t include JSON-P  
→ We:
- Added `javax.json-api.jar` to `WEB-INF/lib`
- Set `parent-last` → so our JAR loaded first

Also used **shared library** for `log4j` across apps.

---

### 💡 Pro Tip:
> Always **isolate apps** unless you need shared libs — prevents "it works on my server" issues.

---

---

### 🔹 Question 5:  
> **How do you enable SSL in WebSphere?**

#### ✅ Strong Answer:

Enable SSL (HTTPS) for secure communication.

---

### ✅ Step-by-Step

#### 1. **Create or Import SSL Certificate**
- Use **IKEYMAN** or **IBM Key Management Tool**
- Create **key store** (e.g., `NodeDefaultKeyStore`)
- Add **personal certificate** (self-signed or CA-signed)

#### 2. **Configure SSL in Admin Console**
- Go to: **Security → SSL certificate and key management**
- Set **key store** and **trust store**
- Define **SSL configurations** (default settings)

#### 3. **Enable HTTPS on Web Container**
- Go to: **Servers → server1 → HTTP Transport → HTTPS**
- Set port (e.g., `9043`)
- Select SSL configuration

#### 4. **Update Virtual Host**
- Map `*:9043` to default host

#### 5. **Update Application (web.xml)**
```xml
<security-constraint>
    <user-data-constraint>
        <transport-guarantee>CONFIDENTIAL</transport-guarantee>
    </user-data-constraint>
</security-constraint>
```
→ Forces HTTP → HTTPS redirect

---

### ✅ Real Project Example:

We enabled SSL for a **banking portal**:
- Used **CA-signed certificate** (DigiCert)
- Enforced **TLS 1.2 only**
- Redirected all HTTP → HTTPS
- Passed **security audit**

Also used **SSL for JDBC** to Oracle.

---

### 💡 Pro Tip:
> Use **IBM Global Security Kit (GSKit)** for advanced key management  
> Test with `openssl s_client -connect host:9043`

---

## ✅ Summary Table

| Topic | Key Takeaway |
|------|--------------|
| **WAR Deployment** | GUI or `wsadmin` — automate in production |
| **Datasource** | JNDI + connection pooling — central, secure, monitored |
| **Performance Monitoring** | PMI + Logs + JVM tools → proactive ops |
| **Classloader Isolation** | `parent-first` (safe), `parent-last` (flexible) — fix `ClassNotFoundException` |
| **SSL** | Key store → SSL config → HTTPS port → `CONFIDENTIAL` in `web.xml` |

---


---

### 🔹 Question 1:  
> **Walk me through a recent feature you implemented end-to-end (from DB to UI).**

#### ✅ Strong Answer:

**Feature**: *Real-time Order Status Dashboard for Customer Service Reps*

---

### 🔄 End-to-End Flow

#### 1. **Database (Oracle)**
- Created `ORDER_STATUS_LOG` table to track status changes
- Added index on `order_id` and `timestamp`
- Wrote PL/SQL trigger to log changes from `ORDERS` table

```sql
CREATE TRIGGER trg_order_status_log
    AFTER UPDATE OF status ON ORDERS
    FOR EACH ROW
BEGIN
    INSERT INTO ORDER_STATUS_LOG (order_id, old_status, new_status, changed_by, change_time)
    VALUES (:NEW.id, :OLD.status, :NEW.status, USER, SYSTIMESTAMP);
END;
```

---

#### 2. **Backend (Spring + Hibernate)**
- Created `OrderStatusService` to fetch logs:
```java
@Service
public class OrderStatusService {
    @Autowired
    private OrderStatusRepository repo;

    public List<OrderStatusLog> getStatusHistory(Long orderId) {
        return repo.findByOrderIdOrderByChangeTimeDesc(orderId);
    }
}
```

- Used `@Cacheable` to reduce DB load

---

#### 3. **Web Layer (JSF 2 + Spring Integration)**
- Built `OrderStatusBean` (ViewScoped):
```java
@Named("orderStatusBean")
@ViewScoped
public class OrderStatusBean implements Serializable {
    @Inject private OrderStatusService service;
    private List<OrderStatusLog> history;

    @PostConstruct
    public void init() {
        String orderId = FacesContext.getCurrentInstance()
                                     .getExternalContext()
                                     .getRequestParameterMap()
                                     .get("orderId");
        this.history = service.getStatusHistory(Long.valueOf(orderId));
    }
}
```

---

#### 4. **UI (XHTML + CSS)**
```xhtml
<h:dataTable value="#{orderStatusBean.history}" var="log">
    <h:column>#{log.oldStatus} → #{log.newStatus}</h:column>
    <h:column>#{log.changeTime}</h:column>
    <h:column>#{log.changedBy}</h:column>
</h:dataTable>
```

Added **auto-refresh** using `<f:ajax>` every 30 seconds.

---

#### 5. **Jasper Report (Bonus)**
- Created a printable version using Jasper:
  - `JRBeanCollectionDataSource(history)`
  - Exported to PDF via `JRPdfExporter`

---

#### ✅ Outcome:
- Reduced customer service calls by 40%
- Real-time visibility into order flow
- Reused backend service in REST API for mobile app

---

### 💡 Why This Matters:
- Shows **full-stack ownership**
- Uses **Spring + JSF + Oracle + Jasper**
- Addresses **performance** (`@Cacheable`), **UX** (auto-refresh), **reusability**

---

---

### 🔹 Question 2:  
> **How do you debug a memory leak in a Java application running on WebSphere?**

#### ✅ Strong Answer:

Memory leaks are **critical in production** — here’s my systematic approach.

---

### 🔍 Step-by-Step Diagnosis

#### 1. **Monitor via PMI (Performance Monitoring Infrastructure)**
- In WebSphere Console → **Tivoli Performance Viewer**
- Watch: **Heap usage**, **GC frequency**, **GC duration**
- If heap grows steadily → leak suspected

---

#### 2. **Enable Heap Dump on OOM**
In `server.xml` or JVM args:
```bash
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/dumps
```

Or trigger manually via `wsadmin`:
```python
AdminTask.generateHeapDump('[-processName server1]')
```

---

#### 3. **Analyze Heap Dump**
Use **Eclipse MAT (Memory Analyzer Tool)**:
- Open `.phd` or `.hprof` file
- Run **Leak Suspects Report**
- Look for:
  - `Histogram` → large number of one class
  - `Dominator Tree` → objects holding memory
  - `Path to GC Roots` → why objects aren’t collected

---

#### 4. **Common Culprits**
| Issue | How to Spot |
|------|-------------|
| `static List` holding objects | Large `ArrayList` in dominator tree |
| Unclosed resources (ResultSet, Stream) | `BufferedInputStream`, `Connection` in heap |
| JSF `@ViewScoped` beans not serialized | `ViewMap` growing |
| Caching without TTL | `ConcurrentHashMap` with 100K entries |

---

#### ✅ Real Project Example:

We had an **OutOfMemoryError** after 3 days.

1. Took heap dump
2. MAT showed 500K instances of `UserSession` in a `static Map`
3. Found: `SessionManager` was caching sessions but never removing expired ones
4. Fixed: Added `WeakHashMap` + cleanup job

✅ Memory stabilized.

---

### 💡 Pro Tip:
> Use **GC logs** to see patterns:
> ```bash
> -verbose:gc -Xloggc:gc.log -XX:+PrintGCDetails
> ```
> Long GC pauses → memory pressure.

---

---

### 🔹 Question 3:  
> **How do you ensure thread safety in a Spring singleton bean?**

#### ✅ Strong Answer:

Spring beans are **singleton by default** — so **thread safety is your responsibility**.

---

### ✅ Thread Safety = No Shared Mutable State

#### ❌ Bad: Mutable Instance Variables
```java
@Service
public class CounterService {
    private int count = 0; // ❌ Shared mutable state

    public int increment() {
        return ++count; // ❌ Not thread-safe
    }
}
```

Multiple threads → race condition → wrong count.

---

#### ✅ Good: Stateless or Synchronized

**Option 1: Stateless (Best)**
```java
@Service
public class UserService {
    // No instance variables
    public User findById(Long id) { ... } // Safe — reads/writes are atomic
}
```

✅ Most services are naturally stateless.

---

**Option 2: Use `synchronized`**
```java
@Service
public class CounterService {
    private int count = 0;

    public synchronized int increment() {
        return ++count;
    }
}
```

✅ Works, but can hurt performance.

---

**Option 3: Use `AtomicInteger`**
```java
@Service
public class CounterService {
    private final AtomicInteger count = new AtomicInteger(0);

    public int increment() {
        return count.incrementAndGet();
    }
}
```

✅ High performance, lock-free.

---

**Option 4: Use `ThreadLocal`**
```java
@Service
public class RequestContext {
    private static final ThreadLocal<User> currentUser = new ThreadLocal<>();

    public void setUser(User user) {
        currentUser.set(user);
    }

    public User getUser() {
        return currentUser.get();
    }
}
```

✅ Each thread has its own copy.

---

### ✅ Real Project Example:

We had a `ReportCacheService`:
- Used `ConcurrentHashMap<String, Report>` → thread-safe
- Added `synchronized` on rebuild logic
- Used `AtomicLong` for hit/miss counters

✅ Handled 1000+ RPS safely.

---

### 💡 Pro Tip:
> Prefer **stateless design** — it’s simpler, faster, and safer.

---

---

### 🔹 Question 4:  
> **You have a slow-running SQL query in production. How do you diagnose and fix it?**

#### ✅ Strong Answer:

This is a **critical production issue** — I follow a structured approach.

---

### 🔍 Step-by-Step

#### 1. **Identify the Query**
- Check **application logs** (enable SQL logging in Hibernate)
- Or use **WebSphere PMI** → JDBC connection pool wait time
- Or ask DBAs to find long-running queries

Example:
```sql
SELECT * FROM orders o 
JOIN customers c ON o.cust_id = c.id 
WHERE o.status = 'PENDING' AND c.region = 'EMEA';
```

---

#### 2. **Get Execution Plan**
```sql
EXPLAIN PLAN FOR <query>;
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
```

Look for:
- `FULL TABLE SCAN` on large tables
- High `COST`, `CARDINALITY` mismatch
- `NESTED LOOPS` vs `HASH JOIN`

---

#### 3. **Check Indexes**
- Are there indexes on:
  - `orders.status`?
  - `customers.region`?
  - `orders.cust_id` (FK)?
- Use `CREATE INDEX idx_orders_status ON orders(status);`

---

#### 4. **Rewrite Query**
- Avoid `SELECT *` → select only needed columns
- Use `EXISTS` instead of `IN` with subqueries
- Consider **materialized views** for aggregations

---

#### 5. **Collaborate with DBAs**
- Share execution plan
- Ask for stats update: `EXEC DBMS_STATS.GATHER_TABLE_STATS(...)`
- Discuss partitioning for large tables

---

#### ✅ Real Project Example:

Query took **45 seconds**.

1. Found `FULL SCAN` on 5M-row `orders` table
2. Added composite index: `CREATE INDEX idx_orders_status_cust ON orders(status, cust_id)`
3. Updated stats
4. Time reduced to **0.8 seconds**

Also added **query timeout** in Spring:
```java
@QueryHints(@QueryHint(name = "javax.persistence.query.timeout", value = "30000"))
```

---

### 💡 Pro Tip:
> Use **AWR reports** for historical query performance.

---

---

### 🔹 Question 5:  
> **How do you write unit and integration tests for a Spring + Hibernate + JSF app?**

#### ✅ Strong Answer:

Testing layered apps requires **different strategies per layer**.

---

### ✅ 1. Unit Tests (JUnit + Mockito)

Test services in isolation:

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    @InjectMocks
    private OrderService orderService;

    @Test
    void shouldCalculateTotal() {
        when(orderRepository.findById(1L)).thenReturn(new Order(100.0));

        double total = orderService.calculateTotal(1L);

        assertEquals(100.0, total);
    }
}
```

✅ Fast, isolated, no DB.

---

### ✅ 2. Integration Tests (SpringBootTest)

Test full context with DB:

```java
@SpringBootTest
@TestPropertySource(locations = "classpath:application-test.properties")
@Sql(scripts = "/test-data.sql", executionPhase = Sql.ExecutionPhase.BEFORE_TEST_METHOD)
class OrderIntegrationTest {

    @Autowired
    private OrderService orderService;

    @Test
    void shouldSaveOrder() {
        Order order = new Order("PENDING");
        Order saved = orderService.save(order);

        assertNotNull(saved.getId());
    }
}
```

Use **H2 in-memory DB** for speed.

---

### ✅ 3. JSF UI Tests

Hard to unit test — use **integration or end-to-end**:

#### Option 1: Arquillian (In-container)
```java
@RunWith(Arquillian.class)
public class UserBeanTest {
    @Inject
    private UserBean userBean;

    @Test
    public void shouldSaveUser() {
        userBean.setName("John");
        String outcome = userBean.save();
        assertEquals("success", outcome);
    }
}
```

#### Option 2: Selenium (End-to-End)
```java
@Test
void shouldDisplayUser() {
    driver.get("/user.xhtml");
    driver.findElement(By.id("name")).sendKeys("John");
    driver.findElement(By.id("saveBtn")).click();
    assertTrue(driver.getPageSource().contains("User saved"));
}
```

---

### ✅ Real Project Example:

We used:
- **JUnit + Mockito** for 80% of tests (fast feedback)
- **SpringBootTest + H2** for integration (CI pipeline)
- **Selenium** for critical user journeys (login, checkout)

Achieved **75% coverage** — focused on business logic.

---

### 💡 Pro Tip:
> Use `@DataJpaTest` for repository-only tests — faster than `@SpringBootTest`

---

## ✅ Summary Table

| Question | Key Takeaway |
|--------|--------------|
| **End-to-End Feature** | Show full ownership — DB → backend → UI → extras |
| **Memory Leak Debug** | PMI → Heap dump → MAT → Fix root cause |
| **Thread Safety** | Avoid mutable state; use `synchronized`, `Atomic`, or `ThreadLocal` |
| **Slow SQL** | Execution plan → Indexes → Rewrite → DBA collaboration |
| **Testing Strategy** | Unit (Mockito), Integration (SpringBootTest), UI (Selenium) |

---



---

### ✅ Question 1:  
> **"How do you manage configuration across dev, test, and prod environments?"**

#### 💡 Why Ask This?

To understand:
- Are they using **modern practices** (externalized config) or **hardcoded values**?
- Do they support **secure secrets management**?
- Is configuration **error-prone** or **automated**?

---

#### 🎯 What You’re Testing:
- **DevOps maturity**
- **Security practices**
- **Ease of deployment**

---

#### ✅ Strong Version to Ask:
> "I’ve worked on apps where configuration was managed via `application-dev.yml`, `application-prod.yml`, and environment variables. How does your team handle configuration across environments? Do you use tools like Spring Cloud Config, Consul, or cloud Parameter Store?"

---

#### 🔍 What Good Answers Sound Like:
- ✅ "We use **Spring Profiles** + **environment variables** in Docker/K8s"
- ✅ "Secrets are in **AWS SSM** or **Hashicorp Vault**"
- ✅ "Config is versioned and injected at runtime"

🚫 Red flags:
- ❌ "We change the `config.properties` file manually on each server"
- ❌ "DB passwords are in the code"

---

### 💡 Pro Insight:
> Configuration is **not just a detail** — it’s a **critical part of system reliability**.

---

---

### ✅ Question 2:  
> **"Do you use CI/CD pipelines? Which tools?"**

#### 💡 Why Ask This?

To assess:
- **Development velocity**
- **Automation level**
- **Team collaboration practices**

---

#### 🎯 What You’re Testing:
- Are they **modern** or **legacy-bound**?
- Do they value **automation** and **quality**?
- Is deployment a **nightmare** or a **button click**?

---

#### ✅ Strong Version to Ask:
> "In my last role, we used Jenkins with pipeline-as-code to build, test, and deploy to WebSphere. Are you using CI/CD here? Tools like Jenkins, GitLab CI, GitHub Actions, or ArgoCD?"

---

#### 🔍 What Good Answers Sound Like:
- ✅ "We use **GitLab CI** to run tests and deploy to OpenShift"
- ✅ "Jenkins pipelines with approval gates for prod"
- ✅ "Fully automated canary releases in Kubernetes"

🚫 Red flags:
- ❌ "We manually copy WAR files to the server"
- ❌ "We don’t run automated tests"
- ❌ "Deployments happen once a quarter"

---

### 💡 Pro Insight:
> A team **without CI/CD** is a team **fighting fires daily**.

---

---

### ✅ Question 3:  
> **"Is there a microservices roadmap, or are you staying monolithic?"**

#### 💡 Why Ask This?

To understand:
- **Architecture direction**
- **Tech stack evolution**
- **Your growth path** (will you work on new tech?)

---

#### 🎯 What You’re Testing:
- Are they **innovating** or **maintaining**?
- Will you be stuck in a **legacy monolith**?
- Is there **modernization** or **technical debt**?

---

#### ✅ Strong Version to Ask:
> "I’ve worked on both monolithic JSF apps and Spring Boot microservices. Is the team moving toward microservices, or is the current architecture expected to remain monolithic? If so, how are you handling scalability and team velocity?"

---

#### 🔍 What Good Answers Sound Like:
- ✅ "We’re gradually decomposing the monolith into services using Strangler Pattern"
- ✅ "We have 5 microservices in production, using Spring Cloud and Eureka"
- ✅ "We’re staying monolithic for now, but using modular design and feature flags"

🚫 Red flags:
- ❌ "We’ve been talking about microservices for 3 years but haven’t started"
- ❌ "We can’t move — everything depends on WebSphere and Oracle"

---

### 💡 Pro Insight:
> Monoliths aren’t bad — if they’re **well-structured**.  
> Microservices aren’t good — if they’re **poorly managed**.

You want **intentional architecture**, not hype.

---

---

### ✅ Question 4:  
> **"How do you handle logging and monitoring in production?"**

#### 💡 Why Ask This?

To assess:
- **Operational excellence**
- **Incident response**
- **Visibility into system health**

---

#### 🎯 What You’re Testing:
- Can you **debug issues quickly**?
- Is production a **black box**?
- Do they care about **SLOs, alerts, observability**?

---

#### ✅ Strong Version to Ask:
> "In production, I’ve used centralized logging with ELK and monitoring with Prometheus/Grafana. How does your team monitor applications? Do you use tools like Splunk, Datadog, or custom dashboards?"

---

#### 🔍 What Good Answers Sound Like:
- ✅ "Logs go to **Splunk**, metrics to **Prometheus**, alerts via **PagerDuty**"
- ✅ "We use **Micrometer** + **Grafana** for JVM and business metrics"
- ✅ "We have dashboards for error rates, latency, and DB performance"

🚫 Red flags:
- ❌ "We check `SystemOut.log` when something breaks"
- ❌ "No monitoring — we wait for users to report issues"
- ❌ "We don’t have access to production logs"

---

### 💡 Pro Insight:
> **You can’t improve what you can’t measure.**  
> A team without monitoring is flying blind.

---

## ✅ Summary: Why These Questions Matter

| Question | What It Reveals |
|--------|-----------------|
| **Config Management** | DevOps maturity, security, automation |
| **CI/CD** | Development speed, quality, deployment safety |
| **Microservices Roadmap** | Innovation, tech debt, future-proofing |
| **Logging & Monitoring** | Operational excellence, incident response, observability |

---

