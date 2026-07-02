# 08 — Deadlocks (Conditions, Prevention, Avoidance, Detection, Recovery)

## 1. What is a Deadlock?

- In a multi-programming environment, several processes compete for a **finite number of resources**.
- A process **requests a resource (R)**. If R is not available (held by another process), the process enters a **waiting state**.
- Sometimes a waiting process is **never able to change its state**, because the resource it requested is busy **forever** — this is called **DEADLOCK (DL)**.
- **Two or more processes are waiting on some resource's availability, which will never become available** because it's also busy with some other waiting process — the processes are said to be in **Deadlock**.
- Deadlock is essentially a **bug in the process/thread synchronization method**.
- In deadlock, processes **never finish executing**, and system resources are tied up, preventing other jobs from starting.

**Examples of resources:** memory space, CPU cycles, files, locks, sockets, I/O devices, etc.
> A single resource type can have **multiple instances** — e.g., "CPU" is a resource type, and a system might have 2 CPU instances.

### How a process/thread utilizes a resource (lifecycle)
1. **Request**: Request the resource. If free, lock it; else wait until available.
2. **Use**.
3. **Release**: Release the resource instance, making it available for other processes.

```
   T1 <---allocated--- R1
   |                    ^
  Req.                 Req.
   |                    |
   v                    |
   R2 ---allocated---> T2
```
*(This is a classic **wait-for / resource-allocation graph** showing a cycle — T1 holds R1 and wants R2; T2 holds R2 and wants R1. Neither can proceed → deadlock.)*

**Real-life analogy:** Two cars meeting at a narrow single-lane bridge from opposite ends, each having already driven halfway onto the bridge. Car A won't reverse because it wants to keep moving forward (holding its position, waiting for the bridge ahead — held by Car B). Car B does the same in the opposite direction. Neither backs up, and both are stuck forever — a textbook deadlock.

---

## 2. Deadlock — 4 Necessary Conditions

**All 4 conditions must hold SIMULTANEOUSLY for a deadlock to occur:**

| # | Condition | Description |
|---|---|---|
| 1 | **Mutual Exclusion** | Only 1 process at a time can use the resource; if another process requests it, that process must wait until the resource is released. |
| 2 | **Hold & Wait** | A process must be **holding at least one resource** while **waiting to acquire additional resources** currently held by other processes. |
| 3 | **No Preemption** | A resource can only be **voluntarily released** by the process holding it, after it completes execution (no forced resource preemption). |
| 4 | **Circular Wait** | A set `{P0, P1, ..., Pn}` of waiting processes must exist such that `P0` waits for a resource held by `P1`, `P1` waits for a resource held by `P2`, ..., and `Pn` waits for a resource held by `P0`. |

**Real-life analogy for all 4 together (the classic Dining Philosophers scenario):**
1. **Mutual exclusion** — a fork can only be held by one philosopher at a time.
2. **Hold & wait** — each philosopher holds their left fork while waiting for the right one.
3. **No preemption** — nobody forcibly takes a fork away from a neighbor.
4. **Circular wait** — Philosopher 1 waits on Philosopher 2's fork, who waits on Philosopher 3's fork... all the way back around to Philosopher 1.

---

## 3. Methods for Handling Deadlocks

There are **3 broad strategies**:

1. **Prevent or avoid** deadlocks via a protocol — ensure the system will **never enter** a deadlocked state.
2. **Allow** the system to enter a deadlocked state, then **detect it and recover**.
3. **Ignore the problem altogether**, pretending deadlocks never occur (the **Ostrich Algorithm**) — aka **Deadlock Ignorance**. (Surprisingly common in practice — e.g., most general-purpose OSes like Linux/Windows mostly ignore deadlock for user-level resources and rely on the programmer/application to avoid it, only occasionally offering detection tools.)

---

## 4. Deadlock Prevention

> Prevent deadlock by ensuring **at least one** of the 4 necessary conditions **cannot hold**.

### 4.1 Attacking Mutual Exclusion
- Use locks (mutual exclusion) **only for non-sharable resources**.
- Sharable resources (like **Read-Only files**) can be accessed by multiple processes/threads simultaneously — no need for exclusive locks there.
- **However**, we generally **cannot** eliminate deadlocks purely by denying mutual exclusion, because **some resources are intrinsically non-sharable** (e.g., a printer can't be "shared" mid-print between two jobs).

### 4.2 Attacking Hold & Wait
- To ensure Hold & Wait never occurs, we must guarantee that whenever a process requests a resource, **it doesn't already hold any other resource**.
- **Protocol A**: Each process must **request and be allocated ALL its resources before execution begins**.
- **Protocol B**: Allow a process to request resources **only when it currently holds none**. It can request additional resources only after releasing all resources it currently holds.
- **Drawback:** Low resource utilization (processes over-request upfront) and possible starvation for resource-heavy processes.

### 4.3 Attacking No Preemption
- If a process holding some resources requests another resource that **cannot be immediately allocated**, then **all resources currently held by that process are preempted** (forcibly taken away). The process restarts only when it can regain its old resources plus the newly requested one. **(Live Lock may occur** — processes keep releasing and re-requesting without progress.)
- Alternative: if a process requests resources, first check availability. If not available, check if they're allocated to another process that's *also* waiting for additional resources — if so, **preempt** the desired resource from that waiting process and give it to the requester.

### 4.4 Attacking Circular Wait
- Ensure this condition never holds by imposing a **proper (total) ordering** of resource allocation.
- **Example:** If P1 and P2 both require R1 and R2, force **both** processes to always request in the same order — e.g., both must lock R1 before R2. Whichever process locks R1 first will proceed to get R2, and the other simply waits for R1 — **no cycle can form**.

**Real-life analogy:** This is exactly like the fix for the two-car-on-a-bridge deadlock — establish a **traffic light rule**: cars can only enter the bridge from the North side on even minutes, and South side on odd minutes. By imposing a global ordering/rule, you eliminate the possibility of a circular standoff.

---

## 5. Deadlock Avoidance

> Idea: the kernel is given **advance information** about which resources a process will use during its lifetime. Using this, the system can decide, for **each individual request**, whether the process should wait.

- To decide whether a current request can be satisfied or must be delayed, the system considers:
  - Resources currently available.
  - Resources currently allocated to each process.
  - Future requests and releases of each process.
- Goal: schedule processes and their resource allocation such that **deadlock never occurs**.

### Safe State vs Unsafe State

| Term | Meaning |
|---|---|
| **Safe State** | A state where the system can allocate resources to each process (up to its max. need) **in some order**, and still avoid deadlock. A system is in a safe state **only if a safe sequence exists**. |
| **Unsafe State** | The OS **cannot prevent** processes from requesting resources in a way that leads to deadlock. **Important:** not all unsafe states are deadlocks — an unsafe state **may** lead to deadlock, but doesn't guarantee it. |

- **Key rule of deadlock avoidance:** Whenever a resource request is made, it's only **approved** if the resulting state is a **safe state**. Otherwise, the process must wait.

### Banker's Algorithm
- When a process requests a set of resources, the system determines whether allocating those resources would **leave the system in a safe state**.
  - If **yes** → resources may be allocated.
  - If **no** → the process must wait until other processes release enough resources.

**Real-life analogy:** Think of a **bank** (hence the name!) that has a fixed amount of cash to lend. Before approving *any* new loan, the bank checks: "If I give this loan, and assuming every current borrower eventually asks for their maximum stated loan amount, can I still satisfy everyone in *some* order without going bankrupt?" If yes → approve the loan (safe state). If no → deny/delay the loan request (would risk an unsafe state), even if the bank *currently* has the cash on hand. It's a conservative, forward-looking check.

---

## 6. Deadlock Detection

> Used by systems that **haven't** implemented prevention or avoidance — they let deadlocks potentially happen, then **detect** and **recover**.

### 6.1 Single Instance of Each Resource Type → Wait-For Graph Method
- A deadlock exists **if and only if there is a cycle** in the **wait-for graph**.
- To detect deadlock, the system maintains the wait-for graph and **periodically** invokes a cycle-detection algorithm on it.

### 6.2 Multiple Instances of Each Resource Type
- Uses a generalized version of the **Banker's Algorithm** for detection (checking if the current allocation state can reach completion for all processes; if not, a deadlock exists among the "stuck" processes).

---

## 7. Recovery from Deadlock

Once detected, the system needs to recover:

### 7.1 Process Termination
- **Abort all deadlocked processes** — simple, but wastes a lot of work (all partial progress lost).
- **Abort one process at a time** until the deadlock cycle is eliminated — safer, but requires repeated deadlock-detection runs to check if the cycle is broken after each termination.

### 7.2 Resource Preemption
- To eliminate deadlock, **successively preempt some resources** from processes and give them to other processes, until the deadlock cycle is broken.
- Considerations: which resource/process to select as a victim (cost minimization), how to roll the preempted process back safely, and preventing **starvation** (the same process shouldn't always be picked as the victim).

**Real-life analogy:** Recovery is like a traffic jam caused by cars blocking an intersection from all 4 directions (gridlock). "Recovery" options:
- **Process termination** = a tow truck removes some cars entirely from the intersection (harsh, but frees things up).
- **Resource preemption** = a traffic cop forces one car to reverse out of the intersection temporarily (preempting its "held resource" — its position), letting others move, then it re-enters later.

---

## Interview Q&A — Deadlocks

**Q1: What are the 4 necessary conditions for deadlock, and why do we need all 4 simultaneously?**
> Mutual Exclusion, Hold & Wait, No Preemption, and Circular Wait. All 4 are individually *necessary* — if even one is broken, deadlock cannot occur (this is the entire basis of deadlock *prevention* strategies, which each target breaking one specific condition).

**Q2: What's the difference between deadlock prevention and deadlock avoidance?**
> **Prevention** takes a static, structural approach — it changes the rules of the system (e.g., always request all resources upfront, or impose a strict resource ordering) so that one of the 4 conditions can *never* occur, regardless of runtime behavior. **Avoidance** is dynamic — it uses runtime information (like the Banker's Algorithm) to evaluate *each* resource request against the current system state, granting it only if it keeps the system in a "safe state." Avoidance is more flexible but requires knowing resource needs in advance and has more runtime overhead.

**Q3: Is every unsafe state a deadlock?**
> No. An unsafe state simply means the OS *can't guarantee* deadlock won't happen — it doesn't mean deadlock has *already* happened or definitely *will* happen. It's possible for processes in an unsafe state to still finish without ever deadlocking, depending on the actual sequence of requests that occurs.

**Q4: How would you detect a deadlock in a real production system, e.g., a database?**
> Databases like PostgreSQL/MySQL maintain an internal **wait-for graph** of transactions waiting on locks held by other transactions. They periodically run cycle detection; if found, the database automatically picks a "victim" transaction (often the one with the least work done, or based on a configurable priority) and **aborts/rolls it back**, releasing its locks so the others can proceed — this is deadlock **detection + recovery** applied in the real world, exactly like the OS-level version.

**Q5: Real-world example of "resource ordering" deadlock prevention?**
> In multi-threaded applications, a common rule to avoid deadlocks when locking multiple mutexes is: "always acquire locks in the same global order" (e.g., always lock the object with the smaller memory address / lower ID first). This directly implements the **circular wait prevention** technique — if every thread follows the same ordering, a cycle in the wait-for graph can never form.

**Q6: Why might a system deliberately choose the "Ostrich Algorithm" (ignore deadlocks) instead of prevention/avoidance?**
> Because prevention and avoidance come with real costs — reduced resource utilization (Hold & Wait prevention), computational overhead (Banker's Algorithm on every request), and reduced concurrency. For systems where deadlocks are *rare* in practice (like most desktop OSes for general resource use), it's often more efficient to just occasionally reboot/restart a stuck process than to pay the constant overhead of avoidance on every single request. This is a classic engineering cost-benefit trade-off.

---

**Next up (File 9):** Concurrency Coding Interview Problems — Print FooBar Alternately, Print Zero Even Odd, Building H2O (classic LeetCode concurrency problems with C++ solutions explained).
