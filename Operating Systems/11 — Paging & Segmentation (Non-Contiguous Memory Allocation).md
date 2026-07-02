# 11 — Paging & Segmentation (Non-Contiguous Memory Allocation)

## 1. Motivation: Why Non-Contiguous Allocation?

- The main disadvantage of Dynamic Partitioning is **External Fragmentation**.
  - Can be removed by **Compaction**, but that comes with overhead.
- We need a more **dynamic/flexible/optimal** mechanism to load processes into memory.

### Idea behind Paging
- Suppose we have only **two small non-contiguous free holes**, say 1KB each.
- If the OS wants to allocate RAM to a process needing 2KB, in **contiguous** allocation this is **not possible** — we'd need one single contiguous 2KB block (external fragmentation blocks us).
- **What if we divide the process into 1KB-1KB blocks instead?** → This is the core idea of **Paging**.

---

## 2. Paging

> **Paging** is a memory-management scheme that permits the **physical address space of a process to be non-contiguous.**

- Avoids external fragmentation and the need for compaction.
- Idea: divide **physical memory** into fixed-sized blocks called **Frames**, and divide **logical memory** into blocks of the **same size** called **Pages**.
  - `Page size = Frame size`
- **Page size** is usually determined by the processor architecture. Traditionally uniform (e.g., 4,096 bytes / 4KB), though modern processors often support multiple/simultaneous page sizes.

### Page Table
- A **data structure** that stores which page is mapped to which frame.
- **Contains the base address of each page** in physical memory.

### Address Translation via Paging
- Every CPU-generated **logical address** is divided into **2 parts**:
  - A **page number (p)** — used as an **index** into the page table to get the base address of the corresponding frame.
  - A **page offset (d)** — the specific byte within that frame.

```
Logical Memory (Process P1)      Page Table         Physical Memory
+------------+                  +---+---+           0: [        ]
|   Page 0   |                  | 0 | 1 |           1: [ Page 0 ]
+------------+                  +---+---+           2: [        ]
|   Page 1   |                  | 1 | 4 |           3: [ Page 2 ]
+------------+                  +---+---+           4: [ Page 1 ]
|   Page 2   |                  | 2 | 3 |           5: [        ]
+------------+                  +---+---+           6: [        ]
|   Page 3   |                  | 3 | 7 |           7: [ Page 3 ]
+------------+                  +---+---+
```
*(Page 0 maps to Frame 1, Page 1 maps to Frame 4, Page 2 maps to Frame 3, Page 3 maps to Frame 7 — notice frames are scattered, non-contiguous, yet the process runs correctly because the page table tracks the mapping.)*

### Where is the Page Table stored?
- Stored in **main memory** at process creation time; its **base address is stored in the PCB**.
- A **Page Table Base Register (PTBR)** points to the current page table.
- Changing page tables (e.g., during a **context switch**) requires updating only **this one register**.

### How does Paging avoid External Fragmentation?
- **Non-contiguous allocation** of a process's pages is allowed in random **free frames** anywhere in physical memory — no need to find one big contiguous block.

**Real-life analogy:** Think of Paging like moving apartments using a **storage-unit facility with many small identical lockers scattered across the building**. Instead of needing one giant contiguous storage room (contiguous allocation — often impossible to find), you split your belongings into equal-sized boxes (pages) and store them in **whichever lockers (frames) happen to be free**, even if they're on totally different floors. A master inventory list (page table) tells you exactly which box is in which locker, so you can always retrieve everything correctly despite the scattering.

---

## 3. Why is Paging Slow, and How Do We Speed It Up?

- **Problem:** There are **too many memory references** to access the desired location in physical memory — every memory access requires first reading the page table (itself in memory!) to translate the address, **then** accessing the actual data. That's effectively **2 memory accesses per 1 logical access**.

### Translation Look-aside Buffer (TLB)
- A **hardware support** mechanism to **speed up** the paging process.
- It's a **hardware cache** — high-speed memory.
- TLB stores entries as **key-value pairs**: `page number -> frame number`.

### How TLB Works
- Page table is stored in main memory — because of this, translation via the page table alone is **slow**.
- When retrieving a physical address using the page table, **after** getting the frame number corresponding to a page number, we **cache that entry into the TLB**.
- Next time the same page is referenced, we get the frame number **directly from the TLB** — without touching the actual page table in main memory — making paging **much faster**.

```
CPU -- logical address (p | d) --> [page number p] --> Search TLB
                                                             |
                                        TLB HIT ------+------+------ TLB MISS
                                            |                          |
                                  get frame # directly        look up full page table
                                            |                  get frame #, then
                                            |                  cache it into TLB
                                            v                          |
                                     [frame # f | offset d] <----------+
                                            |
                                            v
                                     Physical Memory
```

- **TLB hit**: TLB contains the mapping for the requested logical address → fast path.
- **TLB miss**: Must consult the full page table in memory → slow path, but result gets cached for next time.

### Address Space Identifier (ASID)
- Stored in **each TLB entry**.
- **Uniquely identifies each process**, used to provide **address space protection** and allow the TLB to hold entries for **multiple different processes simultaneously**.
- When the TLB tries to resolve a virtual page number, it ensures the **ASID of the currently executing process matches** the ASID associated with that TLB entry.
- If it **doesn't match** → treated as a **TLB miss** (even if the page number technically matches — this prevents Process A from accidentally using Process B's cached translation).

**Real-life analogy:** The TLB is like keeping a **small sticky-note cheat sheet** of "frequently dialed phone numbers → contact names" right on your desk, instead of flipping through your entire phonebook (page table) every single time you want to call someone. If the number's not on your cheat sheet (TLB miss), you look it up in the full phonebook once, then **add it to your cheat sheet** for next time. The **ASID** is like making sure you don't accidentally use *your roommate's* cheat sheet notes when dialing — everyone's cheat sheet entries are tagged with whose list they belong to.

---

## 4. Segmentation

- An important aspect of memory management that becomes unavoidable with paging is the **separation of the user's view of memory from the actual physical memory**.
- **Segmentation** is a memory management technique that supports the **user's view of memory**.

### Core Idea
- A **logical address space** is a collection of **segments** — based on the **user's view** of logical memory.
- Each segment has a **segment number and offset**, defining a segment: `<segment-number, offset> = {s, d}`.
- A process is divided into **variable-sized segments**, based on the user's view (e.g., "main function", "library functions", "stack", "symbol table" — each as its own segment).

### Paging vs Segmentation — Why Segmentation?
- **Paging** is closer to the **Operating System's** view rather than the **User's** view. It divides all processes into uniform pages, even if related parts of a function get split across page boundaries.
- The OS doesn't care about the user's logical view of the process — it may **divide the same function into different pages**, and those pages **may or may not be loaded simultaneously** into memory. This can **decrease efficiency**.
- **Segmentation** is better because it divides a process along **logical, meaningful boundaries** — e.g., the main function in one segment, library functions in another segment — matching how a compiler/programmer naturally thinks about the program.

### Segmentation Hardware
```
CPU --logical address (s | d)--> [Segment Table lookup: limit, base]
                                            |
                                      < Yes -----> (+) --> Physical Memory
                                        |
                                        No
                                        |
                                        v
                              Trap: addressing error
```
- The segment table stores a **limit** and **base** for each segment. The offset `d` is checked against the segment's `limit`; if valid, it's added to the `base` to get the physical address. If invalid → trap (addressing error) — same protective philosophy as the relocation/limit register scheme from File 10.

### Advantages of Segmentation
1. **No internal fragmentation** (segments are exactly sized to their logical content).
2. One segment has **contiguous allocation** → efficient working *within* that segment.
3. Segment table is generally **smaller** than a page table.
4. More efficient system, since the compiler naturally keeps the **same type of functions** together in one segment.

### Disadvantages of Segmentation
1. **External fragmentation** (since segments are variable-sized, just like dynamic partitioning).
2. Variable segment sizes aren't great for the **time complexity of swapping**.

### Modern Systems: Hybrid Approach
- Modern system architectures often implement **both segmentation and paging together** in some hybrid approach (e.g., x86's segmented-paging model) — combining the logical clarity of segmentation with the fragmentation-avoidance of paging.

**Real-life analogy:** Segmentation is like organizing a **filing cabinet by meaningful category** — "Tax Documents," "Medical Records," "Work Contracts" — each folder (segment) can be a different size based on how much content it naturally has, and everything within one folder stays together contiguously. Paging, by contrast, is like a librarian who insists on splitting *everything* into identical-sized boxes regardless of logical content, purely for storage efficiency — a single folder's contents might get split across several same-sized boxes stored in different shelf locations. Segmentation gives *meaning*; Paging gives *efficiency*. Real systems often want both.

---

## Interview Q&A — Paging & Segmentation

**Q1: What problem does the TLB solve, and what happens on a TLB miss?**
> The TLB solves the "double memory access" problem inherent to paging — without it, every logical memory access requires first reading the page table (in RAM) to translate the address, then accessing the actual data (another RAM access). On a TLB **hit**, the frame number is fetched instantly from the fast on-chip cache. On a TLB **miss**, the full page table lookup happens in RAM, the translation is cached into the TLB for future use, then the actual memory access proceeds — slower this one time, but faster for subsequent accesses to the same page.

**Q2: Why do TLB entries need an ASID?**
> Without an ASID, if the TLB simply cached "page number → frame number," and a context switch occurred to a different process, that process's identical-looking page numbers could **incorrectly reuse another process's cached translations** — a serious security/correctness bug. The ASID tags each TLB entry to a specific process, so the hardware only uses an entry if the ASID matches the currently running process (otherwise, treated as a miss, forcing a fresh lookup).

**Q3: What's the fundamental difference between paging and segmentation, in terms of size?**
> Pages are always **fixed-size** (uniform, e.g., 4KB), determined by hardware/OS design, and are unaware of the logical structure of the program. Segments are **variable-sized**, and their boundaries are meaningful to the **program's logical structure** (e.g., one segment per function or per data structure) — decided by the compiler/programmer's view of the code.

**Q4: Does segmentation suffer from external fragmentation like dynamic partitioning? Why?**
> Yes — because segments are variable-sized (just like dynamically-sized partitions), as segments are allocated and freed over time, memory gets fragmented into scattered, differently-sized free chunks that may not be reusable for new segment requests, exactly like the external fragmentation problem in dynamic contiguous allocation.

**Q5: How does this map to real x86/Linux systems today?**
> Modern x86-64 Linux systems primarily rely on **paging** (with multi-level page tables and huge pages for efficiency) for virtual memory. Pure segmentation as a general memory-management scheme has mostly fallen out of favor because paging's fixed-size structure is simpler and more predictable to manage in hardware — though vestiges of segmentation still exist in x86 architecture (e.g., segment registers CS/DS/SS used mainly for protection rings and legacy compatibility, not primary memory management).

**Q6: Practical example: why does increasing page size sometimes help performance, and what's the trade-off?**
> Larger pages ("huge pages," e.g., 2MB instead of 4KB) mean **fewer page table entries** are needed to cover the same amount of memory, which reduces TLB misses (since each TLB entry now covers more memory) — great for large, memory-intensive applications like databases. The trade-off is **increased internal fragmentation** — a process needing just slightly more than one page now wastes a much larger chunk of unused space within that oversized page.

---

**Next up (File 12):** Virtual Memory — Demand Paging, Page Faults, Page Replacement Algorithms (FIFO, Optimal, LRU, LFU/MFU), and Thrashing.
