# 01 — Introduction to Operating Systems

## 1. Software Landscape

- **Application software**: Performs a specific task for the user (e.g., MS Word, Chrome, Spotify).
- **System software**: Operates and controls the computer system, and provides a platform for application software to run on (e.g., OS, device drivers, firmware).

## 2. What is an Operating System?

> An **Operating System (OS)** is a piece of software that manages all the resources of a computer system — both hardware and software — and provides an environment in which the user can execute programs conveniently and efficiently, by **hiding the underlying complexity of hardware** and acting as a **resource manager**.

Think of the OS as a **hotel manager**: guests (applications) don't deal directly with electricians, plumbers, or housekeeping staff (hardware). They just ask the manager (OS) for what they need, and the manager coordinates all the resources behind the scenes.

## 3. Why Do We Need an OS?

### What if there were no OS?
1. **Bulky and complex applications** — every app would need to contain its own hardware-interaction code (its own disk driver, keyboard driver, etc.). Imagine every app you install having to ship its own printer driver.
2. **Resource exploitation by a single app** — one greedy program could hog the CPU/memory/disk forever, starving everything else.
3. **No memory protection** — one app could read/write another app's memory, or crash the entire system.

### What is an OS made up of?
- A **collection of system software** — kernel, drivers, shell, system utilities, libraries, etc.

## 4. Core Functions of an OS

| Function | Description |
|---|---|
| Access to hardware | Provides controlled access to CPU, memory, disk, I/O devices |
| Interface | Acts as a bridge between the user and the computer hardware |
| **Resource management** (aka *Arbitration*) | Manages memory, devices, files, security, processes, etc. |
| **Abstraction** | Hides the underlying complexity of hardware |
| Isolation & protection | Lets multiple applications run safely without interfering with each other |

### The OS Layer Model
```
        User
          |
Application programs
          |
   Operating System
          |
   Computer Hardware
```

**Real-life analogy:** Think of a smartphone. You tap "Camera" (application) — you don't know or care whether it's using a Sony or Samsung sensor, or how the flash driver works. Android/iOS (the OS) abstracts all that and just gives the camera app access to "take a photo."

## 5. Goals of an OS

- **Maximum CPU utilization** — keep CPU busy as much as possible, avoid idling.
- **Less process starvation** — every process eventually gets a fair chance to run.
- **Higher priority job execution** — important jobs (e.g., system processes, real-time tasks) get preference.

---

# Types of Operating Systems

| Type | Example System | Era |
|---|---|---|
| Single process OS | MS-DOS | 1981 |
| Batch-processing OS | ATLAS, Manchester Univ. | Late 1950s – early 1960s |
| Multiprogramming OS | THE (Dijkstra) | Early 1960s |
| Multitasking OS | CTSS, MIT | Early 1960s |
| Multi-processing OS | Windows NT | — |
| Distributed OS | LOCUS | — |
| Real-Time OS (RTOS) | ATCS (Air Traffic Control System) | — |

```
                 Types of OS
                     |
   -----------------------------------------------------
   |        |            |             |         |     |
Single   Batch     Multi-        Multi-      Multi-  Distributed  Real-Time
process processing programming  tasking    processing              OS
```

### 5.1 Single Process OS
- Only **1 process** executes at a time from the ready queue.
- The **oldest** kind of OS.
- **Real-life analogy:** A single cashier at a small shop — serves one customer completely before moving to the next. No multitasking at all.

### 5.2 Batch-Processing OS
**Steps:**
1. User prepares a job using punch cards.
2. Job is submitted to the computer operator.
3. Operator collects jobs from different users and sorts them into **batches** with similar needs.
4. Operator submits batches to the processor one by one.
5. All jobs in a batch execute together.

**Drawbacks:**
- Priorities can't be set once a batch starts.
- Can lead to **starvation** (a batch might take a long time to complete).
- CPU can go idle during I/O operations (since nothing else can run meanwhile).

**Real-life analogy:** A print shop that only prints when it has collected 50 documents together, then prints all 50 back-to-back — even if your one-page doc was ready 10 minutes ago, you wait for the batch.

```
User1 -> Job1 -\
User2 -> Job2 --\                Batch2      Batch1      [CPU + OS + User Program]
  ...            -> Operator -> [Job7,Job5,Job2] [Job4,Job1] -> executed one batch at a time
UserN -> JobN -/
```

### 5.3 Multiprogramming OS
- Increases **CPU utilization** by keeping **multiple jobs (code + data)** in memory simultaneously, so the CPU always has something to execute when the current job goes to I/O wait.
- Single CPU.
- Uses **context switching** between processes.
- Switch happens when the current process goes into a **wait state**.
- Reduces CPU idle time.

**Real-life analogy:** A chef cooking multiple dishes at once — while rice is boiling (I/O wait), the chef chops vegetables for another dish instead of just staring at the pot.

### 5.4 Multitasking OS
- Logical extension of multiprogramming.
- Single CPU, but can run **more than one task simultaneously** (via fast context switching + time sharing).
- Increases **responsiveness**.
- Further reduces CPU idle time.

**Real-life analogy:** Your laptop today — you have Chrome, Spotify, and a code editor open, all seemingly running "at once" though there's really just fast switching underneath.

### 5.5 Multi-Processing OS
- **More than 1 CPU** in a single computer.
- **Increases reliability**: if 1 CPU fails, others can keep working.
- **Better throughput**.
- **Lesser process starvation**: if 1 CPU is busy, another CPU can pick up waiting work.

**Real-life analogy:** A restaurant kitchen with 2 chefs instead of 1 — twice the dishes can be prepared in parallel, and if one chef takes a break, the other keeps the kitchen running.

### 5.6 Distributed OS
- OS manages many bunches of resources: `>=1` CPUs, `>=1` memory units, `>=1` GPUs, etc., spread across machines.
- **Loosely connected, autonomous, interconnected computer nodes**.
- A collection of independent, networked, communicating, and **physically separate** computational nodes that appear to users as a single coherent system.

**Real-life analogy:** Google Search — your query might be processed by servers spread across multiple data centers worldwide, but it *feels* like one system to you.

### 5.7 Real-Time OS (RTOS)
- Guarantees **real-time, error-free computations within tight time boundaries**.
- Used in: Air Traffic Control systems, Robotics, pacemakers, anti-lock braking systems (ABS).
- **Hard RTOS**: missing a deadline is a total system failure (e.g., airbag deployment).
- **Soft RTOS**: missing a deadline degrades quality but isn't catastrophic (e.g., video streaming).

**Real-life analogy:** An airbag control system — if it deploys the airbag 2 seconds late "because the CPU was busy," that's not just annoying, it's fatal. Compare this to your laptop lagging for 2 seconds while opening a file — annoying, not dangerous.

---

## Interview Q&A — Introduction to OS

**Q1: What is an Operating System, in one line?**
> System software that manages hardware/software resources and provides a convenient, protected environment for programs to run.

**Q2: Why can't applications talk directly to hardware?**
> Because that would mean (a) every app duplicating hardware-interaction code, (b) no fairness in resource sharing, and (c) no memory/security protection between programs — one buggy or malicious app could crash or corrupt the whole system.

**Q3: Difference between multiprogramming and multitasking?**
> Multiprogramming is about **keeping the CPU busy** by having multiple jobs loaded in memory (goal: maximize CPU utilization, driven by I/O waits). Multitasking is about **giving the illusion of parallel execution** to the user via fast context switching and time-sharing (goal: responsiveness). Multitasking is essentially multiprogramming + time-sharing.

**Q4: Give a real-world example of an RTOS and explain why a normal OS (like Windows) wouldn't work there.**
> A pacemaker or airbag system needs *guaranteed* response within milliseconds — a general-purpose OS like Windows might delay a task because it's busy running an antivirus scan or updating, which is unacceptable when a deadline miss can cost a life. RTOS schedulers guarantee deadline-bound execution.

**Q5: What's the difference between a Distributed OS and a Multiprocessing OS?**
> Multiprocessing = multiple CPUs **within a single machine**, sharing the same memory. Distributed OS = multiple **independent, physically separate machines** connected over a network, each with their own memory, working together and communicating via message passing.

---

**Next up (File 2):** Programs, Processes, Threads, Kernel Architecture & System Calls.
