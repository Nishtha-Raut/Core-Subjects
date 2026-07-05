# 12 — Virtual Memory, Demand Paging, Page Replacement & Thrashing

## 1. What is Virtual Memory?

> **Virtual memory** is a technique that allows the **execution of processes that are not completely in memory**. It provides the user an **illusion of having a very big main memory**, by treating part of secondary memory as if it were main memory (**swap space**).

### Advantages
- **Programs can be larger than physical memory.**
- Traditionally, instructions had to be in physical memory to execute — limiting program size to physical memory size. But in practice, the **entire program isn't needed at once**. Executing a program that's only *partially* in memory gives:
  1. A program is **no longer constrained** by the amount of physical memory available.
  2. Each user program takes **less physical memory** → **more programs run simultaneously** → **increased CPU utilization and throughput**.
  3. Running a partially-in-memory program benefits **both the system and the user**.
- Programmers are given a very **large virtual memory**, even when only a smaller physical memory is actually available.

**Real-life analogy:** Virtual memory is like a **university library with a "request from archive" system**. Students (the CPU) can "see" the entire massive catalog (virtual address space) as if all books were sitting on the shelves. In reality, only frequently-requested books sit on the visible shelves (RAM); rarely-used ones are stored in a huge off-site archive (disk/swap space) and fetched on-demand ("page fault") when specifically requested — giving the illusion of infinite shelf space.

---

## 2. Demand Paging

- **Demand Paging** is a popular method of **virtual memory management**.
- Pages of a process that are **least used** get stored in **secondary memory**.
- A page is copied to main memory **only when its demand is made**, or a **page fault** occurs.
- Various **page replacement algorithms** determine which pages get replaced when memory is full.

### Lazy Swapper
- Rather than swapping the **entire process** into memory, we use a **Lazy Swapper**.
- A lazy swapper **never** swaps a page into memory unless that page will actually be needed.
- Terminology note: since we view a process as a **sequence of pages** rather than one large contiguous block, the term "**Swapper**" is technically slightly incorrect for this per-page mechanism. A **swapper** manipulates **entire processes**, whereas a **Pager** is concerned with **individual pages** of a process.

### How Demand Paging Works
1. When a process is to be swapped-in, the **pager guesses** which pages will be used.
2. Instead of swapping in the whole process, the pager brings in **only those pages** — avoiding reading in pages that won't be used anyway.
3. This way, the **OS decreases swap time and the amount of physical memory needed**.

### Valid-Invalid Bit Scheme
- Each page table entry has a **valid-invalid bit** to distinguish pages **in memory** from pages **on disk**.

| Bit Value | Meaning |
|---|---|
| **1 (valid)** | The associated page is both **legal** and **currently in memory**. |
| **0 (invalid)** | The page is **either not valid** (not part of the process's logical address space) **or valid but currently on disk** (not yet swapped in). |

```
Logical Memory       Page Table (frame | valid-invalid bit)     Physical Memory       Swap Space (Disk)
0: A                     0 | 4 | 1                                4: A
1: B                     1 |   | 0                                6: C
2: C                     2 | 6 | 1                                9: F
3: D                     3 |   | 0                                (physical mem has A, C, F)
4: E                     4 |   | 0
5: F                     5 | 9 | 1
6: G                     6 |   | 0
7: H                     7 |   | 0                                            [swap: B, D, E, G, H]
```

- If a process **never attempts to access some invalid-bit page**, the process runs **successfully** without ever needing those pages present in swap space at all!

### Page Fault
- What happens if the process tries to access a page **not currently in memory**? Accessing a page marked **invalid** causes a **Page Fault**.
- The paging hardware, noticing the invalid bit for the demanded page, causes a **trap to the OS**.

### Procedure to Handle a Page Fault
1. Check an **internal table** (in the PCB of the process) to determine whether the reference was **valid** or an **invalid memory access**.
2. If the reference was **invalid** → the process **throws an exception** (e.g., segmentation fault).
   If the reference was **valid** → the **pager swaps-in** the page.
3. Find a **free frame** (from the free-frame list).
4. Schedule a **disk operation** to read the desired page into the newly allocated frame.
5. When the disk read is complete, **modify the page table** to reflect that the page is now in memory (update valid bit to 1, update frame number).
6. **Restart the instruction** that was interrupted by the trap — the process can now access the page as though it had always been in memory.

```
              Steps in Handling a Page Fault
                                                          page is on backing store
     reference (1)                    OS <-------(3)------------------
       |                               ^
       v                              trap (2)
  [Page Table]                         |
     | 0 |  <-- (invalid)  -----------+
       |
     restart                    (5) reset page table
   instruction (6)                    |
       |                              v
       +------<------ [free frame] <--(4) bring in missing page
                            |
                     Physical Memory                    Swap Space
```

### Pure Demand Paging
- In the **extreme case**, we can start executing a process with **NO pages in memory at all**.
- When the OS sets the instruction pointer to the process's first instruction (which isn't in memory), the process **immediately faults** for that page, and the page gets brought into memory.
- **Rule: Never bring a page into memory until it is explicitly required.**
- We rely on **Locality of Reference** to make demand paging performant in practice (programs tend to access a relatively small, clustered set of pages within any short time window — see below).

### Advantages of Virtual Memory
1. The **degree of multi-programming** increases (more processes fit, since only needed pages are resident).
2. Users can **run large apps with less real physical memory**.

### Disadvantages of Virtual Memory
1. The system can become **slower** since swapping takes time.
2. **Thrashing** may occur (see Section 4 below).

---

## 3. Page Replacement Algorithms

- Whenever a **Page Fault** occurs (process accesses a page not currently in a frame), the OS must bring that page from swap space into a frame.
- If all frames are busy (**high memory utilization**), the OS must **replace** one of the currently-allocated pages with the new page.
- The **page replacement algorithm** decides **which** memory page gets replaced.
- **AIM: minimize the number of page faults.**

### 3.1 FIFO (First-In-First-Out)
- Allocate a frame to a page as it comes into memory, replacing the **oldest** page.
- **Easy to implement.**
- **Performance is not always good:**
  - The replaced page might be an initialization module used long ago (**good** replacement candidate) — but,
  - The page might contain a **heavily used variable** initialized early and still in constant use (replacing it will **immediately cause another page fault**).
- **Belady's Anomaly:**
  - For LRU and Optimal page replacement, increasing the number of frames generally **decreases** page faults, as expected.
  - However, Bélády found that with **FIFO**, the number of page faults can sometimes **INCREASE** even as the number of frames increases — a strange, counter-intuitive behavior shown by FIFO in certain reference-string cases.

**Real-life analogy for FIFO's weakness:** Imagine a small fridge (limited frames) where you throw out whatever's been in there **longest**, regardless of whether you still use it. You might throw out a jar of your favorite, frequently-used spice just because it was bought first, while a rarely-used condiment bought yesterday stays — leading to you constantly re-buying (re-faulting) that spice.

### 3.2 Optimal Page Replacement
- Find a page that is **never referenced again in the future**. If such a page exists, replace it.
- If no such page exists, find the page **referenced farthest in the future**, and replace that one.
- **Lowest page fault rate** among ANY algorithm — theoretically optimal.
- **Difficult to implement** in practice: the OS would need **future knowledge** of the reference string, which is generally **impossible** to know ahead of time. (Similar in spirit to SJF scheduling needing to know future burst times.)
- **Used mainly as a benchmark/theoretical baseline** to compare other algorithms against.

### 3.3 Least Recently Used (LRU)
- Uses the **recent past as an approximation of the near future** — replace the page that **hasn't been used for the longest period**.
- Can be implemented in 2 ways:

**1. Counters:**
- Associate a **time field** with each page table entry (timestamp of last access).
- Replace the page with the **smallest** time value.

**2. Stack:**
- Keep a **stack of page numbers**.
- Whenever a page is referenced, it's **removed from the stack and put on top**.
- Most recently used = always on top; least recently used = always on the bottom.
- Since entries might be removed from the **middle** of the stack, a **Doubly Linked List** is typically used for efficient removal/insertion.

**Real-life analogy for LRU:** Think of your **browser tabs** — if you have too many open and your browser needs to free memory, it (or your habits) would sensibly close the tab you haven't clicked on in the longest time, keeping the tabs you've actively used recently, on the reasonable bet that "recently used" strongly predicts "will be used again soon."

### 3.4 Counting-Based Page Replacement
- Keep a **counter** of the number of references made to **each** page (**reference counting**).

**1. Least Frequently Used (LFU):**
- Actively used pages should have a **large reference count**.
- Replace the page with the **smallest count**.

**2. Most Frequently Used (MFU):**
- Based on the argument that the page with the **smallest count was probably just brought in** and hasn't been used much yet (so, counter-intuitively, replace the **most**-frequently-used page, betting it's already "served its purpose").
- **Neither MFU nor LFU replacement is common** in practice — both have significant real-world drawbacks (LFU can get "stuck" keeping old pages with high historical counts that are no longer relevant; MFU's logic is often simply wrong in practice).

### Summary Comparison

| Algorithm | Requires Future Knowledge? | Implementation Complexity | Practical Use |
|---|---|---|---|
| FIFO | No | Simple | Common but suboptimal (Belady's Anomaly risk) |
| Optimal | Yes (impossible in practice) | N/A | Theoretical benchmark only |
| LRU | No (uses recent past) | Moderate-High (needs counters/stack) | Widely used, close to Optimal in practice |
| LFU / MFU | No | Moderate | Rarely used |

---

## 4. Thrashing

> If a process doesn't have the number of frames it needs to support its pages in active use, it will **quickly page-fault**. At this point, it must replace some page — but since **all** its pages are in active use, it must replace a page that will be needed again **right away**. Consequently, it **quickly faults again, and again, and again**, replacing pages it must immediately bring back in.

- This **high paging activity is called Thrashing**.
- A system is **Thrashing** when it **spends more time servicing page faults than actually executing processes**.

```
CPU Utilization
     ^
     |            ___
     |         __/    \
     |       _/         \_______  <-- thrashing region
     |      /                    \___
     |    _/
     |  _/
     |_/
     +------------------------------------> Degree of Multiprogramming
```
*(Adding more processes initially increases CPU utilization, but past a certain point, it collapses due to thrashing.)*

**Real-life analogy:** Imagine a chef with a **tiny countertop** (limited frames) trying to prepare 10 dishes simultaneously. Every time they reach for an ingredient not currently on the countertop, they have to **put something else away** to make room, walk to the pantry (disk), retrieve the new ingredient, and come back — but as soon as they need the *previous* ingredient again a moment later, it's already been put away too! The chef spends almost all their time walking back and forth to the pantry (servicing page faults) and almost no time actually cooking (executing) — this is thrashing.

### Techniques to Handle Thrashing

**1. Working Set Model**
- Based on the concept of the **Locality Model**.
- **Basic principle:** if we allocate enough frames to a process to accommodate its **current locality** (the set of pages it's actively using right now), it will only fault when it **moves to a new locality**.
- But if allocated frames are **fewer** than the size of the current locality, the process is **bound to thrash**.

**2. Page Fault Frequency**
- Thrashing has a **high page-fault rate**. We want to **control** the page-fault rate directly.
- If page-fault rate is **too high** → the process needs **more frames**.
- If page-fault rate is **too low** → the process may have **too many** frames (could give some to another process).
- Establish **upper and lower bounds** on the desired page-fault rate.
  - If pf-rate **exceeds the upper limit** → allocate the process **another frame**.
  - If pf-rate **falls below the lower limit** → **remove a frame** from the process.
- By controlling pf-rate directly, thrashing can be **prevented**.

**Real-life analogy for Working Set Model:** If our chef's "working set" for a specific dish is 5 ingredients (their current locality), giving them countertop space for only 5 ingredients means they'll only need to visit the pantry when they **switch to cooking an entirely different dish** (new locality) — smooth, efficient cooking. But squeeze them down to counter space for just 2 ingredients while trying to actively use 5, and they'll be shuffling to the pantry constantly — thrashing.

---

## Interview Q&A — Virtual Memory, Page Replacement & Thrashing

**Q1: What is a page fault, and is it always a bad/error thing?**
> A page fault is a trap raised when a process references a page not currently in physical memory (marked invalid). It's **not necessarily an error** — in demand paging, page faults are a **normal, expected mechanism** by which pages get loaded on-demand. It only becomes a *performance problem* when page faults occur excessively (thrashing).

**Q2: Why is LRU generally preferred over FIFO in practice?**
> LRU uses actual recent access history as a heuristic for future behavior (grounded in locality of reference), while FIFO only tracks *arrival order*, completely ignoring how often/recently a page is actually used. This means FIFO can evict a heavily-used page just because it happened to load first, while LRU specifically avoids evicting actively-used pages. LRU is also immune to Belady's Anomaly, unlike FIFO.

**Q3: Why can't we implement the truly Optimal page replacement algorithm in a real OS?**
> Because it requires knowing the **future** reference string — which page will be accessed farthest in the future — and no OS can predict future memory accesses with certainty ahead of time. It's used purely as a **theoretical upper bound / benchmark** to measure how close practical algorithms (like LRU) come to ideal performance.

**Q4: What causes thrashing, and what's the difference between the Working Set Model and Page Fault Frequency approaches to fixing it?**
> Thrashing is caused by processes not having enough frames to hold their "actively used" pages, causing a vicious cycle of constant faulting. **Working Set Model** proactively estimates each process's current locality size and ensures it gets *at least* that many frames. **Page Fault Frequency** is more reactive — it directly monitors the fault *rate* and dynamically adds/removes frames based on whether that rate crosses defined thresholds. Working Set is more predictive; PFF is more feedback-driven.

**Q5: Real-world example: you notice your Linux server is swapping heavily and becoming unresponsive — how do these concepts apply?**
> This is a live example of **thrashing** — the system likely has too many processes / too much memory demand for the available RAM, so the OS is constantly swapping pages in and out of the disk-based swap space, spending more time on I/O (page faults) than actual computation. The fix mirrors OS theory: either **reduce the degree of multiprogramming** (kill/limit processes, e.g., via `ulimit` or cgroups memory limits) or **add more physical RAM** so each process's working set can actually fit in memory. Tools like `vmstat` (checking the `si`/`so` swap-in/swap-out columns) are the practical equivalent of monitoring page-fault frequency.

**Q6: Why does virtual memory let you "run a 16GB game on an 8GB RAM laptop" (with a performance hit)?**
> Because demand paging only loads the pages actually being *used right now* (e.g., the current game level's assets) into RAM, while inactive pages (menus, other levels, unused assets) stay on disk (swap space) until needed. This works fine as long as the actively-used "locality" fits reasonably within available RAM — but if the working set is too large relative to RAM, you get exactly the thrashing scenario described above, which is why heavy swapping causes noticeable game stutter/lag.

---

**Next up (File 13):** File Systems — a topic not covered in your original notes but essential for OS interviews (inodes, file allocation methods, directory structures, journaling).
