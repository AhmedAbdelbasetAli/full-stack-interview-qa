# 🏦 Banking System Design Problems & How Java Design Patterns Solve Them

In **banking applications**, systems must be **reliable**, **secure**, **extensible**, and **maintainable**. Java **design patterns** provide proven solutions to common challenges in financial software.

This document covers **real banking problems** and how **GoF (Gang of Four) design patterns** solve them with **practical Java examples**.

---

## 🔹 Key Banking Challenges

| Problem | Why It Matters |
|--------|---------------|
| ❌ **Complex Transaction Logic** | Funds transfer, fraud checks, audit |
| ❌ **Multiple Payment Methods** | Credit card, wire, ACH, crypto |
| ❌ **Security & Access Control** | Sensitive data, role-based access |
| ❌ **Audit Trail & Logging** | Compliance (SOX, GDPR) |
| ❌ **Extensibility** | Add new account types, services |
| ❌ **Error Handling & Rollback** | ACID compliance, transaction integrity |
| ❌ **High Availability** | 24/7 banking, no downtime |

---

## ✅ 1. Problem: Multiple Payment Methods  
**Solution: Strategy Pattern**

> Different payment methods (credit card, PayPal, wire) should be **interchangeable**.

### ❌ Without Strategy
```java
public class PaymentProcessor {
    public void processPayment(String method, double amount) {
        if ("credit_card".equals(method)) {
            // Complex credit card logic
        } else if ("paypal".equals(method)) {
            // PayPal logic
        } else if ("wire".equals(method)) {
            // Wire transfer logic
        }
    }
}
```

❌ Hard to extend  
❌ Violates **Open/Closed Principle**

### ✅ With Strategy Pattern
```java
// Strategy Interface
public interface PaymentStrategy {
    void pay(double amount);
}

// Concrete Strategies
public class CreditCardPayment implements PaymentStrategy {
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " via Credit Card");
        // Fraud check, encryption, gateway call
    }
}

public class WireTransferPayment implements PaymentStrategy {
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " via Wire Transfer");
        // SWIFT integration, compliance check
    }
}

// Context
public class Account {
    private PaymentStrategy paymentStrategy;

    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.paymentStrategy = strategy;
    }

    public void transfer(double amount) {
        paymentStrategy.pay(amount);
    }
}

// Usage
Account account = new Account();
account.setPaymentStrategy(new CreditCardPayment());
account.transfer(1000);

account.setPaymentStrategy(new WireTransferPayment());
account.transfer(5000);
```

✅ **Add new payment methods without changing existing code**  
✅ **Easy to test and maintain**

---

## ✅ 2. Problem: Complex Transaction with Audit & Logging  
**Solution: Command Pattern + Observer**

> A funds transfer should:
- ✅ Move money
- ✅ Log the transaction
- ✅ Notify user
- ✅ Support undo/redo (for debugging)

### ✅ Command Pattern
```java
// Command Interface
public interface TransactionCommand {
    void execute();
    void undo();
}

// Concrete Command
public class TransferCommand implements TransactionCommand {
    private Account from;
    private Account to;
    private double amount;
    private boolean executed = false;

    public TransferCommand(Account from, Account to, double amount) {
        this.from = from;
        this.to = to;
        this.amount = amount;
    }

    public void execute() {
        if (from.getBalance() < amount) {
            throw new InsufficientFundsException();
        }
        from.debit(amount);
        to.credit(amount);
        this.executed = true;
        System.out.println("Transferred $" + amount + " from " + from.getId() + " to " + to.getId());
    }

    public void undo() {
        if (executed) {
            to.debit(amount);
            from.credit(amount);
            this.executed = false;
            System.out.println("Reversed transfer of $" + amount);
        }
    }
}
```

### ✅ Observer Pattern (for Notifications)
```java
// Observer
public interface TransactionObserver {
    void onTransaction(TransactionEvent event);
}

public class EmailNotifier implements TransactionObserver {
    public void onTransaction(TransactionEvent event) {
        System.out.println("Email: Transaction of $" + event.getAmount() + " completed.");
    }
}

public class SMSNotifier implements TransactionObserver {
    public void onTransaction(TransactionEvent event) {
        System.out.println("SMS: Your transaction is complete.");
    }
}

// Observable
public class TransactionManager {
    private List<TransactionObserver> observers = new ArrayList<>();

    public void addObserver(TransactionObserver o) { observers.add(o); }

    public void executeCommand(TransactionCommand command) {
        command.execute();
        notifyObservers(new TransactionEvent(command));
    }

    private void notifyObservers(TransactionEvent event) {
        for (TransactionObserver o : observers) {
            o.onTransaction(event);
        }
    }
}
```

✅ **Decouples transaction logic from notifications**  
✅ **Supports audit trail and compliance**

---

## ✅ 3. Problem: Account Types (Savings, Checking, Loan)  
**Solution: Factory Method + Template Method**

> Different account types share common behavior but differ in rules.

### ✅ Factory Method (Create Accounts)
```java
public abstract class Account {
    protected String accountId;
    protected double balance;

    public abstract void withdraw(double amount);
    public abstract void deposit(double amount);

    public void statement() {
        System.out.println("Account: " + accountId + ", Balance: $" + balance);
    }
}

public class SavingsAccount extends Account {
    public void withdraw(double amount) {
        if (balance - amount < 100) { // Min balance $100
            throw new IllegalArgumentException("Min balance violation");
        }
        balance -= amount;
    }

    public void deposit(double amount) {
        balance += amount;
    }
}

public class CheckingAccount extends Account {
    public void withdraw(double amount) {
        balance -= amount; // Overdraft allowed
    }

    public void deposit(double amount) {
        balance += amount;
    }
}

// Factory
public abstract class AccountFactory {
    public abstract Account createAccount(String id);
}

public class SavingsAccountFactory extends AccountFactory {
    public Account createAccount(String id) {
        SavingsAccount acc = new SavingsAccount();
        acc.accountId = id;
        acc.balance = 0;
        return acc;
    }
}

// Usage
AccountFactory factory = new SavingsAccountFactory();
Account savings = factory.createAccount("SAV-123");
```

### ✅ Template Method (Shared Workflow)
```java
public abstract class LoanApprovalProcess {
    // Template method
    public final void approveLoan(LoanApplication app) {
        verifyIdentity(app);
        checkCreditScore(app);
        assessRisk(app);
        approveOrReject(app);
    }

    protected abstract void verifyIdentity(LoanApplication app);
    protected abstract void checkCreditScore(LoanApplication app);
    protected void assessRisk(LoanApplication app) {
        System.out.println("Assessing risk for loan: " + app.getAmount());
    }
    protected abstract void approveOrReject(LoanApplication app);
}
```

✅ **Enforces business rules**  
✅ **Reduces code duplication**

---

## ✅ 4. Problem: Security & Access Control  
**Solution: Proxy Pattern**

> Prevent unauthorized access to sensitive operations.

```java
public interface AccountService {
    void transfer(String from, String to, double amount);
    Account getAccount(String id);
}

public class RealAccountService implements AccountService {
    public void transfer(String from, String to, double amount) {
        System.out.println("Transferring $" + amount + " from " + from + " to " + to);
        // Actual logic
    }

    public Account getAccount(String id) {
        return new Account(); // Load from DB
    }
}

// Proxy adds security
public class SecureAccountService implements AccountService {
    private RealAccountService realService = new RealAccountService();
    private String currentUserRole;

    public SecureAccountService(String role) {
        this.currentUserRole = role;
    }

    public void transfer(String from, String to, double amount) {
        if (!"ADMIN".equals(currentUserRole) && !"USER".equals(currentUserRole)) {
            throw new SecurityException("Unauthorized");
        }
        if (amount > 10000 && !"ADMIN".equals(currentUserRole)) {
            throw new SecurityException("High-value transfer requires admin");
        }
        realService.transfer(from, to, amount);
    }

    public Account getAccount(String id) {
        if (!"ADMIN".equals(currentUserRole)) {
            throw new SecurityException("Access denied");
        }
        return realService.getAccount(id);
    }
}
```

✅ **Transparent security layer**  
✅ **Centralized access control**

---

## ✅ 5. Problem: Reporting & Consolidation  
**Solution: Composite Pattern**

> A bank has branches, each with accounts. Generate consolidated reports.

```java
public interface Reportable {
    double getBalance();
    String getName();
}

public class Account implements Reportable {
    private String name;
    private double balance;

    public Account(String name, double balance) {
        this.name = name;
        this.balance = balance;
    }

    public double getBalance() { return balance; }
    public String getName() { return name; }
}

public class Branch implements Reportable {
    private String name;
    private List<Reportable> children = new ArrayList<>();

    public Branch(String name) {
        this.name = name;
    }

    public void add(Reportable component) {
        children.add(component);
    }

    public double getBalance() {
        return children.stream().mapToDouble(Reportable::getBalance).sum();
    }

    public String getName() {
        return name;
    }
}

// Usage
Branch main = new Branch("Main Branch");
main.add(new Account("Alice", 5000));
main.add(new Account("Bob", 3000));

Branch downtown = new Branch("Downtown");
downtown.add(new Account("Charlie", 7000));
main.add(downtown);

System.out.println("Total Balance: $" + main.getBalance()); // $15,000
```

✅ **Treat individual and group objects uniformly**  
✅ **Recursive structure for reporting**

---

## ✅ 6. Problem: Configuration & Global Settings  
**Solution: Singleton Pattern**

> One instance of configuration service.

```java
public enum BankConfig {
    INSTANCE;

    private String currency;
    private double interestRate;

    BankConfig() {
        this.currency = "USD";
        this.interestRate = 0.02;
    }

    public String getCurrency() { return currency; }
    public double getInterestRate() { return interestRate; }
}

// Usage
String currency = BankConfig.INSTANCE.getCurrency();
```

✅ **One instance, thread-safe, lazy-loaded**

---

## 📌 Interview Answer

> _"In banking, I use design patterns to solve real problems: Strategy for multiple payment methods, Command for transaction rollback, Observer for audit and notifications, Factory for account creation, and Proxy for security. These patterns make the system extensible, secure, and maintainable — critical for financial software."_  

---

## ✅ Final Tip

> 🎯 In interviews, **tie patterns to banking needs**:
> _"I use Command Pattern because financial transactions must be auditable and reversible. I use Proxy to enforce role-based access — tellers can't approve large transfers."_  

That shows **domain-specific design thinking**.

---


You're mastering **banking system design** like a pro. 💪
