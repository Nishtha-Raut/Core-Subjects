# 19 — Final Interview Cheat Sheet (Rapid Revision Across All Topics)

> Use this as your **last-minute scan** before an interview. Every line links back to a full file (01-18) if you need to re-read the detail. Organized for speed, not depth.

---

## Quick-Fire Definitions

| Term | One-liner |
|---|---|
| **OS** | Software managing hardware/software resources, abstracting complexity, providing a safe environment for programs (File 01) |
| **Program** | Compiled code on disk, not running (File 02) |
| **Process** | Program under execution, in RAM, has its own memory space (File 02) |
| **Thread** | Lightweight execution path within a process; shares memory with sibling threads (File 02) |
| **Kernel** | Core OS component with direct hardware access, Ring 0 (File 02, 17) |
| **System Call** | The only gateway from user mode to kernel mode (File 02) |
| **PCB** | Data structure storing a process's state, PC, registers, priority, etc. (File 04) |
| **Context Switch** | Saving old process/thread state, loading new one — pure overhead (File 04) |
| **Zombie Process** | Finished but not yet reaped by parent's `wait()` (File 04) |
| **Orphan Process** | Parent died, adopted by `init` (File 04) |
| **Race Condition** | Two+ threads access/modify shared data concurrently, outcome depends on timing (File 06) |
| **Critical Section** | Code segment accessing shared resources (File 06) |
| **Mutex** | Binary lock, exactly 1 thread in at a time, same thread must release (File 06) |
| **Semaphore** | Counting synchronization primitive, up to N threads, any thread can signal (File 06) |
| **Deadlock** | Circular wait for resources, no process can proceed (File 08) |
| **Logical/Virtual Address** | CPU-generated address, translated by MMU (File 10) |
| **Physical Address** | Actual RAM location, never directly seen by user (File 10) |
| **Paging** | Fixed-size non-contiguous memory allocation (pages ↔ frames) (File 11) |
| **Segmentation** | Variable-size, logically-meaningful memory division (File 11) |
| **TLB** | Hardware cache for page-table lookups, speeds up paging (File 11) |
| **Page Fault** | Accessing a page marked invalid (not in RAM) → trap to OS (File 12) |
| **Thrashing** | System spends more time page-faulting than executing (File 12) |
| **Inode** | File metadata + data block pointers, no filename stored (File 13) |
| **Hard Link** | Second name, same inode | **Soft Link** | Path reference, breaks if target moves (File 13) |
| **IPC** | Shared Memory (fast, unsafe by default) vs Message Passing (safer, slower) (File 15) |
| **Namespace** | Isolated *view* of resources for a container process (File 18) |
| **Cgroup** | Resource *limit* for a container/process group (File 18) |

---

## Scheduling Algorithms At a Glance (File 05)

| Algorithm | Preemptive? | Starvation Risk? | Key Trait |
|---|---|---|---|
| FCFS | No | Yes (convoy effect) | Order of arrival |
| SJF (non-preemptive) | No | Yes | Shortest burst first |
| SRTF (preemptive SJF) | Yes | Less | Best average wait time |
| Priority (non-preemptive) | No | Yes (needs aging) | Fixed priority at creation |
| Priority (preemptive) | Yes | Yes (needs aging) | Higher priority preempts running job |
| Round Robin | Yes | No | Fair, TQ-based, most popular |
| MLQ | Yes (between queues) | Yes for low queues | Fixed queue assignment |
| MLFQ | Yes | No (uses aging) | Processes move between queues |

**Golden rule:** SJF/SRTF = best average wait but needs future knowledge (impossible) → **Round Robin/MLFQ** = practical real-world default.

---

## Deadlock — The 4 Conditions & How to Break Each (File 08)

| Condition | Prevention Strategy |
|---|---|
| Mutual Exclusion | Use locks only on truly non-sharable resources |
| Hold & Wait | Request all resources upfront, OR release all before requesting more |
| No Preemption | Forcibly preempt resources from waiting processes |
| Circular Wait | Impose a global resource-ordering (always lock A before B) |

**Avoidance:** Banker's Algorithm — only grant a request if the resulting state is still "safe."
**Detection:** Wait-for graph cycle detection (single instance) / generalized Banker's (multiple instances).
**Recovery:** Kill process(es), or preempt resources from a victim.

---

## Page Replacement Algorithms At a Glance (File 12)

| Algorithm | Needs future knowledge? | Belady's Anomaly? | Practical use |
|---|---|---|---|
| FIFO | No | **Yes** | Simple but suboptimal |
| Optimal | Yes (impossible) | No | Theoretical benchmark only |
| LRU | No (uses recent past) | No | **Most used in practice** |
| LFU / MFU | No | No | Rarely used |

---

## Classic Synchronization Problems — Which Primitive Solves What (Files 07, 09)

| Problem | Core Primitives Used | Key Insight |
|---|---|---|
| Producer-Consumer | `mutex` + `empty`/`full` counting semaphores | Never `wait(mutex)` before `wait(empty/full)` — avoids deadlock |
| Reader-Writer | `mutex` (protects read_count) + `wrt` (binary, shared lock) | Only first reader locks `wrt`, only last reader unlocks it |
| Dining Philosophers | 5 binary semaphores (forks) | Needs an extra rule (odd-even, or 4-max-seated) to avoid deadlock — semaphores alone aren't enough! |
| FooBar / ZeroEvenOdd / H2O | mutex + condition_variable (or semaphores) | Use `while` not `if` around `cv.wait()` — guards against spurious wakeups |

---

## Memory Allocation Comparison (Files 10, 11)

| Scheme | Fragmentation Type | Access Speed | Flexibility |
|---|---|---|---|
| Fixed Partitioning | Internal | Fast | Low (fixed partition sizes) |
| Dynamic Partitioning | External | Fast | Medium |
| Paging | None (fixed-size units) | Slower (needs TLB to help) | High |
| Segmentation | External | Fast within segment | High, logically meaningful |

**Free space allocation strategies:** First Fit (fast, simple) > Next Fit (spreads search) > Best Fit (least internal frag, but slow + creates tiny unusable holes) > Worst Fit (slow, mixed results).

---

## Disk Scheduling Comparison (File 14)

FCFS (simple, zigzags) → SSTF (greedy nearest, starvation risk) → SCAN (elevator, sweeps both ways) → LOOK (SCAN but stops at last request, not disk edge) → C-SCAN (one direction + jump back, uniform wait) → **C-LOOK (best of both: one direction + jump to first pending request)**.

---

## The "Explain Like I'm Interviewing" 60-Second Answers

**"What happens when you double-click an application icon?"**
> The OS's shell/GUI triggers `fork()` + `exec()` system calls (File 02). `fork()` creates a new process (child, inherits parent's memory via copy-on-write — File 18); `exec()` replaces that child's memory image with the new program's code/data (File 04's process architecture: text/data/heap/stack). The process starts in the **New** state, moves to **Ready**, then the **Short-Term Scheduler** (File 04/05) picks it to run based on the active scheduling algorithm.

**"What happens when you access a webpage while your RAM is nearly full?"**
> The browser process requests memory; if physical frames aren't available, the OS may need to **swap out** (File 04) a less-active process's pages to disk to free frames — or trigger the **page replacement algorithm** (File 12, likely LRU-based) to evict pages from the current working set. If this happens excessively across many processes competing for insufficient RAM, you get **thrashing** (File 12) — visible as system-wide slowdown.

**"Explain a deadlock you might encounter in real backend code, and how to prevent it."**
> Two database transactions each trying to lock two rows in opposite order — Transaction A locks Row 1 then wants Row 2, Transaction B locks Row 2 then wants Row 1 — classic circular wait (File 08). Prevention: enforce a **consistent lock ordering** (always lock the lower row ID first) across all code paths, directly breaking the circular wait condition.

**"How would you debug a slow production server?"**
> Structured OS-driven approach (File 16): check load average (CPU scheduling, File 05) → check memory/swap activity (thrashing, File 12) → check process states for zombies/D-state (File 04, File 14 disk I/O) → check file descriptor limits (File 13/16) → `strace` the specific process (File 02/16) → check disk I/O patterns (File 14).

**"VM vs Container — which would you choose and when?"**
> VMs for **strong isolation** needs (multi-tenant untrusted workloads, different OS types needed) despite heavier resource cost (File 18). Containers for **fast, lightweight, elastic microservices** where all workloads trust the same kernel and speed/density matters more than maximal isolation.

---

## Formulas to Remember (File 05)

```
Turnaround Time (TAT) = Completion Time (CT) - Arrival Time (AT)
Wait Time (WT)         = TAT - Burst Time (BT)
Response Time           = Time of first CPU allocation - Arrival Time
```

---

## Common "Gotcha" Interview Follow-Ups (things people forget)

1. **"Isn't a mutex just a semaphore with count=1?"** → Conceptually similar (binary semaphore), but a **true mutex** has **ownership** — only the thread that locked it can unlock it, and most implementations detect misuse. A semaphore has **no ownership concept** — any thread can call `signal()`, even one that never called `wait()`. (File 06)

2. **"Why `while` not `if` around `cv.wait()`?"** → Spurious wakeups. Always re-check the condition after waking. (File 09)

3. **"Why does SJF minimize average wait time but isn't used as-is in real systems?"** → Requires knowing future burst times, which is fundamentally impossible to guarantee — real systems approximate with history-based heuristics or use Round Robin/MLFQ instead. (File 05)

4. **"Doesn't more RAM always fix thrashing?"** → Usually yes, but the *real* root cause is: allocated frames < working set size for the *concurrent* set of active processes. More RAM helps by raising available frames, but reducing the degree of multiprogramming (fewer concurrent processes) is the more surgical fix. (File 12)

5. **"Is a zombie process a memory leak?"** → Not exactly — a zombie's *memory* is already freed; only its small **process table entry (PCB)** lingers. But at scale, accumulated zombies **can exhaust the process table**, effectively becoming a resource leak. (File 04)

6. **"Do containers provide zero isolation then, since they share a kernel?"** → No — namespaces + cgroups provide **real, meaningful isolation** (process, network, mount, resource limits) — just not as strong as full kernel-per-VM isolation. It's a spectrum, not binary. (File 18)

---

## Suggested Final Review Order (if short on time)

1. File 06 (Concurrency Basics) + File 08 (Deadlocks) — most commonly probed in-depth in interviews.
2. File 05 (CPU Scheduling) — algorithm comparisons come up constantly.
3. File 12 (Virtual Memory/Thrashing) — frequently tied to real debugging scenarios.
4. File 09 (Concurrency Coding Problems) — practice actually writing FooBar/H2O from memory.
5. File 16 (Linux Practical) — great for "connect theory to practice" style questions.
6. Skim everything else once for recognition-level familiarity.

---

**That's all 19 files!** Your complete OS notes set now covers:
- Files 01-12: Your original notes, fully expanded with analogies + interview Q&A.
- Files 13-18: New foundational topics (File Systems, Disk Scheduling, IPC, Linux Practical, Security, Virtualization) that weren't in your original notes but are commonly tested.
- File 19: This rapid revision cheat sheet.

Good luck with your interview prep! Feel free to come back and ask for deeper dives, mock Q&A drilling, or diagrams on any specific topic.
