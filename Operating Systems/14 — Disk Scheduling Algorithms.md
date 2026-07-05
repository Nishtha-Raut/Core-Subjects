# 14 — Disk Scheduling Algorithms *(New Topic — Not in Original Notes)*

> Not covered in your original notes, but a **very common interview topic**, especially for systems/infrastructure roles — "how does the OS decide the order to service disk I/O requests?"

## 1. Why Disk Scheduling Matters

- A **Hard Disk Drive (HDD)** has a **physical read/write head** that must move across spinning platters (tracks) to read/write data.
- Moving this head (**seek time**) is by far the **slowest** part of a disk operation — much slower than actually reading/writing once positioned.
- If multiple processes request disk I/O at different track locations, the OS must decide **the order to service these requests** to minimize total head movement (and thus total seek time).

**Real-life analogy:** Imagine an elevator in a 20-floor building receiving requests from floors 2, 15, 4, 18, and 7, all roughly at once. If the elevator just serviced requests in the **order received** (2 → 15 → 4 → 18 → 7), it would zigzag wildly up and down the building, wasting enormous time. A smarter algorithm would service nearby floors together as the elevator sweeps in one direction, minimizing total travel. Disk scheduling faces the exact same problem — the "floors" are disk tracks, and the "elevator" is the read/write head.

---

## 2. Disk Scheduling Algorithms

Assume requests come in for tracks: `98, 183, 37, 122, 14, 124, 65, 67` and the head is currently at track `53`.

### 2.1 FCFS (First Come, First Serve)
- Service requests **strictly in the order they arrive**, regardless of track position.
- ✅ Simple, fair (no starvation).
- ❌ Can cause **huge, inefficient head movement** (zigzagging), as in the elevator example above.

### 2.2 SSTF (Shortest Seek Time First)
- Service whichever pending request is **closest to the current head position** next.
- ✅ Generally gives a much better average response time than FCFS.
- ❌ Can cause **starvation** — a request far from the current cluster of activity might keep getting "skipped" if closer requests keep arriving (analogous to SJF's starvation problem in CPU scheduling — same root cause pattern!).

### 2.3 SCAN ("Elevator" Algorithm)
- The head moves in **one direction**, servicing all requests along the way, until it reaches the **end of the disk**, then **reverses direction** and services requests on the way back.
- ✅ No starvation (everyone eventually gets serviced within one or two "sweeps").
- ✅ More predictable/uniform wait times than SSTF.
- ❌ Requests that just missed the head's pass have to wait for a full reverse sweep.
- This is literally how a real building **elevator** operates — hence the nickname.

### 2.4 C-SCAN (Circular SCAN)
- Like SCAN, but the head **only services requests in one direction**. Upon reaching the end, it **jumps back to the beginning** (without servicing on the way back) and starts a fresh sweep in the same direction.
- ✅ Provides a **more uniform wait time** than SCAN, since it treats the disk as a **circular list** — no track waits disproportionately longer just because of which "end" it's near.
- ❌ The "jump back" (no requests serviced) is wasted movement.

### 2.5 LOOK
- Just like SCAN, but the head **doesn't necessarily go all the way to the physical end of the disk** — it only goes as far as the **last request in that direction**, then reverses. ("Looks ahead" to see if there are more requests before deciding to keep going.)
- ✅ Saves unnecessary travel to the disk's physical edges when there's nothing to service there.

### 2.6 C-LOOK
- Combination of **C-SCAN + LOOK**: services requests in one direction only up to the **last request** (not the physical end), then jumps back to the **first pending request** (not the physical start) to begin the next sweep.
- Most efficient/realistic variant used in practice.

### Summary Table

| Algorithm | Direction Change? | Services Both Directions? | Starvation Risk | Head Movement Efficiency |
|---|---|---|---|---|
| FCFS | N/A (order-based) | N/A | No | Poor (can zigzag) |
| SSTF | Dynamic (nearest first) | N/A | **Yes** | Good (locally) |
| SCAN | Yes, at disk ends | Yes | No | Good |
| C-SCAN | Yes, jumps back | One direction only | No | Uniform wait, some wasted jump |
| LOOK | Yes, at last request | Yes | No | Better than SCAN (no wasted edge travel) |
| C-LOOK | Yes, jumps to first request | One direction only | No | Best of the circular variants |

**Real-life analogy for SCAN vs C-SCAN vs LOOK:**
- **SCAN** = the elevator goes all the way to floor 20 (even if no one's there) before reversing.
- **LOOK** = the elevator only goes as high as the highest floor with an actual request (say floor 18), then reverses — no wasted trip to floor 20.
- **C-SCAN** = the elevator only ever goes up, and once it hits the top, it takes an "express" ride back down to the ground floor without stopping, then starts sweeping up again — like a paternoster lift or a one-way conveyor.
- **C-LOOK** = same as C-SCAN, but the express "reset" only goes back to the *lowest pending request floor*, not all the way to the physical ground floor if nobody's waiting there.

---

## Interview Q&A — Disk Scheduling

**Q1: Why is seek time the dominant factor in disk scheduling, and does this apply to SSDs too?**
> Seek time (moving the physical read/write head) is dominant on **HDDs** because it involves real mechanical movement — orders of magnitude slower than the actual data transfer once positioned. On **SSDs**, there's no mechanical head — access time is roughly uniform regardless of "location" (it's all electronic, flash-based) — so classic disk scheduling algorithms like SCAN/C-LOOK are largely **irrelevant for SSDs**; modern OS I/O schedulers often detect SSDs and switch to simpler schedulers (like Linux's "none"/"noop" scheduler) since there's no seek-time benefit to reorder for.

**Q2: Why might SSTF cause starvation, and how does SCAN/C-LOOK fix that?**
> SSTF always greedily picks the closest pending request — if requests keep clustering near the current head position, a request far away can be repeatedly "overtaken" and starved indefinitely, exactly like SJF causing CPU-scheduling starvation. SCAN/LOOK fix this by enforcing a **structured sweep pattern** — the head must eventually pass through every region of the disk in its sweep, guaranteeing every request gets serviced within a bounded number of sweeps.

**Q3: Why is C-LOOK generally preferred in real systems over plain SCAN?**
> C-LOOK avoids two inefficiencies of SCAN: (1) it doesn't travel all the way to the disk's physical edges if there's nothing to service there (like LOOK), and (2) by always sweeping in one direction and jumping back, it gives more **uniform** wait times across all disk regions — SCAN's back-and-forth pattern causes a "middle-of-disk favored" bias where middle tracks get serviced more frequently than edge tracks.

**Q4: How does this relate to Linux's real I/O schedulers today?**
> Linux has historically offered multiple I/O schedulers: `cfq` (Completely Fair Queuing — balances fairness across processes), `deadline` (ensures no request starves beyond a deadline — conceptually related to aging), and `noop`/`none` (minimal reordering, ideal for SSDs where seek time doesn't matter). Modern Linux defaults often use `mq-deadline` or `kyber` for SSD-optimized multi-queue I/O. Understanding classic algorithms like SCAN/C-LOOK gives you the conceptual foundation for why these real schedulers exist and what trade-offs (fairness vs throughput vs starvation-avoidance) they're balancing.

**Q5: Practical example — why does defragmenting an HDD improve performance but defragmenting an SSD is pointless (and even harmful)?**
> Defragmentation rearranges file data to be more **physically contiguous** on disk, reducing the head's seek distance when reading a file — directly helps HDDs due to real mechanical seek costs. SSDs have **no seek time penalty** for non-contiguous data (uniform electronic access), so defragmenting provides no read speed benefit — and actively **harms** SSD lifespan, since defragmentation causes many extra write cycles, and SSD flash cells have a limited number of write cycles before wearing out.

---

**Next up (File 15):** Inter-Process Communication Deep Dive *(new topic)* — Pipes, Named Pipes (FIFOs), Message Queues, Shared Memory, Sockets, Signals.
