# 04 — Process Management (PCB, States, Queues, Swapping, Context Switching)

## 1. Introduction to Process

- **Program** = compiled code, ready to execute.
- **Process** = program under execution.

### How does the OS create a process?

**Steps:**
1. Load the program & static data into memory.
2. Allocate runtime **stack**.
3. **Heap** memory allocation.
4. Set up I/O tasks (e.g., stdin/stdout).
5. OS hands off control to `main()`.

### Architecture of a Process (Memory Layout)

```
High Address
+-------------------+
|       Stack        |  <- Local variables, function args & return values
|         |           |
|         v           |
|      (grows down)   |
|                     |
|         ^           |
|         |           |
|       Heap          |  <- Dynamically allocated variables (malloc/new)
+-------------------+
|       Data          |  <- Global & Static data
+-------------------+
|       Text          |  <- Compiled code (loaded from disk)
+-------------------+
Low Address
```

**Real-life analogy:** Think of a process's memory layout like an office desk:
- **Text (code)** = the fixed instruction manual bolted to your desk — doesn't change while you work.
- **Data (globals/statics)** = a whiteboard with permanent notes visible throughout your shift.
- **Heap** = a filing cabinet you can add/remove folders from as needed, growing as you take on more work.
- **Stack** = a stack of sticky notes for "what I'm doing right now" — you add one every time you start a sub-task (function call) and remove it when done (function returns).

## 2. Attributes of a Process

- Every process has **unique identifying features**.
- **Process Table**: all processes are tracked by the OS using a table-like data structure.
- Each entry in that table is a **Process Control Block (PCB)**.
- **PCB**: a data structure used for each process, storing info such as process ID, program counter, process state, priority, etc.

### PCB Structure

| Field | Purpose |
|---|---|
| **Process ID** | Unique identifier |
| **Program Counter (PC)** | Address of the next instruction to execute |
| **Process State** | Current state (New/Ready/Running/Waiting/Terminated) |
| **Priority** | Determines how much CPU time the process gets |
| **Registers** | CPU register values for this process |
| **List of open files** | Files currently opened by this process |
| **List of open devices** | Devices currently in use by this process |

### Why do we need Registers in the PCB?
> When a process is running and its time slice expires, the **current values of process-specific registers** are stored in the PCB, and the process is swapped out. When the process is later scheduled to run again, the register values are read from the PCB and written back into the CPU's actual registers. This is the main purpose of registers in the PCB — **preserving execution state across context switches**.

**Real-life analogy:** The PCB is like a **bookmark + sticky note system** for a book you're reading (the process). If you get interrupted (context switch), you don't lose your place — you jot down the page number (program counter), which chapter/section (registers/state), and any relevant notes (open files) so you can resume exactly where you left off.

---

## 3. Process States

As a process executes, it changes **state**. Each process is in one of:

| State | Meaning |
|---|---|
| **New** | OS is about to pick the program & convert it into a process (process being created) |
| **Ready** | Process is in memory, waiting to be assigned to a processor |
| **Run** | Instructions are being executed; CPU is allocated |
| **Waiting** | Waiting for I/O to complete |
| **Terminated** | Process has finished execution; PCB entry removed from process table |

### State Transition Diagram

```
   [new] --admitted--> [ready] --scheduler dispatch--> [running] --exit--> [terminated]
                           ^                                |
                           |                                |
                    I/O or event completion         I/O or event wait
                           |                                |
                           +------------ [waiting] <--------+
                                        <--interrupt--
```

*(An interrupt can also move a process from `running` back to `ready` — e.g., time quantum expiry in preemptive scheduling.)*

**Real-life analogy:** Think of a process's life like ordering food at a busy restaurant:
- **New** = you just walked in, haven't been seated yet.
- **Ready** = you're seated and ready to order, waiting for the waiter to come take your order (waiting for CPU).
- **Running** = the waiter is actively taking your order (CPU is executing your instructions).
- **Waiting** = your order is placed and now you're waiting for the kitchen (I/O) to prepare the food.
- **Terminated** = you've eaten, paid, and left.

---

## 4. Process Queues

| Queue | Contains | Located in | Managed by |
|---|---|---|---|
| **Job Queue** | Processes in **New** state | **Secondary memory** | **Job Scheduler / Long-Term Scheduler (LTS)** — picks processes from the pool and loads them into memory |
| **Ready Queue** | Processes in **Ready** state | **Main memory** | **CPU Scheduler / Short-Term Scheduler (STS)** — picks a process from the ready queue and dispatches it to the CPU |
| **Waiting Queue** | Processes in **Wait** state | Main memory | — |

### Degree of Multi-programming
- The **number of processes in memory** at a given time.
- Controlled by the **LTS (Long-Term Scheduler)**.

### Dispatcher
> The module of the OS that gives **control of the CPU** to a process selected by the **STS (Short-Term Scheduler)**.

**Real-life analogy:** Job Queue vs Ready Queue is like a hospital:
- **Job Queue (secondary memory)** = the waiting room list of patients who've registered but haven't been assigned a hospital bed yet — LTS decides who gets admitted (moved into memory).
- **Ready Queue (main memory)** = patients already in beds, waiting for a doctor (CPU) to attend to them — STS decides which patient the doctor sees next.
- **Dispatcher** = the nurse who actually walks the doctor over to the chosen patient's bed.

---

## 5. Swapping, Context Switching, Orphan & Zombie Processes

### 5.1 Swapping

- Time-sharing systems may have a **Medium-Term Scheduler (MTS)**.
- MTS **removes processes from memory** to reduce the degree of multi-programming (freeing up RAM).
- These removed processes can later be **reintroduced into memory**, continuing execution from where they left off — this is called **Swapping**.
- **Swap-out** and **Swap-in** are done by the MTS.
- Swapping is necessary to improve the process mix or because memory requirements have overcommitted available memory, requiring memory to be freed up.
- Mechanism: a process is temporarily moved out of main memory to secondary storage (disk), freeing that memory for other processes. Later, the system swaps it back in.

```
     [Ready Queue] <---Swap-in--- [Partially executed swapped-out processes]
          |                                    ^
          v                                    | Swap-out
        [CPU] ----------------------------------
          |
          v
    [I/O waiting queue] ---> [I/O]
```

**Real-life analogy:** Swapping is like a hotel that's overbooked. Instead of turning guests away permanently, the manager asks a guest who's mid-stay to temporarily vacate the room and store their luggage (swap to disk), then lets them back into a room later to continue their stay exactly as before.

### 5.2 Context Switching

- Switching the CPU to another process requires:
  1. **State save** of the current process.
  2. **State restore** of a different process.
- The kernel saves the old process's context into its PCB and loads the saved context of the new process scheduled to run.
- It's **pure overhead** — the system does no useful work while switching.
- Speed varies by machine, depending on memory speed, number of registers to copy, etc.

**Real-life analogy:** Imagine you're a surgeon operating on Patient A, and suddenly you're called to handle an emergency with Patient B. Before switching, you must carefully note exactly where you left off with Patient A (save state), scrub in fresh for Patient B (load state) — and none of that note-taking/scrubbing time actually treats either patient (pure overhead).

### 5.3 Orphan Process

- A process whose **parent process has been terminated**, but the child is **still running**.
- Orphan processes are **adopted by the `init` process** (the first process of the OS, PID 1).

**Real-life analogy:** A child whose parent has passed away and gets taken in ("adopted") by a guardian institution (`init`) so they're not left unmanaged.

### 5.4 Zombie Process (Defunct Process)

- A process whose **execution is completed**, but it still has an **entry in the process table**.
- Usually occurs for **child processes** — the parent still needs to read the child's **exit status**. Once done via the `wait()` system call, the zombie entry is removed — this is called **"reaping"** the zombie.
- Happens because the parent may call `wait()` much later, even though the child terminated earlier — the entry lingers as a "zombie" until reaped.
- The process table entry can only be removed **after** the parent reads the exit status.

**Real-life analogy:** A zombie process is like a completed food order sitting on the "ready for pickup" counter — the kitchen (child process) is done cooking, but the ticket (process table entry) can't be cleared until the waiter (parent process) actually comes and picks up the receipt/confirms it (calls `wait()`).

---

## Interview Q&A — Process Management

**Q1: What's stored in a PCB, and why does it matter for context switching?**
> A PCB stores process ID, program counter, process state, priority, register values, open file list, and open device list. It matters because during a context switch, the OS needs to save *all* of this to resume the process later exactly where it left off — without it, switching between processes would corrupt their execution state.

**Q2: What's the difference between the Ready Queue and the Job Queue?**
> Job Queue holds processes in the **New** state sitting in **secondary memory** — not yet loaded into RAM. Ready Queue holds processes already in **main memory**, in the **Ready** state, just waiting for CPU time. The Long-Term Scheduler moves jobs from the Job Queue into the Ready Queue.

**Q3: How do you prevent zombie processes in your own code?**
> In Unix/Linux, the parent process should call `wait()` or `waitpid()` on its child processes to read their exit status, which allows the kernel to remove the zombie's PCB entry from the process table. If a parent never calls `wait()`, zombies accumulate and can eventually exhaust the process table (a real-world resource leak).

**Q4: What happens if a parent process dies before its child?**
> The child becomes an **orphan process** and gets adopted by the `init` process (PID 1), which will periodically call `wait()` on its adopted children to clean up their zombie entries once they finish.

**Q5: Why is context switching considered "pure overhead"?**
> Because the CPU cycles spent saving/loading process state don't perform any of the actual work the process needs done — it's bookkeeping cost. This is why OS designers try to minimize excessive context switching (e.g., choosing an appropriate time quantum in Round Robin scheduling) — too much switching wastes CPU time on overhead instead of real computation.

**Q6: Real-world example: You run `ps aux` on Linux and see a process marked `<defunct>` — what does that mean and what would you do?**
> That's a zombie process — it finished executing but its exit status hasn't been reaped by its parent. I'd check the parent process (via `ps -ef` to find the PPID) — if the parent is buggy and never calls `wait()`, I might need to fix that code, or if the parent itself is stuck/dead, killing and restarting the parent will let `init` clean up the zombie.

---

**Next up (File 5):** CPU Scheduling — FCFS, SJF, Priority Scheduling, Round Robin, MLQ, MLFQ.
