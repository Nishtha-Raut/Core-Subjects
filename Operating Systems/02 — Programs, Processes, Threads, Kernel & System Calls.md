# 02 — Programs, Processes, Threads, Kernel & System Calls

## 1. Program vs Process vs Thread

| Concept | Definition |
|---|---|
| **Program** | An executable file containing a set of instructions to do a specific job. It's **compiled code, ready to execute**, and sits on **disk**. |
| **Process** | A **program under execution**. Resides in the computer's **primary memory (RAM)**. |
| **Thread** | A **single sequence stream within a process** — an independent path of execution. Also called a **light-weight process**. |

**Real-life analogy:**
- **Program** = a recipe written in a cookbook (static, not being cooked yet).
- **Process** = you actually cooking that recipe right now in your kitchen (uses real resources — stove, pan, ingredients).
- **Thread** = if you have two hands, you could be stirring the pot with one hand and chopping vegetables with the other **at the same time**, both part of cooking the *same* dish (same process), sharing the same kitchen (memory).

### Why Threads?
- Used to achieve **parallelism** by dividing a process's tasks into independent paths of execution.
- **Example:** Multiple tabs in a browser, or a text editor — while you type, spell-checking, formatting, and auto-saving all happen **concurrently** via multiple threads of the *same* process.

## 2. Multi-Tasking vs Multi-Threading

| Multi-Tasking | Multi-Threading |
|---|---|
| Execution of more than one **task (process)** simultaneously. | A process divided into several sub-tasks called **threads**, each with its own path of execution. |
| Concept of **more than 1 process** being context-switched. | Concept of **more than 1 thread** being context-switched. |
| No. of CPUs required: **1**. | No. of CPUs: `>= 1` (better with more than 1). |
| **Isolation and memory protection exist.** OS allocates separate memory to each process. | **No isolation / memory protection.** Threads of a process **share** the same memory and resources. |

### Thread Scheduling
- Threads are scheduled based on **priority**.
- Even though threads run "within" a process's runtime, the OS still assigns them **processor time slices**.

### Thread Context Switching vs Process Context Switching

| Thread Context Switching | Process Context Switching |
|---|---|
| OS saves the current thread's state & switches to another thread **of the same process**. | OS saves current process's state & switches to another process by restoring its state. |
| **Does NOT** include switching the memory address space (but PC, registers, stack ARE saved). | **DOES** include switching the memory address space. |
| **Fast** switching. | **Slow** switching. |
| CPU's cache state is **preserved**. | CPU's cache state is **flushed**. |

**Real-life analogy:** Switching between two threads of the same process is like you switching from stirring the pot to tasting the soup — you're still in the same kitchen with the same ingredients laid out. Switching between two *processes* is like leaving your kitchen entirely and walking into a completely different kitchen with different ingredients, tools, and layout — much more overhead.

---

## 3. Components of an OS

### 3.1 Kernel
- The part of the OS that **interacts directly with hardware** and performs the most crucial tasks.
- **Heart / core component of the OS**.
- The **very first part of the OS to load** on startup.

### 3.2 User Space
- Where **application software runs**.
- Apps here **don't have privileged access** to underlying hardware — they must go through the kernel.
- Interfaces: **GUI** and **CLI**.

### 3.3 Shell
- A **shell** (aka command interpreter) is the part of the OS that receives commands from users and gets them executed.
- The two primary types of shells are:
- Command Line Interface (CLI): Text-based interfaces where users type commands (e.g., Bash, Zsh, or Windows PowerShell).
- Graphical User Interface (GUI): Visual interfaces that allow users to interact with the system using windows, icons, and menus (like the Windows desktop or macOS Finder)

### 3.4 Functions of the Kernel

| Function | What it does |
|---|---|
| **Process management** | Scheduling processes/threads on CPUs, creating & deleting user/system processes, suspending/resuming processes, providing synchronization/communication mechanisms |
| **Memory management** | Allocating/deallocating memory as needed, tracking which memory is used by which process |
| **File management** | Creating/deleting files & directories, mapping files to secondary storage, backup support |
| **I/O management** | Managing I/O operations & devices via **buffering, caching, spooling** |

**Buffering vs Caching vs Spooling:**

| Term | Meaning | Example |
|---|---|---|
| **Spooling** |  Queues multiple jobs on a hard disk for slow devices (like printers) so processes don't wait. Multiple jobs are placed in a queue on the disk. As the device finishes one job, the spooler automatically feeds the next one from the disk.| Print spooling, mail spooling |
| **Buffering** | Temporarily holds data in main memory (RAM) to smooth out speed differences between a sender and receiver. A temporary memory space (usually in RAM) holds incoming data until the receiving device or process is ready to process it. | YouTube video buffering |
| **Caching** | Stores a duplicate of frequently accessed data in a high-speed memory layer (like CPU cache) for faster future retrieval. The cache keeps a copy of data that is originally stored elsewhere (like a slower hard drive). When the CPU needs data, it checks the cache first; if it's there (a "cache hit"), it gets it instantly. | Memory caching, Web caching |

---

## 4. Types of Kernels

### 4.1 Monolithic Kernel
- **All** OS functions (process, memory, file, I/O management, drivers) run **inside the kernel itself**, in kernel space.
- ✅ High performance — communication is fast (fewer user-mode ↔ kernel-mode transitions/overheads).
- ❌ Bulky in size.
- ❌ High memory requirement to run.
- ❌ **Less reliable** — if one module crashes, the **whole kernel goes down**.
- **Examples:** Linux, Unix, MS-DOS.

### 4.2 Micro Kernel
- Only the **major/essential** functions live in the kernel: **memory management, process management**.
- **File management and I/O management** are pushed out to **user space**.
- ✅ Smaller in size.
- ✅ More reliable (a crashing file-system module doesn't crash the whole kernel).
- ✅ More stable.
- ❌ **Performance is slower** — more overhead switching between user mode and kernel mode.
- **Examples:** L4 Linux, Symbian OS, MINIX.

### 4.3 Hybrid Kernel
- Combines advantages of both: e.g., file management in user space, rest in kernel space.
- Speed and design closer to monolithic; modularity and stability closer to microkernel.
- IPC still happens, but with **lesser overhead** than a pure microkernel.
- **Examples:** macOS, Windows NT/7/10.

### 4.4 Nano/Exo Kernels
- Even more minimal than microkernels — push almost everything (even device drivers) out of the kernel. (Mentioned briefly in notes — advanced/niche topic.)

**Real-life analogy:**
- **Monolithic kernel** = one giant company where every department (HR, Sales, IT) works in the same open office, sharing everything directly — fast communication, but if a fire breaks out anywhere, the whole office shuts down.
- **Microkernel** = a company where only the CEO's core office (essential decision-making) is centralized, while HR, IT, and Sales are separate outsourced vendors — safer/more modular but communication requires more back-and-forth (calls/emails = context switches).

### IPC — How does User Mode talk to Kernel Mode?
- **Inter-Process Communication (IPC)**.
- Two processes execute independently, each with **independent memory space** (memory protection) — but sometimes they need to communicate to get work done.
- Done via **shared memory** and **message passing**.

---

## 5. System Calls

### How do apps interact with the Kernel?
> Using **system calls**.

**Example — `mkdir laks`:**
- `mkdir` indirectly calls the kernel and asks the file management module to create a new directory.
- `mkdir` is just a **wrapper** around the actual system call.
- `mkdir` interacts with the kernel **using system calls**.

**Example — Creating a process:**
1. User executes a process. *(User Space)*
2. Gets a system call. *(User Space → transition)*
3. Exec system call creates the process. *(Kernel Space)*
4. Returns to User Space.

> **Transitions from User Space to Kernel Space are done by software interrupts.**

> **System calls are implemented in C.**

### Definition
> A **system call** is a mechanism by which a user program can request a service from the kernel **for which it does not have permission to perform itself**.

- User programs typically **can't** directly access I/O devices or communicate with other programs — they need the kernel's help.
- **System calls are the ONLY way a process can transition from user mode to kernel mode.**

```
        User Mode
   -------------------
   |    User App     |
   -------------------
   |     Glibc       |
   ------------------- <-- Software Interrupt (the boundary)
   | System Call      |
   | Interface (SCI)  |
   -------------------
        Kernel Mode
   -------------------
   |     Kernel      |
   -------------------
   |    Hardware     |
   -------------------
```

**Real-life analogy:** Think of a bank. You (user program) can't just walk into the vault (hardware) and take money. You fill out a **withdrawal slip** (system call) and hand it to the teller (kernel), who has the *privilege* to access the vault on your behalf, verifies your request, and returns the result to you.

### Types of System Calls

| Category | Examples |
|---|---|
| **1. Process Control** | end, abort, load, execute, create process, terminate process, get/set process attributes, wait for time, wait/signal event, allocate & free memory |
| **2. File Management** | create file, delete file, open, close, read, write, reposition, get/set file attributes |
| **3. Device Management** | request device, release device, read, write, reposition, get/set device attributes, attach/detach devices |
| **4. Information Maintenance** | get/set time or date, get/set system data, get/set process/file/device attributes |
| **5. Communication Management** | create/delete communication connection, send/receive messages, transfer status info, attach/detach remote devices |

### Windows vs Unix System Call Examples

| Category | Windows | Unix |
|---|---|---|
| Process Control | `CreateProcess()`, `ExitProcess()`, `WaitForSingleObject()` | `fork()`, `exit()`, `wait()` |
| File Management | `CreateFile()`, `ReadFile()`, `WriteFile()`, `CloseHandle()`, `SetFileSecurity()` | `open()`, `read()`, `write()`, `close()`, `chmod()`, `umask()`, `chown()` |
| Device Management | `SetConsoleMode()`, `ReadConsole()`, `WriteConsole()` | `ioctl()`, `read()`, `write()` |
| Information Management | `GetCurrentProcessID()`, `SetTimer()`, `Sleep()` | `getpid()`, `alarm()`, `sleep()` |
| Communication | `CreatePipe()`, `CreateFileMapping()`, `MapViewOfFile()` | `pipe()`, `shmget()`, `mmap()` |

---

## Interview Q&A — Processes, Threads, Kernel, System Calls

**Q1: Why are threads called "lightweight" processes?**
> Because creating/switching a thread doesn't require allocating a new memory address space or re-registering resources — threads of the same process **share** memory, open files, and other resources. Creating a new process, on the other hand, needs a fresh memory space, PCB, etc. — much heavier.

**Q2: If threads share memory, how does that cause bugs? Give a real example.**
> Since threads share memory, if two threads write to the same variable at the same time without synchronization, you get a **race condition**. E.g., two threads incrementing a shared `counter` variable simultaneously in a banking app might both read `counter = 100`, both add 1, and both write back `101` — losing an update, and your account balance becomes wrong. (Covered in-depth in File 6.)

**Q3: Why is a monolithic kernel like Linux still widely used if microkernels are "more reliable"?**
> Because performance matters a lot in practice — the overhead of constant user-mode/kernel-mode switching in microkernels adds latency. Linux mitigates monolithic risk via loadable kernel modules, strict code review, and mechanisms like kernel address sanitization — trading a bit of "pure" reliability for speed. It's an engineering trade-off, not a strict "better/worse."

**Q4: Why can't a user program directly read/write a file on disk without a system call?**
> Because direct hardware access from arbitrary user programs would break isolation and security — any program could corrupt the disk or read another user's files. The system call acts as a **controlled gateway** so the kernel can validate permissions before touching hardware.

**Q5: What triggers the transition from user mode to kernel mode?**
> A **software interrupt** (trap) triggered by a system call. Hardware interrupts (like a keyboard press) can also cause a mode switch, but for *process-initiated* requests, it's specifically the system call mechanism.

**Q6: Real-world example of System Call usage you can explain in an interview:**
> "When I call `open()` in a C program to open a file, my program doesn't directly manipulate the disk. It issues a system call; the kernel checks permissions via the file's ACL, locates the inode, and returns a file descriptor to my process — abstracting away the actual disk I/O."

---

**Next up (File 3):** Boot Process, 32-bit vs 64-bit OS, and Memory Hierarchy (Storage Devices Basics).
