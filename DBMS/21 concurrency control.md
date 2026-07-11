# LEC-22: Concurrency Control

*(Not present in original notes — added as a critical interview topic. Directly extends LEC-12's mention of "concurrent-access anomalies" and "transaction isolation.")*

## 1. Why Concurrency Control?
- Multiple transactions execute **simultaneously** in a DBMS to improve throughput and resource utilization.
- Without control, concurrent execution can cause **anomalies**:
  - **Lost Update** — two transactions read the same value, both update it independently, and one update overwrites the other without either knowing.
  - **Dirty Read** — a transaction reads data written by another transaction that hasn't committed yet (and might later roll back).
  - **Unrepeatable Read** — a transaction reads the same row twice and gets different values, because another transaction modified it in between.
  - **Phantom Read** — a transaction re-runs a query and finds new rows that weren't there before, because another transaction inserted them in between.
- **Concurrency Control** is the set of techniques a DBMS uses to ensure transactions execute concurrently **without violating data integrity** (i.e., preserving Isolation from ACID).

## 2. Lock-Based Protocols
- A **lock** is a variable associated with a data item that controls what operations can be performed on that item while it's locked.

### Types of Locks
- **Shared Lock (S-lock / Read lock):** Allows a transaction to **read** a data item. Multiple transactions can hold a shared lock on the same item simultaneously.
- **Exclusive Lock (X-lock / Write lock):** Allows a transaction to **both read and write** a data item. Only one transaction can hold an exclusive lock on an item at a time, and no other transaction can hold any lock (shared or exclusive) on it simultaneously.

| | S-Lock | X-Lock |
|---|---|---|
| **S-Lock requested by another T** | ✅ Allowed (compatible) | ❌ Blocked |
| **X-Lock requested by another T** | ❌ Blocked | ❌ Blocked |

### Two-Phase Locking (2PL) Protocol
- Ensures **serializability** by dividing a transaction's execution into two phases:
  1. **Growing Phase:** The transaction may **acquire** locks, but **cannot release** any.
  2. **Shrinking Phase:** The transaction may **release** locks, but **cannot acquire** any new ones.
- Once a transaction releases its first lock, it enters the shrinking phase and can never acquire a new lock again.

> **Extra — Variants of 2PL:**
> - **Strict 2PL:** All exclusive (write) locks are held until the transaction **commits or aborts**. Prevents other transactions from reading uncommitted (dirty) data — most commonly used in practice.
> - **Rigorous 2PL:** ALL locks (both shared and exclusive) are held until commit/abort — even stricter than Strict 2PL.
> - **Conservative (Static) 2PL:** A transaction must acquire **all** the locks it will ever need **before** it starts executing — avoids deadlocks entirely, but is impractical since you must know all needed locks upfront.

### Problem with Basic 2PL: Deadlock
- 2PL guarantees serializability, but it does **not prevent deadlocks**.

## 3. Deadlock
- A **deadlock** occurs when two or more transactions are waiting for each other to release locks, and none of them can proceed — a **circular wait**.
- **Example:** T1 holds a lock on A and wants a lock on B. T2 holds a lock on B and wants a lock on A. Neither can proceed.

### Deadlock Handling Approaches

**a. Deadlock Prevention**
- Ensures the system **never enters** a deadlock state, typically by ordering transactions using **timestamps**.
- **Wait-Die scheme:** (Non-preemptive) If an **older** transaction requests a lock held by a **younger** transaction, the older transaction **waits**. If a **younger** transaction requests a lock held by an **older** one, the younger transaction is **aborted (dies)** and restarted later with the same timestamp.
- **Wound-Wait scheme:** (Preemptive) If an **older** transaction requests a lock held by a **younger** one, the older transaction **wounds** (forces abort of) the younger one. If a **younger** transaction requests a lock held by an **older** one, the younger transaction simply **waits**.

**b. Deadlock Avoidance**
- Similar to prevention, but decisions are made more dynamically (e.g., analyzing resource allocation graphs in real time to avoid entering unsafe states).

**c. Deadlock Detection & Recovery**
- Allow deadlocks to occur, then **detect** them (typically using a **Wait-For Graph** — if there's a cycle in the graph, a deadlock exists), and **recover** by aborting one or more transactions (the "victim") to break the cycle.

> **Extra — Wait-Die vs Wound-Wait comparison table:**

| Scheme | Type | Older Transaction wants lock held by Younger | Younger Transaction wants lock held by Older |
|---|---|---|---|
| **Wait-Die** | Non-preemptive | Older **waits** | Younger is **aborted (dies)** |
| **Wound-Wait** | Preemptive | Older **wounds** (forces abort of) Younger | Younger **waits** |

## 4. Starvation
- A transaction **repeatedly gets delayed/aborted** because it keeps losing out to other transactions (e.g., always the "victim" chosen when resolving a conflict), and never gets a chance to complete.
- Solved by using a **fair scheduling policy**, e.g., first-come-first-served ordering when granting locks, or increasing a transaction's priority the longer it waits ("aging").

## 5. Timestamp-Based Protocols
- An **alternative to lock-based protocols** — avoids the need for locks (and hence deadlocks) entirely.
- Every transaction is assigned a unique **timestamp** when it starts (typically based on system clock or a logical counter).
- Conflicting operations (read/write on the same data item) are ordered based on their transaction's timestamp, ensuring the equivalent of a serial schedule in timestamp order.
- Each data item maintains:
  - **W-timestamp(Q):** the largest timestamp of any transaction that successfully executed `write(Q)`.
  - **R-timestamp(Q):** the largest timestamp of any transaction that successfully executed `read(Q)`.
- If a transaction attempts an operation that would violate the timestamp ordering (e.g., an older transaction tries to write after a younger one already read/wrote that item), it is **rolled back and restarted** with a new timestamp.

> **Extra — Lock-based vs Timestamp-based comparison:**

| Aspect | Lock-Based Protocols | Timestamp-Based Protocols |
|---|---|---|
| Deadlock possible? | Yes (unless prevention scheme used) | No — no locks, so no circular waiting |
| Overhead | Managing locks, lock tables | Managing timestamps, potential frequent rollbacks |
| Starvation risk | Yes, without fair scheduling | Yes, if a transaction repeatedly gets rolled back |
| Best suited for | Systems with moderate contention | Systems needing guaranteed deadlock-freedom |

---

## Interview Questions & Answers

**Q1. What is Concurrency Control, and why is it needed?**
Concurrency Control is the set of DBMS techniques that manage simultaneous execution of multiple transactions to ensure data integrity is preserved — specifically, ensuring the Isolation property of ACID. It's needed because uncontrolled concurrent execution can lead to anomalies like lost updates, dirty reads, and inconsistent results.

**Q2. What is the difference between a Shared Lock and an Exclusive Lock?**
A Shared Lock (S-lock) allows a transaction to read a data item and permits multiple transactions to hold shared locks on the same item simultaneously. An Exclusive Lock (X-lock) allows a transaction to both read and write a data item, and no other transaction (whether wanting a shared or exclusive lock) can access that item while it's held.

**Q3. Explain the Two-Phase Locking (2PL) Protocol.**
2PL divides a transaction into two phases: a Growing Phase, where the transaction can acquire locks but not release any, and a Shrinking Phase, where it can release locks but not acquire new ones. Once any lock is released, the transaction moves permanently into the shrinking phase. This protocol guarantees serializability of the resulting schedule.

**Q4. Does 2PL prevent deadlocks?**
No. While 2PL guarantees serializability, it does not prevent deadlocks — two transactions can still end up in a circular wait for locks held by each other. Deadlocks must be handled separately, through prevention schemes (like Wait-Die/Wound-Wait), avoidance, or detection-and-recovery mechanisms.

**Q5. What is the difference between Strict 2PL and basic 2PL?**
Basic 2PL only requires that locks are not acquired after any lock has been released (growing then shrinking phases), but locks can technically be released before commit. Strict 2PL additionally requires that all exclusive (write) locks be held until the transaction actually commits or aborts — this prevents other transactions from reading uncommitted (dirty) data, which is why it's the variant most commonly used in real systems.

**Q6. What is a Deadlock? Give a simple example.**
A deadlock occurs when two or more transactions are stuck waiting for each other's locks in a circular manner, so none can proceed. Example: Transaction T1 holds a lock on data item A and requests a lock on item B; simultaneously, Transaction T2 holds a lock on item B and requests a lock on item A — both wait forever for the other to release its lock.

**Q7. Explain the Wait-Die and Wound-Wait deadlock prevention schemes.**
Both use transaction timestamps to decide what happens on a lock conflict. In Wait-Die (non-preemptive): if an older transaction wants a lock held by a younger one, it waits; if a younger transaction wants a lock held by an older one, the younger one is aborted (dies). In Wound-Wait (preemptive): if an older transaction wants a lock held by a younger one, it forcibly aborts (wounds) the younger transaction; if a younger transaction wants a lock held by an older one, it simply waits.

**Q8. How does the DBMS detect a deadlock if it isn't prevented upfront?**
The DBMS constructs a Wait-For Graph, where nodes represent transactions and a directed edge from Ti to Tj means Ti is waiting for a lock held by Tj. If this graph contains a cycle, a deadlock exists. The DBMS periodically checks for cycles, and if found, selects one or more transactions (usually the one with the least "cost" to abort) as a "victim" to roll back, breaking the cycle.

**Q9. What is Starvation, and how does it differ from Deadlock?**
Starvation occurs when a specific transaction keeps getting delayed or repeatedly chosen as the "victim" in conflict resolution, and as a result, never gets to complete — even though the overall system isn't stuck (other transactions ARE making progress). This differs from Deadlock, where NO involved transactions can make progress at all due to a circular wait.

**Q10. How do Timestamp-Based Protocols avoid deadlocks entirely?**
Timestamp-based protocols don't use locks at all — instead, every transaction gets a unique timestamp, and conflicting operations on the same data item are ordered strictly according to these timestamps. If an operation would violate this required timestamp ordering, the offending transaction is simply rolled back and restarted with a new timestamp, rather than being made to "wait" for a lock — since there's no waiting on locks, there's no possibility of a circular wait (deadlock).

**Q11. What is a Dirty Read, and which concurrency control mechanism helps prevent it?**
A Dirty Read occurs when a transaction reads data that was written by another transaction that hasn't yet committed (and might later roll back), potentially reading data that never "really" existed. Strict 2PL helps prevent this by holding exclusive (write) locks until commit, so no other transaction can read uncommitted, potentially-to-be-rolled-back data.

**Q12. Compare Lock-Based and Timestamp-Based concurrency control approaches.**
Lock-based protocols (like 2PL) use locks to control access to data items and can guarantee serializability, but are susceptible to deadlocks unless combined with a prevention/detection scheme, and involve overhead in managing lock tables. Timestamp-based protocols avoid locks and hence avoid deadlocks entirely, ordering operations strictly by transaction timestamp, but can suffer from frequent transaction rollbacks/restarts under high contention, and also carry a risk of starvation if a transaction repeatedly gets rolled back.
