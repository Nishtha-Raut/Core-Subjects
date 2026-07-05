# 16 — Linux/OS Practical Concepts for Interviews *(New Topic — Not in Original Notes)*

> Interviewers love asking practical "connect the theory to real tools" questions. This file bridges everything from Files 1-15 to actual Linux commands and behaviors you can talk about confidently.

## 1. File Descriptors

- A **file descriptor (FD)** is a small non-negative integer that a process uses to refer to an **open file** (or socket, pipe, device — "everything is a file" in Unix philosophy).
- Every process starts with 3 standard FDs:

| FD | Name | Purpose |
|---|---|---|
| `0` | `stdin` | Standard input |
| `1` | `stdout` | Standard output |
| `2` | `stderr` | Standard error |

- The OS maintains a **per-process file descriptor table**, which points into a **system-wide open file table**, which points to the actual **inode** (File 13).

```
Process FD Table          System-wide Open File Table       Inode
  0 -> stdin  ------\
  1 -> stdout ------->  [offset, flags, ...]  ------------->  [actual file data on disk]
  2 -> stderr ------/
  3 -> myfile.txt --->  [offset, flags, ...]  ------------->  [myfile.txt's data]
```

**Real-life analogy:** A file descriptor is like a **coat-check ticket number** at a restaurant. You (the process) don't hold your actual coat (the file) — you hold a small ticket number (FD `3`, say). The coat-check counter (system-wide open file table) uses that number to know exactly which coat (inode/data) belongs to you. Multiple people (processes) can have "ticket #3" simultaneously because each restaurant location (process) has its **own independent numbering**.

### Why does `Too many open files` error happen?
- Each process has a **limit** on the number of file descriptors it can have open simultaneously (configurable via `ulimit -n` on Linux).
- A common real-world bug: **forgetting to close files/sockets/connections** after use — over time, the process accumulates open FDs (a "file descriptor leak") until it hits the limit and starts failing.

---

## 2. `ps` and `top` — Process Monitoring

### `ps aux` — Process Snapshot
Common columns and what they map to from earlier files:

| Column | Meaning | Related Concept |
|---|---|---|
| `PID` | Process ID | Process attributes (File 4) |
| `PPID` (via `ps -ef`) | Parent Process ID | Orphan/Zombie processes (File 4) |
| `STAT` | Process state (`R`=running, `S`=sleeping, `D`=uninterruptible sleep, `Z`=**zombie**, `T`=stopped) | Process States (File 4) |
| `%CPU` | CPU utilization | CPU Scheduling (File 5) |
| `%MEM` | Memory usage | Memory Management (Files 10-12) |
| `VSZ` / `RSS` | Virtual memory size / Resident set size (actual RAM used) | Virtual Memory (File 12) — VSZ can be much larger than RSS precisely *because* of demand paging (not all virtual pages are actually resident in RAM)! |

### `top` / `htop` — Live Process Monitoring
- Shows real-time CPU/memory usage, updated periodically.
- **Load Average** (shown at the top): a rolling average of the number of processes either **running** or in the **uninterruptible-wait (D state, usually I/O wait)** queue, over the last 1, 5, and 15 minutes.
  - A load average of `4.0` on a **4-core** machine means the CPUs are, on average, fully utilized (roughly one runnable process per core).
  - A load average of `8.0` on that same 4-core machine means processes are, on average, **queued up waiting** — directly tying back to the **Ready Queue** concept (File 4) and **CPU Scheduling** (File 5): more runnable processes exist than there are CPUs to run them right now.

**Real-life analogy:** Load average is like the number of people waiting in line at a coffee shop with 4 baristas (cores). If there are consistently 4 people in line, the shop is exactly saturated. If there are consistently 8, customers are waiting roughly double the time they'd like — an overloaded system.

---

## 3. Memory as Seen in Linux Tools

### `free -h`
Shows system-wide memory:
```
              total        used        free      shared  buff/cache   available
Mem:           16Gi        6.0Gi       2.0Gi       500Mi       8.0Gi       9.0Gi
Swap:          4.0Gi          0B       4.0Gi
```
- `buff/cache`: memory used by the OS for **disk caching** (File 3's memory hierarchy concept in action!) — this memory is **reclaimable** on demand, so it's not "wasted," it's the OS opportunistically using spare RAM to speed up future disk reads.
- `Swap`: the actual **swap space** used for demand paging (File 12) when physical RAM is exhausted.

### `vmstat`
- Shows `si`/`so` (swap-in / swap-out) columns — directly observing **page fault / thrashing behavior** (File 12) in real time. Consistently high `si`/`so` values are the practical symptom of thrashing.

---

## 4. `strace` — Watching System Calls Live

- `strace` is a tool that **traces the system calls** a process makes in real time (directly observing the theory from File 2 — user mode ↔ kernel mode transitions via system calls!).

```bash
$ strace ls
execve("/bin/ls", ["ls"], ...) = 0
openat(AT_FDCWD, ".", O_RDONLY|...) = 3
getdents64(3, ...) = 120
close(3)                          = 0
write(1, "file1.txt  file2.txt\n", 22) = 22
```
- You can literally **see** `open()`, `read()`, `write()`, `close()` — the exact system calls from File 2's table — being invoked as `ls` runs.

**Real-life analogy:** `strace` is like being a fly on the wall inside the bank (File 2's analogy), watching every single "withdrawal slip" (system call) a customer (process) submits to the teller (kernel), in real time — invaluable for debugging *why* a program is slow or behaving unexpectedly (e.g., seeing it repeatedly fail to `open()` a config file it can't find).

---

## 5. Nice Values & Process Priority

- `nice` / `renice` commands let you influence a process's **scheduling priority** (directly ties to Priority Scheduling, File 5).
- Range: `-20` (highest priority) to `+19` (lowest priority) on Linux. Default is `0`.
- Only root/admin can **increase** priority (lower the nice value); any user can **lower** their own process's priority.

**Real-life analogy:** This is a direct real-world implementation of the **Priority Scheduling** concept from File 5 — you're literally telling the scheduler "let this backup script run at low priority (`nice +15`, be 'nice' to others) so it doesn't compete with my important build job (`nice -5`)."

---

## 6. Understanding `kill` and Signals in Practice

- `kill -9 <PID>` sends `SIGKILL` (File 15) — the process is terminated **immediately, unconditionally**, no cleanup possible.
- `kill -15 <PID>` (or just `kill <PID>`) sends `SIGTERM` — a **polite request** the process **can** catch and handle gracefully (e.g., flush buffers, close database connections) before exiting.

**Interview-relevant nuance:** Always try `SIGTERM` first in production (`kill <PID>` or `docker stop`) to allow graceful shutdown, and only escalate to `SIGKILL` (`kill -9`) if the process doesn't respond within a reasonable timeout — this is exactly the philosophy Kubernetes/Docker use when stopping containers (`SIGTERM`, then `SIGKILL` after a grace period).

---

## 7. Context Switch Overhead in Practice

- `vmstat` also reports a `cs` (context switches per second) column.
- A very high `cs` count relative to your workload can indicate **excessive context switching** (File 4/5's overhead concept) — e.g., too many threads competing for too few cores, or a poorly-tuned time quantum, or lock contention causing threads to constantly sleep/wake.

---

## 8. Putting It All Together — A Realistic Debugging Scenario

> **Interview-style prompt:** *"Our production server is slow. Walk me through how you'd investigate, using OS concepts."*

**A strong structured answer:**
1. **Check load average** (`top`/`uptime`) — is it a **CPU scheduling** problem (File 5)? High load relative to core count = processes queuing for CPU.
2. **Check memory** (`free -h`, `vmstat`) — is the system **thrashing** (File 12)? High `si`/`so` swap activity = not enough RAM for the working set.
3. **Check process states** (`ps aux` for `Z` zombies or `D` uninterruptible sleep) — zombies (File 4) suggest a parent isn't reaping children; lots of `D`-state processes suggest I/O bottleneck, possibly disk scheduling (File 14) related.
4. **Check open file descriptors** (`lsof`, `ulimit -n`) — is a process **leaking FDs** (Section 1 above)?
5. **`strace` the specific slow process** — is it stuck repeatedly retrying a failing system call (File 2)?
6. **Check disk I/O** (`iostat`) — are we seek-bound on an HDD (File 14's disk scheduling concepts)?

> Framing your answer this way in an interview demonstrates you understand **how the theoretical OS concepts (scheduling, memory, processes, file systems) map directly onto real diagnostic tools** — which is exactly what separates candidates who've memorized definitions from those who can actually apply the knowledge.

---

## Interview Q&A — Practical Linux/OS Concepts

**Q1: What's the difference between VSZ and RSS in `ps` output, and how does it relate to virtual memory?**
> VSZ (Virtual Size) is the total virtual address space a process has reserved — including pages that may **never actually be loaded into RAM**. RSS (Resident Set Size) is the actual physical RAM currently occupied by the process. VSZ can be far larger than RSS precisely because of **demand paging** (File 12) — a process might have gigabytes of virtual address space mapped, but only megabytes actually resident in RAM at any given moment.

**Q2: Why would you use `SIGTERM` before `SIGKILL` when stopping a production service?**
> `SIGTERM` gives the process a chance to gracefully clean up — flush write buffers, close database connections, finish in-flight requests — preventing data corruption or dropped work. `SIGKILL` terminates immediately with **zero cleanup opportunity**, which can leave files half-written or connections in a bad state. Best practice: send `SIGTERM`, wait a grace period, escalate to `SIGKILL` only if the process hasn't exited (exactly what `docker stop` and Kubernetes pod termination do by default).

**Q3: A process shows as `<defunct>` in `ps`. What is it, and how do you fix it?**
> That's a **zombie process** (File 4) — it's finished executing but its exit status hasn't been reaped by its parent via `wait()`. You can't directly "kill" a zombie (it's already dead — there's no process left to signal). The fix is to address the **parent process**: either it needs code fixed to call `wait()`/`waitpid()`, or if the parent itself is unresponsive, restarting the parent lets `init`/`systemd` adopt and reap the zombie.

**Q4: What does a high load average with low CPU usage typically indicate?**
> This often means processes are in **uninterruptible I/O wait (D state)** rather than actually needing CPU cycles — since Linux's load average counts both runnable *and* uninterruptible-waiting processes. This points toward a **disk/I/O bottleneck** (File 14) rather than a CPU scheduling bottleneck (File 5) — worth checking `iostat` next.

**Q5: How does `strace` help debug a "hanging" application?**
> By showing the exact **system call** the process is currently blocked on (File 2) — e.g., if it's stuck on a `read()` call to a socket, that suggests it's waiting on a network response; if stuck on `futex()`, that suggests a **lock contention/deadlock** issue (File 8) between threads. This turns a vague "it's hanging" into a precise, actionable diagnosis.

---

**Next up (File 17):** OS Security Basics *(new topic)* — Authentication vs Authorization, Access Control Lists, Privilege Rings, Buffer Overflows, Sandboxing.
