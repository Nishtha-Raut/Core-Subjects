# LEC-13: How to Implement Atomicity and Durability in Transactions

## 1. Introduction
- The **Recovery Mechanism** component of a DBMS supports **atomicity** and **durability**.

## 2. Shadow-Copy Scheme
- Based on making **copies of the DB** (aka **shadow copies**).
- **Assumption:** only **one Transaction (T)** is active at a time.
- A pointer called **db-pointer** is maintained on the **disk**, which at any instant points to the **current copy** of the DB.
- T, that wants to update the DB, first creates a **complete copy** of the DB.
- All further updates are done on the **new DB copy**, leaving the original copy (**shadow copy**) untouched.
- If at any point T has to be **aborted**, the system simply **deletes the new copy**; the old copy is unaffected.
- If T **succeeds**, it is committed as follows:
  1. The OS makes sure all the pages of the new copy of the DB are written to disk.
  2. The DB system updates the **db-pointer** to point to the new copy of the DB.
  3. The new copy is now the **current copy** of the DB.
  4. The old copy is **deleted**.
  5. T is said to have been **COMMITTED** at the point where the updated db-pointer is written to disk.

### Atomicity (via Shadow-Copy)
- If T fails at any time **before** the db-pointer is updated, the old content of the DB is **not affected**.
- T's abort can be done by just **deleting the new copy** of the DB.
- Hence, either **all updates are reflected**, or **none**.

### Durability (via Shadow-Copy)
- Suppose the system fails at any time **before** the updated db-pointer is written to disk.
  - When the system restarts, it will read the db-pointer & will thus see the **original content** of the DB — none of the effects of T will be visible.
  - T is assumed to be successful **only when** the db-pointer is updated.
- If the system fails **after** the db-pointer has been updated (before which all pages of the new copy were already written to disk):
  - When the system restarts, it will read the **new DB copy**.
- The implementation is dependent on the write to the db-pointer being **atomic**. Luckily, disk systems provide atomic updates to an entire block or at least a disk sector. So, we make sure the db-pointer lies entirely within a **single sector**, by storing the db-pointer at the **beginning of a block**.

### Drawback
- **Inefficient**, as the **entire DB is copied** for every transaction.

> **Extra — Why Shadow-Copy is rarely used in modern large-scale DBs:** Copying the *entire* database for every single transaction is prohibitively expensive at scale (imagine copying a multi-terabyte database for a single row update). This is precisely why real-world production DBMSs use **Log-based recovery** instead — it only needs to record the specific *changes* made, not duplicate the whole database.

## 3. Log-Based Recovery Methods
- The **log** is a sequence of records. A log of each transaction is maintained in some **stable storage**, so that if any failure occurs, it can be recovered from there.
- If any operation is performed on the database, it will be **recorded in the log**.
- The process of storing the logs should be done **before** the actual transaction is applied in the database (this is the **Write-Ahead Logging / WAL** principle).
- **Stable storage** is a classification of computer data storage technology that guarantees atomicity for any given write operation, and allows software to be written that is robust against some hardware and power failures.

> **Extra — Write-Ahead Logging (WAL), explicitly named:** Although the original notes just describe "storing logs before applying the transaction," this principle has a standard name: **Write-Ahead Logging (WAL)**. WAL is the fundamental rule underlying nearly all modern recovery systems (used by PostgreSQL, MySQL/InnoDB, etc.) — a log record describing a change must be written to stable storage *before* the corresponding data change is written to the database itself.

### a. Deferred DB Modifications
- Ensures **atomicity** by recording all the DB modifications in the log, but **deferring** the execution of all the write operations until the **final action (commit)** of T has been executed.
- Log information is used to execute the deferred writes when T is completed.
- If the system crashes **before** T completes, or if T is **aborted**, the information in the logs is simply **ignored** (since actual DB writes never happened).
- If T **completes**, the records associated with it in the log file are used in executing the **deferred writes**.
- If a failure occurs while this updating is taking place, we perform **redo**.

### b. Immediate DB Modifications
- DB modifications are output to the DB **while T is still in the active state** (i.e., writes happen immediately, not deferred).
- DB modifications written by an active T are called **uncommitted modifications**.
- In the event of a crash or T failure, the system uses the **old value field** of the log records to **restore** modified values.
- An update takes place **only after** the corresponding log record is safely in stable storage (again, following WAL).

#### Failure Handling (Immediate Modifications)
1. **System failure before T completes**, or if T is **aborted** → the **old value field** is used to **undo** T.
2. **If T completes and the system crashes afterward** → the **new value field** is used to **redo** T, provided a commit log entry exists for that T.

> **Extra — UNDO vs REDO logic summarized:**

| Scenario | Log field used | Action |
|---|---|---|
| T did not commit before crash | Old value | **UNDO** — roll back to old value |
| T committed before crash, but data not yet flushed to disk | New value | **REDO** — reapply the change |

> **Extra — Deferred vs Immediate Modifications comparison:**

| Aspect | Deferred DB Modification | Immediate DB Modification |
|---|---|---|
| When writes happen | Only after commit (writes deferred) | Can happen while transaction is still active |
| Undo needed? | Never (nothing written until commit) | Yes, if T fails before commit |
| Redo needed? | Yes, if crash happens during the deferred write phase after commit | Yes, if crash happens after commit but before all changes flushed to disk |
| Log fields required | Only new values needed | Both old and new values needed |

---

## Interview Questions & Answers

**Q1. What is the purpose of the Recovery Mechanism in a DBMS?**
The Recovery Mechanism is responsible for ensuring the Atomicity and Durability properties of transactions — making sure that a transaction's effects are either fully applied or fully undone, and that once committed, changes survive system crashes or power failures.

**Q2. Explain the Shadow-Copy scheme for implementing atomicity and durability.**
In the Shadow-Copy scheme, any transaction that wants to modify the database first creates a complete copy of the database and makes all its changes on that new copy, leaving the original (shadow) copy untouched. A db-pointer on disk always points to the current valid copy. If the transaction succeeds, the db-pointer is atomically updated to point to the new copy (and the old one is deleted); if it fails, the new copy is simply discarded, leaving the original database intact.

**Q3. Why is the Shadow-Copy scheme considered inefficient?**
Because it requires copying the *entire* database for every single transaction, regardless of how small the actual change is — this is extremely wasteful in terms of both storage and time for any database larger than trivially small, making it impractical for real-world large-scale systems.

**Q4. How does the Shadow-Copy scheme guarantee atomicity?**
If a transaction fails at any point before the db-pointer is updated, the original database copy remains completely untouched and valid — the system simply deletes the incomplete new copy. Since the switch to the new copy only happens via a single atomic db-pointer update, either all the transaction's changes take effect (pointer updated) or none do (pointer unchanged) — satisfying atomicity.

**Q5. Why must the db-pointer update itself be atomic, and how is this achieved physically?**
If the db-pointer update itself could partially fail or be interrupted, the system could end up in an ambiguous state, not knowing which copy is authoritative — undermining the entire atomicity guarantee. This is achieved by relying on the fact that disk systems guarantee atomic writes to an entire block or sector, so the db-pointer is stored entirely within a single disk sector/block (often at the beginning of a block) to leverage this hardware-level atomicity.

**Q6. What is Write-Ahead Logging (WAL), and why is it important?**
WAL is the principle that a log record describing a change to the database must be written to stable storage *before* the actual change is applied to the database itself. It's important because it ensures that even if a crash occurs immediately after (or during) an actual data write, the system can always consult the log to determine what should have happened and correctly recover — either by undoing incomplete changes or redoing committed ones.

**Q7. What is the difference between Deferred and Immediate Database Modification techniques?**
In Deferred Modification, all changes are recorded in the log first but the actual database writes are postponed until the transaction commits — meaning no undo is ever needed (since nothing was written prematurely), only redo in case of a crash during the deferred-write phase. In Immediate Modification, changes may be written to the database while the transaction is still active (before commit) — this requires both undo capability (using old values, if the transaction fails before commit) and redo capability (using new values, if it crashes after commit but before all changes are flushed).

**Q8. In Immediate DB Modification, when do we use UNDO vs REDO during recovery?**
UNDO is used when a transaction had NOT committed before the system crash — we use the log's old value field to revert any changes that transaction had already written to the database. REDO is used when a transaction HAD committed before the crash but its changes might not have been fully flushed to permanent storage yet — we use the log's new value field to reapply those changes.

**Q9. What is "stable storage," and why is it critical for recovery mechanisms?**
Stable storage is a storage technology (or technique) that guarantees atomicity for any given write operation, remaining robust against certain hardware and power failures — essentially storage that either fully completes a write or leaves no trace of it, never a partial/corrupted write. It's critical because the entire recovery mechanism (both shadow-copy and log-based methods) depends on being able to trust that at least some reference point (the db-pointer or the log) is never left in a corrupted, half-written state.

**Q10. Why does Deferred DB Modification never require an UNDO operation?**
Because in this technique, no actual database writes happen until the transaction has fully completed (committed) — everything is first only recorded in the log. Since nothing was ever physically written to the database prematurely, there's nothing to undo; if the transaction fails or the system crashes before commit, the log entries are simply ignored/discarded.

**Q11. Compare the Shadow-Copy scheme with Log-based recovery in terms of practicality.**
The Shadow-Copy scheme is conceptually simple and gives strong atomicity/durability guarantees, but is highly inefficient since it duplicates the entire database for every transaction — impractical for large or frequently updated databases. Log-based recovery is far more practical and widely used in real systems (like MySQL, PostgreSQL) because it only needs to log the specific changes made by each transaction, not the entire database, making it vastly more storage- and time-efficient while still providing the same atomicity/durability guarantees.

**Q12. What happens to the log records of an aborted transaction under Deferred Modification?**
They are simply ignored/discarded. Since Deferred Modification never writes actual changes to the database until commit, an aborted (or crashed-before-commit) transaction's log entries never get applied — the database remains as if that transaction never happened.
