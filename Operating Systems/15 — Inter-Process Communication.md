# 15 — Inter-Process Communication (IPC) Deep Dive *(New Topic — Expands on File 2)*

> Your original notes briefly mentioned IPC = "shared memory and message passing" (File 2). This file expands that into the actual mechanisms used in real systems — a very common interview area, especially "how would two processes talk to each other?"

## 1. Why IPC?

- Processes have **independent memory spaces** (memory protection, from File 2) — that's a *feature*, not a bug, for safety.
- But sometimes processes **need to cooperate**: sharing data, sending notifications, coordinating work.
- **IPC mechanisms** are the OS-provided, controlled ways for processes to communicate **without breaking memory isolation**.

Two broad categories:
1. **Shared Memory** — processes share a region of memory directly.
2. **Message Passing** — processes exchange messages via the kernel (no shared memory).

```
Shared Memory:                     Message Passing:
Process A --\                      Process A --> [Kernel] --> Process B
             \--> [Shared Memory Region] <--/     (data copied via kernel)
             /
Process B --/
(direct read/write, no kernel involvement per-access)
```

| | Shared Memory | Message Passing |
|---|---|---|
| Speed | **Faster** (no kernel involvement per access, once set up) | **Slower** (kernel mediates every message — copies data) |
| Synchronization | You must handle it yourself (mutex/semaphore — race condition risk!) | Built-in — kernel serializes message delivery |
| Setup complexity | More complex to set up correctly | Simpler to use, safer by default |
| Use case | Large/frequent data exchange (e.g., video frame buffers) | Simpler, infrequent, or smaller communication (e.g., control signals) |

**Real-life analogy:** **Shared memory** is like two roommates sharing one whiteboard on the wall — either can walk up and write/read anytime, fast and direct, but they need an agreed-upon system ("only write when the marker is on the left side" = synchronization) to avoid writing over each other mid-sentence. **Message passing** is like sending letters through a **shared mailbox with a mail carrier (kernel)** — slower (carrier has to physically deliver each letter), but inherently safer since only one letter is handled at a time, no risk of both roommates scribbling on the same spot simultaneously.

---

## 2. Pipes

### Anonymous Pipes
- A **unidirectional** communication channel between **related processes** (e.g., parent-child, created via `fork()`).
- Data written by one process is read by the other in **FIFO order** (first byte written = first byte read).
- Exists only as long as the processes using it are alive — has **no name** on the file system.
- **Classic shell example:** `ls | grep "txt"` — the pipe (`|`) connects `ls`'s standard output directly to `grep`'s standard input.

### Named Pipes (FIFOs)
- Like anonymous pipes, but have a **name** and exist as a special file in the file system (e.g., created via `mkfifo` on Linux).
- Can be used by **unrelated processes** (not just parent-child), since any process that knows the FIFO's path/name can open it.
- Still generally **unidirectional** per FIFO (though 2 FIFOs can be combined for bidirectional communication).

**Real-life analogy:** An **anonymous pipe** is like a private pneumatic tube directly connecting two adjacent rooms in the same house (only works because they're structurally connected — like parent-child processes). A **named pipe (FIFO)** is like a labeled drop-box in a public building — anyone who knows the box's name/location (unrelated processes) can use it to pass messages, as long as one puts things in and another takes them out in order.

---

## 3. Message Queues

- A **linked list of messages**, stored within the kernel, identified by a **message queue identifier**.
- Processes can **send** and **receive** messages via this queue.
- Unlike pipes, messages can have a **type/priority**, and a receiver can selectively read messages of a specific type rather than strictly FIFO order.
- Messages **persist** in the queue even if the sending process terminates, until a receiver reads them (unlike pipes, which are tied to the lifetime of the connected processes).

**Real-life analogy:** A message queue is like a **restaurant order ticket rail** in a kitchen — multiple waitstaff (senders) can post orders (messages) with different priority tags ("VIP table" vs "regular"), and cooks (receivers) can pick tickets by priority rather than strictly first-come-first-served, and even if a particular waiter goes on break (process terminates), their posted tickets remain on the rail until a cook handles them.

---

## 4. Shared Memory

- The OS maps a **region of physical memory** into the **address space of multiple processes**.
- Once set up, processes read/write to it **directly**, like regular memory — **no kernel/system call overhead per access**, making it the **fastest** IPC mechanism.
- **Requires explicit synchronization** (mutexes/semaphores — from File 6) between processes to avoid race conditions, since the OS does **not** automatically serialize access.

**Real-life analogy:** Exactly like the shared whiteboard analogy above — extremely fast because there's no "middleman" delivering messages, but responsibility for avoiding a mess (race conditions) is entirely on the processes themselves.

---

## 5. Sockets

- Used for communication **between processes on different machines** over a network (though also usable for **local** inter-process communication via **Unix domain sockets**).
- A **socket** is an endpoint for communication — identified by an **IP address + port number** (for network sockets).
- Supports both:
  - **TCP sockets** (connection-oriented, reliable, ordered delivery — like a phone call).
  - **UDP sockets** (connectionless, best-effort, no delivery guarantee — like sending postcards).

**Real-life analogy:** Sockets are like **phone lines between two houses in different cities** — TCP is a proper phone call (you dial, connect, confirm the other side picked up, and speak in order, with the option to ask "did you get that?"). UDP is like shouting a message out your window hoping the wind carries it to the right house — fast and simple, but no guarantee it arrives, arrives once, or arrives in order.

---

## 6. Signals

- A **software interrupt** delivered to a process to notify it of an **event** (e.g., `Ctrl+C` in the terminal sends `SIGINT`).
- Lightweight, **asynchronous** notification mechanism — doesn't carry much data, just "this event happened."
- **Common signals (Unix/Linux):**

| Signal | Meaning |
|---|---|
| `SIGINT` | Interrupt from keyboard (Ctrl+C) |
| `SIGKILL` | Force-terminate a process **immediately** (cannot be caught or ignored) |
| `SIGTERM` | Politely ask a process to terminate (**can** be caught/handled for graceful shutdown) |
| `SIGSEGV` | Segmentation fault (invalid memory access) |
| `SIGCHLD` | Sent to a parent when a child process terminates (used to trigger `wait()` and avoid zombies!) |

**Real-life analogy:** Signals are like a **fire alarm** in a building — a simple, universal, low-information notification ("something happened!") that everyone (the process) reacts to immediately, without needing detailed data about *why*. `SIGKILL` is like the building's emergency sprinkler system activating forcibly regardless of what you're doing (can't be ignored); `SIGTERM` is like a fire marshal calmly asking everyone to evacuate — you *can* finish sending an important email first (graceful shutdown handler) before leaving.

---

## 7. Summary Comparison Table

| Mechanism | Related Processes Only? | Speed | Data Size | Persistence | Typical Use Case |
|---|---|---|---|---|---|
| **Anonymous Pipe** | Yes | Fast | Small-medium, streaming | Tied to process lifetime | Shell command chaining (`\|`) |
| **Named Pipe (FIFO)** | No | Fast | Small-medium, streaming | Exists as a file until deleted | Unrelated local processes needing simple streaming comm |
| **Message Queue** | No | Medium | Small structured messages | Persists in kernel until read | Priority/typed message delivery |
| **Shared Memory** | No | **Fastest** | Large data | Persists until explicitly detached/removed | High-throughput data sharing (video buffers, databases) |
| **Sockets** | No (works across machines) | Slower (network) / Medium (local) | Any size | Connection-based | Network communication, microservices, local unrelated processes |
| **Signals** | No | Instant (async) | Essentially none (just an event flag) | N/A | Event notification (Ctrl+C, child termination, crashes) |

---

## Interview Q&A — Inter-Process Communication

**Q1: Why is shared memory the fastest IPC mechanism, but also the riskiest?**
> It's fastest because, once mapped, processes access the shared region like normal memory — **no system call / kernel mediation** is needed per read/write. It's riskiest because the OS provides **no built-in synchronization** — if two processes read/write the shared region concurrently without proper mutex/semaphore protection, you get classic **race conditions** (File 6), potentially corrupting data.

**Q2: What's the practical difference between a pipe and a message queue?**
> A pipe is a **raw, unstructured byte stream** in strict FIFO order — the receiver just gets a stream of bytes with no inherent message boundaries or types. A message queue delivers **discrete, typed/prioritized messages** — a receiver can selectively read messages of a certain type, and message boundaries are preserved (message N is read as exactly what was sent, not merged with message N+1).

**Q3: Why can SIGKILL not be caught or ignored by a process, unlike SIGTERM?**
> `SIGKILL` is designed as the OS's **guaranteed way to forcibly terminate** a process, even one that's buggy, hung, or intentionally trying to ignore termination signals (e.g., malware). If it could be caught/ignored, a misbehaving process could refuse to die, leaving admins with no reliable way to stop it. `SIGTERM`, by contrast, is meant as a **polite request**, allowing well-behaved applications to catch it and perform graceful cleanup (closing files, finishing transactions) before exiting.

**Q4: Real-world example: how does a web server (like Node.js) handle multiple client connections using sockets?**
> Each incoming client connection creates a new **socket** (an endpoint identified by the client's IP+port, on the server's listening port). For TCP-based HTTP, the server accepts the connection (three-way handshake), then reads the client's request and writes the response back over that same socket — analogous to a phone call being established, a conversation happening, then the call ending. Node's event loop handles many such sockets concurrently without blocking, using asynchronous I/O rather than one dedicated OS thread per connection.

**Q5: How would you decide between shared memory and message passing when designing a multi-process application?**
> If the data being exchanged is **large and frequent** (e.g., streaming video frames between a capture process and an encoder process), shared memory's speed advantage is worth the added complexity of manual synchronization. If the data is **small, infrequent, or needs guaranteed safe delivery without extra synchronization code** (e.g., sending occasional status updates or commands between microservices), message passing (message queues, sockets) is simpler and safer, trading some performance for correctness and ease of implementation.

**Q6: What signal does a parent process receive when its child terminates, and how does this relate to zombie processes (File 4)?**
> The parent receives `SIGCHLD`. A well-written parent process installs a `SIGCHLD` handler that calls `wait()`/`waitpid()` upon receiving it — immediately reaping the child's exit status and preventing it from lingering as a **zombie process**. This directly ties signals back to the zombie/orphan process concepts from File 4 — `SIGCHLD` is the *mechanism* that lets a parent know "hey, go clean up your child's entry now."

---

**Next up (File 16):** Linux/OS Practical Concepts for Interviews *(new topic)* — `top`/`ps` internals, load average, file descriptors, `strace`, container basics groundwork.
