# 05 — CPU Scheduling (FCFS, SJF, Priority, Round Robin, MLQ, MLFQ)

## 1. Introduction to Process Scheduling

- **Basis of multi-programming OS.**
- By switching the CPU among processes, the OS makes the computer more productive.
- Many processes are kept in memory at a time. When a process must wait or its time quantum expires, the OS takes the CPU away from it and gives it to another process — this pattern continues.

### CPU Scheduler
- Whenever the CPU becomes idle, the OS must select one process from the ready queue to execute.
- Done by the **STS (Short-Term Scheduler)**.

### Non-Preemptive vs Preemptive Scheduling

| Non-Preemptive | Preemptive |
|---|---|
| Once CPU is allocated to a process, it **keeps the CPU** until it releases it — either by terminating or switching to wait-state. | CPU is **taken away** from a process after its time quantum expires, or it terminates/switches to wait-state. |
| Can cause **starvation** — a process with long burst time may starve shorter processes waiting behind it. | **Less starvation**. |
| **Low** CPU utilization (can idle while waiting). | **High** CPU utilization. |

### Goals of CPU Scheduling
- Maximum CPU utilization
- Minimum Turnaround Time (TAT)
- Minimum Wait Time
- Minimum Response Time
- Maximum Throughput

### Key Terminology

| Term | Definition | Formula |
|---|---|---|
| **Throughput** | Number of processes completed per unit time | — |
| **Arrival Time (AT)** | Time when a process arrives in the ready queue | — |
| **Burst Time (BT)** | Time required by the process for its execution | — |
| **Completion Time (CT)** | Time at which the process finishes/terminates | — |
| **Turnaround Time (TAT)** | Time from a process's first entry into ready state until it terminates | `TAT = CT - AT` |
| **Wait Time (WT)** | Time the process spends waiting for the CPU | `WT = TAT - BT` |
| **Response Time** | Time between the process entering the ready queue and getting the CPU **for the first time** | — |

**Real-life analogy for these metrics:** Think of a queue at a bank:
- **Arrival Time** = when you enter the queue.
- **Burst Time** = how long the teller actually needs to serve you.
- **Completion Time** = when you walk out, fully served.
- **Turnaround Time** = total time from entering the bank to leaving.
- **Wait Time** = time spent just standing in line before being served.
- **Response Time** = time until the teller *first* calls your number (even if they then pause to handle a side task before finishing you).

---

## 2. FCFS (First Come, First Serve)

- Whichever process arrives first in the ready queue gets the CPU first.
- **Non-preemptive**.
- If one process has a very long burst time, it majorly affects the **average wait time** of other processes — this is called the **Convoy Effect**.

> **Convoy Effect**: A situation where many processes needing a resource for a short time are blocked by one process holding that resource for a long time — causing poor resource management.

**Real-life analogy:** You're at a grocery store checkout (single line, FCFS). The person ahead of you has a cart full of 200 items while you just have milk. Even though you'd be done in 10 seconds, you're stuck waiting behind their 15-minute checkout — classic convoy effect.

---

## 3. Shortest Job First (SJF)

### 3.1 SJF — Non-preemptive
- Process with the **least burst time** is dispatched to the CPU first.
- Must **estimate** BT for each process in the ready queue beforehand — correct estimation is *ideally* impossible.
- Run the lowest-time process fully, then pick the next-lowest at that instance.
- **Suffers from convoy effect** if the very first process to arrive has a large BT.
- **Process starvation** can happen (a process with a large BT might be pushed back indefinitely by a stream of shorter jobs).
- Scheduling criteria: `AT + BT`.

### 3.2 SJF — Preemptive (aka Shortest Remaining Time First - SRTF)
- **Less starvation** than non-preemptive SJF.
- **No convoy effect.**
- Gives a **lower average wait time** — scheduling a short job before a long one decreases the WT of the short job *more* than it increases the WT of the long process.

**Real-life analogy:** Imagine a barista serving orders by "fastest to make first" — a black coffee (30 sec) gets served before a complex 5-shot caramel macchiato (5 min), even if the macchiato order came in first. It minimizes the *average* wait across all customers, though the one customer with the complex order might wait a long time (potential starvation).

---

## 4. Priority Scheduling

### 4.1 Non-preemptive
- Priority is assigned to a process **when it's created**.
- **SJF is a special case** of general priority scheduling, where priority is inversely proportional to burst time (shorter job = higher priority).

### 4.2 Preemptive
- The current **RUN**-state job is **preempted** if a newly-arrived job has **higher priority**.
- Can cause **indefinite waiting (starvation)** for lower-priority jobs — they might never get to execute. (True for both preemptive and non-preemptive versions.)
- **Solution: Aging** — gradually increase the priority of processes that have waited a long time. E.g., increase priority by 1 every 15 minutes.

**Real-life analogy:** In a hospital ER, patients are treated by priority (severity), not arrival order — a heart attack patient (high priority) jumps the queue ahead of someone with a sprained ankle (low priority) who arrived earlier. Without "aging," the sprained-ankle patient could theoretically wait forever if severe cases keep arriving — so hospitals also bump up priority the longer someone waits.

---

## 5. Round Robin (RR) Scheduling

- **Most popular** scheduling algorithm.
- Like FCFS but **preemptive**.
- Designed specifically for **time-sharing systems**.
- Scheduling criteria: `AT + Time Quantum (TQ)` — **doesn't depend on burst time**.
- **No process waits forever** → very low starvation. **No convoy effect.**
- **Easy to implement.**
- If **TQ is too small** → more context switches → **more overhead**.
- If **TQ is too large** → behaves like FCFS.

### Round Robin Flow
```
        [Ready Queue]
              |
              v
     Pick a process (FCFS order)
              |
         BT < TQ? ----Yes----> Execute till termination --> Terminate state
              |
              No
              |
              v
       Execute for TQ
              |
      TQ expires
              |
   Process execution done? --Yes--> Terminate state
              |
              No
              |
   Back to [Ready Queue]
```

**Real-life analogy:** Round Robin is like a study group where everyone gets exactly 5 minutes to speak, in turn, regardless of whether they're done or not. If someone isn't finished in 5 minutes, they get cut off and go to the back of the line to wait for their next turn. Fair, but if the time slice is too short (e.g., 10 seconds), you spend more time on "next person, next person" transitions than actual discussion (overhead).

---

## 6. Comparison Table

| | FCFS | SJF | PSJF (Preemptive SJF) | Priority | P-Priority | RR | MLQ | MLFQ |
|---|---|---|---|---|---|---|---|---|
| **Design** | Simple | Complex | Complex | Complex | Complex | Simple | Complex | Complex |
| **Preemption** | No | No | Yes | No | Yes | Yes | Yes | Yes |
| **Convoy Effect** | Yes | Yes | No | Yes | Yes | No | Yes | Yes |
| **Overhead** | No | No | Yes | No | Yes | Yes | Yes | Yes |

---

## 7. Multi-Level Queue Scheduling (MLQ)

- The **ready queue** is divided into **multiple sub-queues** based on priority.
- A process is **permanently assigned** to one queue (inflexible) — based on process property (memory size, priority, or type).
- **Each queue has its own scheduling algorithm.** E.g.:
  - System Process (SP) queue → RR
  - Interactive Process (IP) queue → RR
  - Batch Process (BP) queue → FCFS

```
Highest priority
   System Process (SP) queue      --->
   Interactive Process (IP) queue --->
   Batch Process (BP) queue       --->
Lowest priority
```

### Process Types
- **System process**: created by the OS (highest priority).
- **Interactive process (Foreground)**: needs user input (I/O).
- **Batch process (Background)**: runs silently, no user input needed.

### Behavior
- Scheduling between sub-queues is **fixed priority preemptive** — e.g., the foreground queue has absolute priority over the background queue.
- If an interactive process arrives while a batch process is executing, the **batch process gets preempted**.

### Problem
- Only after **all** processes from the top-level queue finish, lower-level queues get scheduled — causing **starvation for lower priority processes**.
- **Convoy effect is present.**

**Real-life analogy:** MLQ is like an airport security line with separate lanes: "Priority/First Class" lane, "Frequent Flyer" lane, and "Economy" lane — where Economy passengers are only let through once **every single** Priority and Frequent Flyer passenger has gone through, even if a huge batch of Priority flyers keeps arriving. Economy travelers could theoretically wait forever.

---

## 8. Multi-Level Feedback Queue Scheduling (MLFQ)

- **Multiple sub-queues**, but unlike MLQ, processes are **allowed to move between queues**.
- Idea: separate processes by the characteristics of their burst time.
  - If a process uses **too much CPU time**, it's moved to a **lower priority queue**.
  - This keeps **I/O-bound and interactive** processes in the **higher-priority** queue.
- A process that **waits too long** in a lower-priority queue may be **moved up** to a higher-priority queue — this form of **aging prevents starvation**.
- **Less starvation than MLQ.**
- **Flexible** — can be configured to match specific system design requirements.

### Sample MLFQ Design
```
--> [ TQ = 2 ] -->
--> [ TQ = 4 ] -->
--> [ TQ = 8 ] -->
--> [   FCFS  ] -->
```
*(A process that doesn't finish within its time quantum at one level drops down to the next level, which has a longer time quantum but lower priority.)*

**Real-life analogy:** MLFQ is like a customer support system: New tickets first go into a "fast lane" (short TQ) — if resolved quickly, great. But if a ticket keeps needing more time (CPU-bound / complex), it gets bumped down to a "longer handling time" queue with lower priority, so it doesn't block quick, simple tickets. But if a ticket has been sitting unresolved for too long in the low-priority queue, it gets escalated back up (aging) so it doesn't get forgotten forever.

---

## Interview Q&A — CPU Scheduling

**Q1: Why is Round Robin considered "fair" and why is it the default for time-sharing OSes?**
> Because every process gets a bounded, equal share of CPU time regardless of arrival order or burst time — no process can be indefinitely starved, and it's simple to implement. This predictability and fairness is exactly what interactive/time-sharing systems (like desktop OSes) need, where responsiveness to many processes matters more than strictly minimizing average wait time.

**Q2: What's the trade-off in choosing the time quantum for Round Robin?**
> Too small → excessive context-switch overhead (wasting CPU cycles on bookkeeping instead of actual work). Too large → RR starts behaving like FCFS, losing its responsiveness benefits and risking convoy effect for interactive processes. A good TQ is typically chosen so that most processes finish (or make good progress) within one or two quantums.

**Q3: Why does Preemptive SJF give a lower average wait time than FCFS?**
> Because scheduling a short job before a long one reduces the wait time of the short job by more than it increases the wait time of the long job — mathematically, this reordering effect minimizes the total/average wait time across all processes. (This is provably optimal for average wait time, assuming perfect knowledge of burst times.)

**Q4: Why is SJF hard to actually implement in a real OS?**
> Because you'd need to know the burst time of a process *before* running it — which is generally impossible to predict perfectly (it depends on runtime behavior, I/O waits, user input, etc.). Real systems approximate it using historical averages (e.g., exponential averaging of past CPU bursts) rather than true SJF.

**Q5: How does MLFQ solve the starvation problem seen in MLQ?**
> MLQ permanently locks a process into one priority queue — if higher-priority queues stay busy, lower-priority processes may never run. MLFQ instead allows a process to move *between* queues — a process that's waited too long gets **aged up** to a higher-priority queue, guaranteeing it eventually gets CPU time.

**Q6: Real-world example — how does this relate to Linux's actual scheduler (CFS)?**
> Modern Linux doesn't use plain Priority/RR/MLFQ as-is — it uses the **Completely Fair Scheduler (CFS)**, which is conceptually related to weighted fair queuing (similar spirit to RR + priority, using a red-black tree keyed by "virtual runtime" to always pick the most CPU-starved process next). Knowing the classic algorithms (RR, MLFQ, priority + aging) gives you the conceptual foundation to explain why CFS exists and what problems it solves (fairness + avoiding starvation, similar to aging).

---

**Next up (File 6):** Concurrency Basics — Race Conditions, Critical Section, Mutex/Locks, Semaphores, Condition Variables.
