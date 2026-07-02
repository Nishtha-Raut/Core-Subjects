# 06 — Concurrency Basics (Race Conditions, Critical Section, Mutex, Semaphores, Condition Variables)

## 1. Introduction to Concurrency

- **Concurrency** = execution of multiple instruction sequences **at the same time**.
- Happens in the OS when several process threads run in parallel.

### Thread Recap
- Single sequence stream within a process.
- Independent path of execution.
- Light-weight process.
- Used to achieve parallelism by dividing a process's tasks into independent execution paths.
- **Example:** Multiple browser tabs, or a text editor where spell-checking, formatting, and auto-save happen concurrently via multiple threads.

### Thread Scheduling & Context Switching (recap)
- Threads are scheduled by priority; assigned processor time slices like processes.
- Thread context switching **doesn't** switch the memory address space (fast, cache preserved) — unlike process context switching.
- Uses a **Thread Control Block (TCB)** (analogous to PCB) for state storage during context switches.

### Will a single-CPU system gain from multi-threading?
> **Never.** Since two threads still have to context-switch for that single CPU, there's no real parallel gain — you're just interleaving, not truly running simultaneously.

### Benefits of Multi-threading
- **Responsiveness** — app stays responsive even if one task is slow (e.g., UI thread stays smooth while a background thread downloads a file).
- **Resource sharing** — efficient sharing of memory/resources between threads of the same process.
- **Economy** — cheaper to create/context-switch threads than processes (allocating memory/resources per-process is costly).
- **Better multiprocessor utilization** — threads let you actually exploit multiple CPU cores efficiently.

**Real-life analogy:** A restaurant kitchen with one chef (single CPU) "multi-threading" between three dishes is still bound by having only one pair of hands — dishes get made faster in wall-clock terms only if you have **multiple chefs** (multiple CPU cores) working truly in parallel.

---

## 2. Critical Section Problem

- **Process synchronization** techniques play a key role in maintaining the **consistency of shared data**.

### Critical Section (C.S.)
> The segment of code where processes/threads **access shared resources** (common variables, files) and perform **write operations** on them. Since processes/threads execute concurrently, any process can be interrupted mid-execution — creating risk.

### Race Condition
> A **race condition** occurs when two or more threads access shared data and try to change it **at the same time**. Because the thread scheduling algorithm can swap between threads at any point, the *order* of access is unpredictable — so the final result of the data change depends on scheduling, i.e., both threads are "racing" to access/change the data.

**Real-life analogy:** Imagine two people trying to book the **last seat** on a flight at the exact same moment through two different browser tabs. Both read "1 seat available," both click "Book," and if there's no proper locking mechanism, **both** bookings might succeed — overselling the seat. That's a race condition on the shared `available_seats` variable.

### Concrete Example (classic interview example)
```c
// Shared variable
int counter = 100;

// Thread A                  // Thread B
temp = counter;   // reads 100    temp = counter;   // reads 100 (before A writes back!)
temp = temp + 1;  // 101           temp = temp - 1;  // 99
counter = temp;   // writes 101   counter = temp;    // writes 99 (overwrites A's update!)
```
> Depending on the interleaving, the final value of `counter` could be 99, 101, or 100 — **not deterministic**. This non-determinism is the core danger of race conditions.

### Solutions to Race Conditions
1. **Atomic operations** — make the critical section execute as a single, uninterruptible operation (one CPU cycle equivalent).
2. **Mutual Exclusion using locks.**
3. **Semaphores.**

### Can a simple flag variable solve race conditions?
> **No.** A simple boolean flag isn't safe either, because checking the flag and setting it are themselves **two separate operations** — another thread can interleave between the check and the set (this is itself a race condition on the flag).

### Peterson's Solution
- Can avoid race conditions, but **only works for exactly 2 processes/threads**. Not generalizable to N threads — of mostly theoretical/educational interest today (modern CPUs' instruction reordering also breaks naive Peterson's solution without memory barriers).

---

## 3. Mutex / Locks

- Locks implement **mutual exclusion**, avoiding race conditions by allowing only **one thread/process** to access the critical section at a time.

### Disadvantages of Locks
| Issue | Description |
|---|---|
| **Contention** | One thread holds the lock; others **busy-wait**. If the lock-holding thread dies while holding the lock, all others wait **infinitely**. |
| **Deadlocks** | Covered in depth in File 8. |
| **Debugging difficulty** | Concurrency bugs are notoriously hard to reproduce/debug. |
| **Starvation of high-priority threads** | A lower-priority thread might repeatedly grab the lock, blocking a higher-priority thread. |

**Real-life analogy:** A mutex is like a **single-key bathroom** in a coffee shop — only one person (thread) can be inside at a time. If someone locks the door and then faints inside (thread dies holding the lock), everyone else is stuck waiting outside forever — a real-world "contention" disaster, which is why some public washrooms have staff carry a spare key (timeout/recovery mechanisms in real systems).

---

## 4. Condition Variables

- A **condition variable** is a synchronization primitive that lets a thread **wait until a certain condition occurs**.
- Works together with a **lock**.
- A thread can enter a **wait state** only when it has **acquired the lock**. When a thread enters the wait state, it **releases the lock** and waits until another thread **notifies** that the event has occurred. Once the waiting thread is signaled and re-enters the running state, it **re-acquires the lock immediately** and continues.
- **Why use a condition variable?** → To **avoid busy waiting** (continuously polling a condition in a loop, wasting CPU cycles).
- **Contention is not an issue here** — because the thread releases the lock while waiting, other threads can proceed.

**Real-life analogy:** Instead of repeatedly checking your phone every 5 seconds to see if your food delivery has arrived (busy waiting = wasted effort), you put your phone on the table and wait for the **notification ping** (condition variable signal) — you're not wasting energy constantly checking; you just react when notified.

---

## 5. Semaphores

- A **synchronization method**.
- An **integer** value equal to the number of available resources.
- Multiple threads **can** execute the critical section concurrently — up to the semaphore's count.
- Allows multiple program threads to access a **finite number of instances** of a resource, whereas a mutex allows only **one thread at a time** to access a single shared resource.

### Types of Semaphores

| Type | Description |
|---|---|
| **Binary Semaphore** | Value can be **0 or 1** — aka **mutex locks**. |
| **Counting Semaphore** | Can range over an **unrestricted domain**; used to control access to a resource with a **finite number of instances**. |

### Avoiding Busy Waiting in Semaphores
- To overcome the need for busy waiting, the `wait()` and `signal()` semaphore operations are modified:
  - When a process executes `wait()` and finds the semaphore value **not positive**, it must wait — but instead of busy-waiting, the process **blocks itself**.
  - The **block** operation places the process into a **waiting queue** associated with the semaphore, and switches its state to **Waiting**. Control transfers to the CPU scheduler to run another process.
  - A blocked process is restarted when another process executes `signal()` on that semaphore — via a **wakeup()** operation, moving it from Waiting → Ready, and placing it in the ready queue.

**Real-life analogy:** A semaphore is like a **parking garage with a limited number of spots** (say, 5). A counter at the entrance tracks available spots (the semaphore value). Cars can enter (`wait()`) as long as spots > 0, decrementing the counter. When a car leaves (`signal()`), the counter increments, and if cars were waiting at the gate (blocked, not honking endlessly = not busy-waiting), one gets let in. A **binary semaphore** would be like a **single-car garage** — either occupied (0) or free (1), functionally the same as a mutex lock.

---

## Interview Q&A — Concurrency Basics

**Q1: What's the fundamental difference between a mutex and a semaphore?**
> A mutex is a **locking mechanism** for mutual exclusion — only the thread that locked it can unlock it, and it protects exactly one resource, allowing exactly one thread in the critical section at a time. A semaphore is a **signaling/counting mechanism** — it can allow multiple threads (up to `N`) into the critical section simultaneously, and unlike a mutex, any thread can signal/release it, not necessarily the one that acquired it.

**Q2: Why is a simple boolean flag not sufficient to solve a race condition?**
> Because checking the flag's value and then setting it are two separate, non-atomic steps. A thread could check the flag (see it's `false`), get preempted before setting it to `true`, and another thread could also check it (still sees `false`) and proceed — both threads think they have exclusive access. The check-and-set itself needs to be atomic, which a plain flag doesn't guarantee.

**Q3: Why do condition variables need to be used together with a lock/mutex?**
> Because checking a condition (e.g., "is the queue empty?") and then deciding to wait must be done atomically with respect to other threads — otherwise, you could have a race where the condition changes *between* your check and your wait call, causing you to wait forever even though the condition is already true (a "lost wakeup"). The mutex ensures the check-and-wait sequence is atomic, and releasing the lock during the wait avoids deadlocking other threads that need to update the condition.

**Q4: Give a real-world software example where you'd use a semaphore instead of a mutex.**
> A **connection pool** to a database that allows a maximum of, say, 10 concurrent connections. You'd use a counting semaphore initialized to 10 — each thread calls `wait()` to "check out" a connection (decrementing the count) and `signal()` to "return" it (incrementing the count) when done. A mutex wouldn't work here since it only allows exactly 1 thread through, but we want up to 10 concurrent connections.

**Q5: What is "busy waiting" and why is it bad?**
> Busy waiting is when a thread repeatedly checks a condition in a tight loop (`while(!ready) {}`) instead of sleeping/blocking — it wastes CPU cycles that could be used by other processes, and on a single-core system, it can even prevent the condition from ever becoming true (since the thread that would set it never gets CPU time). Condition variables and semaphore blocking avoid this by putting the waiting thread to sleep until it's explicitly woken up.

**Q6: How would you explain a race condition you might have debugged, in an interview?**
> "I once had two threads incrementing a shared analytics counter without synchronization. Under load, the final count was consistently lower than expected — a classic **lost update** race condition, because `counter++` isn't atomic (it's a read-modify-write). I fixed it using `std::atomic<int>` in C++ / `AtomicInteger` in Java, or wrapped the increment in a mutex, depending on performance needs."

---

**Next up (File 7):** Classic Synchronization Problems — Producer-Consumer, Reader-Writer, Dining Philosophers.
