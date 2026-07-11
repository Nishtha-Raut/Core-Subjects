# LEC-12: Transaction

## 1. Transaction
- A **unit of work** done against the DB in a logical sequence.
- **Sequence is very important** in a transaction.
- It is a logical unit of work that contains **one or more SQL statements**. The result of all these statements in a transaction either:
  - gets **completed successfully** (all changes made to the DB are permanent), **or**
  - if at any point a **failure** happens, it gets **rolled back** (all changes being done are undone).

## 2. ACID Properties
- To ensure integrity of the data, we require that the DB system maintain the following properties of a transaction.

### a. Atomicity
- Either **all** operations of a transaction are reflected properly in the DB, or **none** are.

### b. Consistency
- Integrity constraints must be maintained **before and after** a transaction.
- The DB must be consistent after a transaction happens.

### c. Isolation
- Even though multiple transactions may execute concurrently, the system guarantees that, for every pair of transactions `Ti` and `Tj`, it appears to `Ti` that either `Tj` finished execution before `Ti` started, or `Tj` started execution after `Ti` finished.
- Thus, each transaction is **unaware** of other transactions executing concurrently in the system.
- Multiple transactions can happen in the system **in isolation**, without interfering with each other.

### d. Durability
- After a transaction completes successfully, the changes it has made to the database **persist**, even if there are **system failures**.

> **Extra — Quick ACID mnemonic recap for interviews:**

| Property | One-line meaning |
|---|---|
| Atomicity | All-or-nothing execution |
| Consistency | DB moves from one valid state to another valid state |
| Isolation | Concurrent transactions don't interfere with each other |
| Durability | Once committed, changes survive crashes/failures |

## 3. Transaction States

```
                          Permanent
                            store
Active state ---R/W ops---> Partially committed state --------> Committed state
     |                              |                                  |
   Failure                       Failure                               |
     |                              |                                  |
     v                              v                                  |
Failed state ------Roll back---> Aborted state -------------------> Terminated state
```

### a. Active State
- The **very first state** of the life cycle of a transaction — all the read and write operations are being performed.
- If they execute without any error, the transaction (T) comes to the **Partially committed** state.
- If any error occurs, it leads to a **Failed** state.

### b. Partially Committed State
- After the transaction is executed, the changes are saved in the **buffer** in main memory.
- If the changes made are made **permanent** on the DB, the state transfers to the **Committed** state.
- If there is any failure, T will go to the **Failed** state.

### c. Committed State
- When updates are made **permanent** on the DB, T is said to be in the committed state.
- **Rollback can't be done** from the committed state.
- A new **consistent state** is achieved at this stage.

### d. Failed State
- When T is being executed and some failure occurs. Due to this, it is **impossible to continue** the execution of T.

### e. Aborted State
- When T reaches the failed state, all the changes made in the buffer are **reversed**.
- After that, T **rollbacks completely**. T reaches the abort state after rollback.
- The DB's state prior to T is achieved (restored).

### f. Terminated State
- A transaction is said to have **terminated** if it has either **committed** or **aborted**.

> **Extra — Why "Partially Committed" is a distinct state:** It exists because writing to the actual database (permanent storage) is a separate, potentially failure-prone step from executing the transaction's logic in memory/buffer. A transaction can complete all its logical operations successfully (partially committed) but still fail before those changes are flushed to disk — this is exactly the gap that Log-based recovery / Shadow-copy schemes (covered in LEC-13) are designed to protect against.

---

## Interview Questions & Answers

**Q1. What is a Transaction in DBMS?**
A transaction is a logical unit of work performed against a database, consisting of one or more SQL statements, that either executes completely and successfully (committing all changes) or fails entirely and is rolled back (undoing all partial changes) — ensuring the database never ends up in a partially-updated, inconsistent state.

**Q2. What are the ACID properties? Explain each briefly.**
Atomicity ensures a transaction is all-or-nothing. Consistency ensures the database moves from one valid state to another, respecting all integrity constraints. Isolation ensures concurrently executing transactions don't interfere with each other's intermediate states. Durability ensures that once a transaction commits, its changes persist even through system crashes or power failures.

**Q3. Why is Isolation important in a multi-user database system?**
Without isolation, concurrently running transactions could read each other's uncommitted (intermediate) data, leading to inconsistent results — like one transaction reading a partially updated balance during a funds transfer. Isolation ensures each transaction behaves as if it's executing alone, even when multiple transactions run at the same time, preventing anomalies like dirty reads, non-repeatable reads, or lost updates.

**Q4. What is the difference between Atomicity and Durability?**
Atomicity guarantees that all operations within a transaction either complete entirely or not at all (protecting against partial execution). Durability guarantees that once a transaction has successfully committed, its effects are permanent and survive any subsequent system failure (protecting already-completed work from being lost).

**Q5. Explain the different states a transaction can go through, in order.**
Active (read/write operations executing) → Partially Committed (operations done, changes in memory buffer, not yet permanent) → Committed (changes made permanent on disk) → Terminated. Alternatively, if a failure occurs at any point before commit: Active/Partially Committed → Failed → Aborted (rollback completed, DB restored to prior state) → Terminated.

**Q6. What is the difference between the "Failed" state and the "Aborted" state?**
The Failed state is when an error occurs during transaction execution, making it impossible to continue — but the changes made so far are still sitting in the buffer. The Aborted state is reached *after* the failed transaction's buffered changes have been rolled back (reversed), restoring the database to its state prior to the transaction.

**Q7. Can a transaction be rolled back after reaching the Committed state? Why or why not?**
No. Once a transaction reaches the Committed state, its changes have been made permanent on the database (satisfying Durability), and rollback is no longer possible from that point — any further correction would require a new, separate compensating transaction rather than a rollback.

**Q8. What does it mean for a transaction to be "Terminated"?**
A transaction is considered terminated once it reaches either the Committed state (successful completion) or the Aborted state (unsuccessful, rolled back) — both represent the natural end of a transaction's lifecycle, just via different outcomes.

**Q9. Why does the "Partially Committed" state exist as a separate state from "Committed"?**
Because executing a transaction's operations (writing to an in-memory buffer) is logically separate from actually flushing those changes permanently to disk storage. A transaction can complete all its intended operations successfully but still be vulnerable to failure before the final write-to-disk step — the Partially Committed state captures this in-between, still-risky phase.

**Q10. Give a real-world example illustrating why Atomicity matters.**
In a bank funds transfer from Account A to Account B, the transaction involves two steps: debit A, then credit B. If the system crashes after debiting A but before crediting B, without atomicity the money would simply vanish. Atomicity ensures that either both steps happen together, or neither happens at all — preventing this kind of partial, inconsistent update.

**Q11. What is Consistency in the context of ACID, and how is it different from Isolation?**
Consistency ensures that a transaction takes the database from one valid state (satisfying all defined integrity constraints) to another valid state — e.g., total balance across accounts remains the same after a transfer. Isolation, on the other hand, is about how concurrently executing transactions interact with each other (or rather, don't interact) — it doesn't concern data correctness per se, but concurrency behavior.

**Q12. What component of a DBMS is responsible for enforcing Atomicity and Durability?**
The **Recovery Mechanism** component of the DBMS (covered in detail in LEC-13) is responsible for implementing Atomicity and Durability, typically using techniques like the Shadow-copy scheme or Log-based recovery methods.
