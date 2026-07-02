# 07 — Classic Synchronization Problems (Producer-Consumer, Reader-Writer, Dining Philosophers)

These are the three "must-know" synchronization problems for OS interviews — they show up both as **conceptual questions** and as **coding problems** (often on LeetCode's "Concurrency" section).

---

## 1. Producer-Consumer Problem (Bounded Buffer Problem)

### Setup
- **Producer thread**: generates data and inserts it into a shared buffer.
- **Consumer thread**: removes/consumes data from the shared buffer.
- The buffer has **`n` slots** (bounded — fixed capacity).

```
Producer --> [ | | | | | ] --> Consumer picks
              n slots
```

### The Problem
- We need to **synchronize** the buffer (critical section) between the Producer and Consumer.
- **Producer must NOT insert data when the buffer is full.**
- **Consumer must NOT pick/remove data when the buffer is empty.**

**Real-life analogy:** Think of a **bakery conveyor belt** with limited space for 10 trays (bounded buffer). The baker (producer) can't put a new tray on the belt if all 10 slots are full — they must wait. The packer (consumer) can't pick up a tray if the belt is empty — they must wait too. Both need to coordinate so the baker doesn't overflow the belt and the packer doesn't try to grab a nonexistent tray.

### Solution — Semaphores

| Semaphore | Type | Purpose |
|---|---|---|
| `mutex` | Binary semaphore | Used to acquire a **lock on the buffer** itself (protects the actual insert/remove operation) |
| `empty` | Counting semaphore, **initial value = n** | Tracks the number of **empty slots** |
| `full` | Counting semaphore, **initial value = 0** | Tracks the number of **filled slots** |

### Pseudocode

```c
// Producer                          // Consumer
do {                                 do {
    wait(empty);   // wait until         wait(full);    // wait until
                    // empty > 0                          // full > 0, then full--
                    // then empty--
    wait(mutex);                          wait(mutex);

    // C.S: add data to buffer              // remove data from buffer

    signal(mutex);                        signal(mutex);
    signal(full);  // increment full       signal(empty); // increment empty

} while(1);                          } while(1);
```

**Why this order matters:** `wait(empty)` / `wait(full)` is called **before** `wait(mutex)` — this prevents deadlock. If we acquired `mutex` first and then blocked on `empty`/`full`, we'd hold the buffer lock while waiting, and the other thread would never be able to enter the critical section to change the count — a classic deadlock trap.

---

## 2. Reader-Writer Problem

### Setup
1. **Reader threads** → only **read** data.
2. **Writer threads** → **write/update** data.

### Rules
1. **If more than one reader is reading** → **no issue**, since reads don't modify data — multiple readers can safely read concurrently.
2. **If more than one writer is writing, OR a writer + any other thread (reader/writer) access the shared resource in parallel** → **Race Condition** → **data inconsistency**.

**Real-life analogy:** Think of a **shared Google Doc**.
- Multiple people **viewing** (reading) the doc simultaneously is totally fine — no conflicts.
- But if two people try to **edit the same paragraph** at the same time, or one person reads while another is mid-edit, you can get garbled/inconsistent content — hence writers need **exclusive access**.

### Solution — Semaphores

| Variable | Type | Purpose |
|---|---|---|
| `mutex` | Binary semaphore | Ensures **mutual exclusion** when the `read_count` (rc) variable itself is being updated — no two threads should modify `rc` at the same time |
| `wrt` | Binary semaphore | **Common lock** for both readers and writers — ensures a writer gets exclusive access, and the *first* reader also uses it to block writers |
| `read_count (rc)` | Integer, starts at 0 | Tracks **how many readers** are currently reading in the critical section |

### Writer Solution
```c
do {
    wait(wrt);

    // do write operation

    signal(wrt);
} while (true);
```

### Reader Solution
```c
do {
    wait(mutex);     // to mutex the read_count variable
    rc++;
    if (rc == 1)
        wait(wrt);    // ensures no writer can enter if there is even one reader
    signal(mutex);

    // C.S: Reader is reading

    wait(mutex);
    rc--;             // a reader leaves
    if (rc == 0)       // no reader is left in C.S
        signal(wrt);   // writer can enter
    signal(mutex);     // reader leaves
} while (1);
```

**Key insight:** Only the **first reader** to arrive locks `wrt` (blocking writers), and only the **last reader** to leave unlocks `wrt`. In between, any number of readers can freely enter/exit without touching `wrt` — that's what allows concurrent reads.

**Note:** This is the classic "**readers-preference**" solution — it can lead to **writer starvation** if readers keep arriving continuously (writers can starve indefinitely). A "writers-preference" or "fair" variant exists too, but the readers-preference version above is the standard textbook solution.

---

## 3. Dining Philosophers Problem

### Setup
- **5 philosophers** sit around a **circular table**.
- Each philosopher spends life in **two states**: **Thinking** and **Eating**.
- In the center is a bowl of noodles, and the table has **5 single forks** (one between each pair of adjacent philosophers).

```
            Philosopher 1
                 |
Philosopher 5         Philosopher 2
        \      [Bowl]      /
         \                /
Philosopher 4 -------- Philosopher 3
```

### Rules
- **Thinking state**: philosopher doesn't interact with others.
- **Eating state**: philosopher tries to pick up the **2 forks adjacent to them** (left and right) — **one fork at a time**.
- A philosopher **can't pick up a fork** if it's already taken by a neighbor.
- Once a philosopher has **both forks**, they eat **without releasing** the forks until done.

### Naive Semaphore Solution
- Each fork = a **binary semaphore**.
- A philosopher calls `wait()` to acquire a fork, `signal()` to release it.
- `Semaphore fork[5] = {1, 1, 1, 1, 1};`

### The Deadlock Problem
> Although the semaphore solution ensures **no two neighbors eat simultaneously**, it can still create a **Deadlock**.

**Scenario:** If all 5 philosophers become hungry **at the same time** and each picks up their **left fork first**, then **all 5 fork semaphores become 0**. When each philosopher then tries to grab their **right fork**, they'll be waiting **forever** — classic circular wait **Deadlock**.

**Real-life analogy:** Imagine 5 people around a table, each needing 2 chopsticks to eat, but there are only 5 chopsticks total (one between each pair). If everyone simultaneously grabs the chopstick to their left, no one has a second chopstick free, and everyone sits there indefinitely waiting for their right-hand neighbor to put theirs down — except nobody will, because everybody's stuck the same way. Nobody eats. This is a perfect illustration of **deadlock** in real life.

### Solutions to Avoid Deadlock (semaphores alone aren't enough)

1. **Allow at most 4 philosophers to sit simultaneously** (out of 5) — guarantees at least one philosopher can always get both forks.
2. **Pick up forks only if both are available, atomically** (in a critical section) — a philosopher checks both forks together, and only picks them up if both are free at that moment; otherwise waits without holding either.
3. **Odd-even rule**: An **odd-numbered philosopher** picks up their **left fork first, then right**. An **even-numbered philosopher** picks up their **right fork first, then left**. This breaks the symmetric "everyone grabs left simultaneously" scenario, preventing circular wait.

> **Conclusion:** Semaphores alone are **not enough** to solve the Dining Philosophers problem safely — you need additional **enhancement rules** (like the ones above) to guarantee a deadlock-free solution.

---

## Interview Q&A — Classic Synchronization Problems

**Q1: In the Producer-Consumer problem, why do we need both a mutex AND the `empty`/`full` semaphores? Isn't mutex enough?**
> Mutex alone only ensures **one thread accesses the buffer at a time** — it doesn't track *how many* items are in the buffer or block a producer from inserting into a full buffer or a consumer from removing from an empty one. `empty`/`full` are **counting semaphores** that track available slots/items, enforcing the bounded-buffer constraint. Mutex handles *mutual exclusion*; `empty`/`full` handle the *capacity constraint*.

**Q2: Why does the readers-writer solution risk writer starvation, and how would you fix it?**
> Because in the readers-preference solution, as long as *any* reader is in the critical section (or new readers keep arriving), a waiting writer is blocked indefinitely from acquiring `wrt`. To fix it, you can implement a **fair/writers-preference** solution — e.g., once a writer is waiting, no *new* readers are allowed to start until that writer has had a turn (often implemented via an additional semaphore that queues readers behind waiting writers).

**Q3: What are the 4 necessary conditions for deadlock, and which one does the Dining Philosophers "odd-even rule" break?**
> The 4 conditions are: Mutual Exclusion, Hold & Wait, No Preemption, and Circular Wait (detailed in File 8). The odd-even rule breaks **Circular Wait** — by forcing philosophers to acquire forks in different orders (some left-then-right, some right-then-left), you can no longer form a cycle where everyone is waiting on the next person's resource in the same rotational direction.

**Q4: How does the Dining Philosophers problem map to a real distributed systems scenario?**
> It's a classic model for **resource contention among independent, symmetric processes** — e.g., multiple microservices/threads all needing to lock 2 shared database rows to complete a transaction (like a funds transfer needing to lock both the sender's and receiver's account rows). If every service always locks "account A then B" in the same naive order without a consistent global ordering, you can get the same kind of circular-wait deadlock. The fix (like the odd-even rule) is to **always acquire locks in a consistent global order** (e.g., always lock the lower account ID first).

**Q5: Give a one-line real-world example for each of the three problems, as you'd say in an interview:**
> - **Producer-Consumer** → A message queue (like Kafka) where producers publish messages and consumers process them from a bounded topic partition.
> - **Reader-Writer** → A cache (like Redis) where many services read a cached value concurrently, but only one writer updates it at a time, and reads must not see a half-written value.
> - **Dining Philosophers** → Multiple services needing to lock two shared resources (e.g., two database rows) to complete a transaction — improper lock ordering can deadlock the whole system.

---

**Next up (File 8):** Deadlocks — Necessary Conditions, Prevention, Avoidance (Banker's Algorithm), Detection & Recovery.
