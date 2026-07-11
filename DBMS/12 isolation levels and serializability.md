# LEC-23: Isolation Levels & Serializability

*(Not present in original notes — added as a critical interview topic. Directly extends LEC-12's "Isolation" ACID property and LEC-22's Concurrency Control.)*

## 1. Read Phenomena (Concurrency Anomalies)
These are the specific problems that different isolation levels are designed to prevent (or allow, at weaker levels):

| Phenomenon | Description |
|---|---|
| **Dirty Read** | A transaction reads data written by another transaction that has **not yet committed** — if that transaction rolls back, the read data never "really" existed. |
| **Non-Repeatable Read** | A transaction reads the **same row twice** and gets **different values**, because another transaction **updated** and committed a change to that row in between. |
| **Phantom Read** | A transaction re-runs the **same query** (with a range condition) and finds **new rows** that weren't there before, because another transaction **inserted** rows matching that condition in between. |
| **Lost Update** | Two transactions read the same value, and both write back an update independently — one update silently overwrites the other. |

## 2. SQL Standard Isolation Levels
Ordered from **weakest** (fastest, most anomalies allowed) to **strongest** (slowest, fewest anomalies allowed):

| Isolation Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|---|---|---|---|
| **Read Uncommitted** | ✅ Possible | ✅ Possible | ✅ Possible |
| **Read Committed** | ❌ Prevented | ✅ Possible | ✅ Possible |
| **Repeatable Read** | ❌ Prevented | ❌ Prevented | ✅ Possible (in strict SQL standard; MySQL's InnoDB actually prevents most phantom reads here too, via gap locks) |
| **Serializable** | ❌ Prevented | ❌ Prevented | ❌ Prevented |

### a. Read Uncommitted
- **Weakest** isolation level. Transactions can read data written by other **uncommitted** transactions.
- Fastest, but allows all anomalies (dirty reads, non-repeatable reads, phantom reads).
- Rarely used in practice.

### b. Read Committed
- A transaction can only read data that has been **committed** by other transactions — no dirty reads.
- However, if the same row is read twice within the transaction, it might see different values if another transaction committed a change in between (non-repeatable read).
- **Default isolation level** in PostgreSQL, Oracle, SQL Server.

### c. Repeatable Read
- Guarantees that if a transaction reads a row once, it will see the **same value** for that row every time it re-reads it during the transaction — no non-repeatable reads.
- Phantom reads may still occur (technically, per SQL standard) — new rows matching a query's range condition could still appear.
- **Default isolation level in MySQL (InnoDB)** — and InnoDB actually also prevents most phantom reads at this level using **gap locks** (a MySQL-specific enhancement beyond the strict SQL standard).

### d. Serializable
- **Strongest** isolation level. Guarantees that concurrently executing transactions produce a result **equivalent to some serial (one-at-a-time) execution** of those transactions.
- Prevents dirty reads, non-repeatable reads, AND phantom reads.
- Achieved typically via strict locking (like Strict 2PL) or by aborting/retrying transactions that would violate serializability.
- **Slowest** due to heavy locking / high abort-retry rates under contention.

> **Extra — Practical trade-off:** Higher isolation levels give stronger correctness guarantees but at the cost of reduced concurrency (more locking/blocking, more transaction aborts/retries) — meaning lower throughput and higher latency under contention. Choosing an isolation level is fundamentally a trade-off between **consistency guarantees** and **performance**.

## 3. Serializability
- A **schedule** is the order in which operations of multiple concurrent transactions are interleaved during execution.
- A schedule is **serializable** if its outcome is **equivalent to some serial execution** of the same transactions (i.e., as if they'd run one after another, with no overlap) — even though they may have actually executed concurrently.
- Serializability is the **gold-standard correctness criterion** for concurrent execution.

### a. Conflict Serializability
- Two operations **conflict** if:
  1. They belong to **different transactions**.
  2. They operate on the **same data item**.
  3. At least **one of them is a write**.
- A schedule is **conflict serializable** if it can be transformed into a serial schedule by **swapping non-conflicting operations** (operations that don't meet all three conditions above can be freely reordered without changing the outcome).
- Checked using a **Precedence Graph** (aka Serialization Graph):
  - Draw a node for each transaction.
  - Draw an edge Ti → Tj if Ti has an operation that conflicts with, and comes before, an operation of Tj.
  - The schedule is conflict serializable **if and only if this graph has no cycles**.

### b. View Serializability
- A **broader** (less strict) notion than conflict serializability.
- A schedule is **view serializable** if it is "view equivalent" to some serial schedule — meaning:
  1. Each transaction reads the same **initial values**.
  2. Each read operation reads the value produced by the same write operation in both schedules.
  3. Each transaction performs the **final write** on each data item in both schedules.
- **Every conflict serializable schedule is view serializable, but not every view serializable schedule is conflict serializable** (view serializability is a strictly larger set).
- View serializability is much **harder to test** (NP-complete in general), so in practice, DBMSs check for **conflict serializability** instead, since it's easier to verify (via the precedence graph) and is a sufficient (though not always necessary) condition.

> **Extra — Conflict Serializability vs View Serializability comparison:**

| Aspect | Conflict Serializability | View Serializability |
|---|---|---|
| Definition basis | Reordering non-conflicting operations | Matching initial reads, read-from relationships, and final writes |
| Strictness | Stricter (smaller set of valid schedules) | Looser (larger set of valid schedules) |
| Testing complexity | Easy — polynomial time (precedence graph, cycle detection) | Hard — NP-complete in general |
| Relationship | Every conflict-serializable schedule is also view-serializable | Not every view-serializable schedule is conflict-serializable |
| Used in practice? | Yes — this is what real DBMSs actually check for | Rarely, due to computational complexity |

---

## Interview Questions & Answers

**Q1. What are the four SQL standard isolation levels, ordered from weakest to strongest?**
Read Uncommitted (weakest), Read Committed, Repeatable Read, and Serializable (strongest). As you move from weakest to strongest, more concurrency anomalies are prevented, but at the cost of reduced concurrency/performance.

**Q2. What is a Dirty Read, and which isolation level first prevents it?**
A Dirty Read is when a transaction reads data written by another transaction that hasn't committed yet — if that other transaction later rolls back, the read data never "really" existed. Read Committed is the first (weakest) isolation level that prevents dirty reads, by ensuring transactions only read committed data.

**Q3. What is a Non-Repeatable Read, and which isolation level first prevents it?**
A Non-Repeatable Read occurs when a transaction reads the same row twice and gets different values because another transaction updated and committed a change to that row in between the two reads. Repeatable Read is the first isolation level that prevents this, by ensuring a transaction sees consistent values for rows it has already read, throughout its duration.

**Q4. What is a Phantom Read, and how does it differ from a Non-Repeatable Read?**
A Phantom Read occurs when a transaction re-runs a range-based query (e.g., "all orders over $100") and finds new rows that weren't there before, because another transaction inserted matching rows in between. It differs from a Non-Repeatable Read in that it involves NEW rows appearing (due to inserts) rather than existing rows changing value (due to updates) — Non-Repeatable Read is about a specific row's value changing, Phantom Read is about the row SET changing.

**Q5. What is the default isolation level in MySQL (InnoDB), and how does it handle phantom reads differently from the SQL standard?**
MySQL's InnoDB engine defaults to Repeatable Read. Unlike the strict SQL standard (where Repeatable Read technically still allows phantom reads), InnoDB's implementation additionally prevents most phantom reads at this level using a mechanism called "gap locks," which lock the ranges between index records to prevent new rows from being inserted into a range that's been queried by an active transaction.

**Q6. What is Serializability, and why is it considered the "gold standard" for correctness?**
Serializability is the property that a concurrent schedule of transactions produces a result equivalent to SOME serial (sequential, one-at-a-time) execution of those same transactions. It's the gold standard because a serial execution is trivially correct (no interleaving means no concurrency anomalies at all) — so if a concurrent schedule is guaranteed to be equivalent to some serial order, it inherits that same correctness guarantee while still allowing the performance benefits of concurrent execution.

**Q7. What is Conflict Serializability, and how is it tested?**
A schedule is conflict serializable if it can be converted into a serial schedule by repeatedly swapping adjacent, non-conflicting operations (operations are "conflicting" if they belong to different transactions, act on the same data item, and at least one is a write). It's tested by constructing a Precedence Graph — a node per transaction, with an edge Ti → Tj whenever Ti has an operation that conflicts with and precedes an operation of Tj — the schedule is conflict serializable if and only if this graph has no cycles.

**Q8. What is View Serializability, and how does it relate to Conflict Serializability?**
A schedule is view serializable if it's "view equivalent" to some serial schedule — meaning transactions read the same initial values, each read gets its value from the same write in both schedules, and each data item's final write is performed by the same transaction in both schedules. Every conflict-serializable schedule is also view-serializable, but the reverse isn't always true — view serializability is a strictly broader (less restrictive) category, though it's much harder to test computationally (NP-complete), which is why real DBMSs check for conflict serializability instead as a practical, sufficient approximation.

**Q9. Why do real-world DBMSs check for conflict serializability instead of view serializability, even though view serializability allows more valid schedules?**
Because testing for conflict serializability can be done efficiently using a precedence graph and simple cycle detection (polynomial time complexity), whereas testing for view serializability is NP-complete in the general case — computationally impractical to check at runtime for every schedule. Since every conflict-serializable schedule is guaranteed to also be view-serializable, checking for conflict serializability is a practical, "good enough" sufficient condition that DBMSs can actually enforce efficiently.

**Q10. If you're building a banking application, which isolation level would you likely choose, and why?**
Serializable (or at least Repeatable Read with careful design) would typically be preferred for critical financial operations like fund transfers, since correctness (avoiding lost updates, ensuring consistent balances) is far more important than maximizing throughput — the cost of a stale or incorrect balance read is much higher than the performance overhead of stricter locking/isolation.

**Q11. What is a Lost Update anomaly, and which isolation level(s) prevent it?**
A Lost Update occurs when two transactions both read the same data value, then both independently compute and write back an updated value — one transaction's update silently overwrites the other's, and the effect of one update is "lost" entirely. Generally, Repeatable Read and Serializable isolation levels (combined with appropriate locking) prevent lost updates, since they ensure a transaction's read values remain valid/locked until it completes its corresponding write.

**Q12. What is the general trade-off when choosing a higher isolation level?**
Higher isolation levels (like Serializable) provide stronger correctness guarantees by preventing more types of concurrency anomalies, but they achieve this through more restrictive locking or higher rates of transaction abort-and-retry — this reduces the degree of true concurrency the system can achieve, leading to lower throughput and higher latency, especially under high contention. It's fundamentally a trade-off between data consistency guarantees and system performance/concurrency.
