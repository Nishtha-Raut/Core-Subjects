# 13 — File Systems *(New Topic — Not in Original Notes)*

> This topic wasn't in your handwritten/typed notes, but **File Systems are one of the most commonly asked OS interview topics** — especially "how does a file get stored/read from disk" and "what happens when you `rm` a file." Adding it in full here.

## 1. What is a File?

- A **file** is a named collection of related information recorded on secondary storage.
- From the OS's perspective, a file is just an **abstraction** — the OS doesn't care about the file's internal content structure (text, binary, image); it just manages **how the file's data is stored, retrieved, and protected**.

### File Attributes
| Attribute | Description |
|---|---|
| Name | Human-readable identifier |
| Identifier (inode number on Unix) | Unique number the OS uses internally |
| Type | Regular file, directory, symbolic link, device file, etc. |
| Size | Current size in bytes |
| Protection | Permissions (read/write/execute for owner/group/others) |
| Timestamps | Creation, last modified, last accessed |

### File Operations (mirrors System Calls from File 2)
- Create, Delete, Open, Close, Read, Write, Reposition (seek), Get/Set attributes.

---

## 2. File Allocation Methods (How File Data Maps to Disk Blocks)

A file's data is stored across **disk blocks**. The file system needs a strategy to track which blocks belong to which file.

### 2.1 Contiguous Allocation
- Each file occupies a **contiguous set of disk blocks**.
- ✅ Fast sequential AND random access (just compute `start_block + offset`).
- ❌ **External fragmentation** (same problem as contiguous memory allocation — File 10!).
- ❌ Hard to grow a file (no room to expand contiguously if neighbors are occupied).

### 2.2 Linked Allocation
- Each file is a **linked list of disk blocks**, scattered anywhere on disk. Each block contains a pointer to the next block.
- ✅ No external fragmentation. Files can grow easily.
- ❌ **No random access** (must traverse the linked list from the start to reach block `n`).
- ❌ Reliability risk — if one pointer gets corrupted, you lose the rest of the chain.
- ❌ Pointers waste some space in every block.
- **Variant: FAT (File Allocation Table)** — used by older Windows/USB drives — keeps all the "next block" pointers in one central table (in memory) instead of scattered inside each block, improving reliability and enabling faster traversal (since the table can be cached in RAM).

### 2.3 Indexed Allocation
- Each file has an **index block** containing pointers to **all** its data blocks (like a table of contents).
- ✅ Supports **direct/random access** (look up the index block, then jump straight to any data block).
- ✅ No external fragmentation.
- ❌ Overhead of the index block itself; for very small files, the index block may waste more space than the file's actual data.
- **This is essentially what Unix inodes use** — see below.

**Real-life analogy:**
- **Contiguous** = booking a single row of connected seats at a movie theater — great if available, but often you can't find a matching contiguous block (fragmentation).
- **Linked** = a scavenger hunt where each clue tells you where the next clue is — flexible, but you can't jump straight to clue #10 without going through 1-9 first.
- **Indexed** = a table of contents at the front of a book listing every chapter's exact page number — you can jump directly to any chapter (block) instantly.

---

## 3. Unix Inodes (Indexed Allocation in Practice)

- An **inode** ("index node") is a data structure storing **metadata** about a file: permissions, owner, size, timestamps, and **pointers to data blocks**.
- **Crucially, an inode does NOT store the filename** — the filename-to-inode mapping lives in the **directory** (a directory is itself just a special file mapping names → inode numbers).

### Multi-level Index in Inodes
To support both small and huge files efficiently, inodes typically use:
- A few **direct pointers** (point straight to data blocks — fast for small files).
- **Single indirect pointer** (points to a block that itself contains more pointers to data blocks).
- **Double indirect pointer** (points to a block of pointers, each pointing to *another* block of pointers to data).
- **Triple indirect pointer** (one more level deep) — allows extremely large files.

**Real-life analogy:** Think of it like a phone's contact system: a few "favorites" (direct pointers) are instantly accessible, medium-sized contact lists route through one lookup (single indirect), and a massive corporate directory might need you to look up a department (double indirect) which then looks up a team (another level) to finally find the person (triple indirect) — more levels of indirection needed only as the "file" (contact list) grows.

### Why deleting a file with `rm` is (usually) fast
- `rm` doesn't erase the actual data blocks — it just **removes the directory entry** (the name→inode mapping) and decrements the inode's **link count**. If the link count reaches 0 (no more names point to this inode) **and** no process has the file open, the inode and its data blocks are marked as **free** for reuse. This is why "file recovery" tools can sometimes recover deleted files — the actual data often still physically exists on disk until overwritten.

---

## 4. Directory Structure

| Structure | Description |
|---|---|
| **Single-level** | One directory for the entire system — all files must have unique names (very limiting, mostly historical). |
| **Two-level** | One directory per user — avoids naming collisions between users, but no further organization within a user's space. |
| **Tree-structured (hierarchical)** | Directories can contain subdirectories, forming a tree — this is what modern OSes use (folders within folders). |
| **Acyclic Graph** | Allows **shared files/directories** via links (like Unix symbolic/hard links) — a file can appear in multiple directories without duplication. |

### Hard Links vs Symbolic (Soft) Links

| Hard Link | Symbolic (Soft) Link |
|---|---|
| Multiple directory entries **point to the same inode**. | A special file that stores the **path** to another file. |
| Deleting the original file **doesn't break** the hard link (data persists as long as link count > 0). | Deleting the original file **breaks** the symlink ("dangling link"). |
| Can't span across different file systems/partitions. | Can point to files on different file systems/partitions. |
| Can't link to directories (in most systems, to prevent cycles). | Can link to directories. |

**Real-life analogy:** A **hard link** is like having two different nicknames that both refer to the exact same person — if one nickname stops being used, the person still exists and can be reached by the other nickname. A **symbolic link** is like a sticky note that says "this person now lives at 123 Main St." — if that person moves away or the address becomes invalid, the sticky note is now "dangling," pointing to nothing useful.

---

## 5. Free Space Management on Disk

Similar in spirit to free space management in memory (File 10), but for disk blocks:

| Technique | Description |
|---|---|
| **Bit Vector (Bitmap)** | One bit per block: `1` = allocated, `0` = free. Simple, but needs scanning to find free blocks. |
| **Linked List** | Free blocks are linked together in a list. No wasted space for a bitmap, but slow to find a contiguous run of free blocks. |
| **Grouping** | The first free block stores addresses of the next `n` free blocks, making bulk free-block retrieval faster. |
| **Counting** | Since blocks are often allocated/freed in contiguous chunks, store `(starting block address, count of free blocks)` pairs instead of one entry per block — more compact. |

---

## 6. Journaling File Systems (Crash Consistency)

- A **journal** (aka write-ahead log) records the file system operations that are **about to happen** *before* they're actually applied to the main data structures on disk.
- If the system crashes mid-operation (e.g., power loss), on reboot the file system can **replay the journal** to complete or safely roll back the interrupted operation — avoiding corruption.
- **Examples:** ext3/ext4 (Linux), NTFS (Windows).

**Real-life analogy:** Journaling is like a construction crew that keeps a **detailed to-do checklist with checkboxes** before starting any risky renovation step. If the power goes out mid-renovation (crash), when work resumes, they check the list: any step marked "started but not confirmed complete" gets either finished properly or safely undone — instead of leaving the building in a half-demolished, unsafe state.

---

## Interview Q&A — File Systems

**Q1: What's stored in an inode, and what's NOT stored there?**
> An inode stores file metadata — permissions, owner, size, timestamps, and pointers to data blocks. It does **NOT** store the filename — that mapping lives in the directory entry, which is why you can have multiple names (hard links) pointing to the same inode.

**Q2: Why does deleting a huge file on Linux with `rm` return almost instantly, regardless of file size?**
> Because `rm` only removes the directory entry and decrements the inode's link count — it doesn't have to touch/erase the actual data blocks (which could be huge). The space is simply marked free for future reuse. This is why file recovery tools can sometimes recover "deleted" files — the data isn't actually wiped, just unlinked.

**Q3: What's the difference between a hard link and a symbolic link, practically?**
> A hard link is essentially a second name for the *exact same* underlying file (same inode) — deleting one name doesn't affect the data as long as another link exists. A symbolic link is a separate small file that just *stores a path* to another file — if the target is deleted or moved, the symlink becomes a dangling/broken reference.

**Q4: Why do file systems use indexed allocation (inodes) instead of pure linked allocation?**
> Linked allocation only supports sequential access (must traverse the chain from the start), which is unacceptably slow for large files where you might want to seek to an arbitrary offset (e.g., seeking to the middle of a video file). Indexed allocation (inode-style) supports **direct/random access** by jumping straight to the needed data block via the index, at the cost of some space for storing the index pointers.

**Q5: What problem does journaling solve, and how does this relate to database transactions?**
> Journaling solves **crash consistency** — ensuring the file system doesn't end up in a corrupted, half-updated state if the system crashes mid-operation. This is conceptually identical to a database's **write-ahead log (WAL)**, used for the same reason — both record intended changes before applying them, so a crash mid-write can be safely rolled forward (completed) or rolled back (undone) on recovery, guaranteeing atomicity.

**Q6: Real-world example: explain why copying a file across two different physical hard drives is slower than a "move" within the same drive.**
> A "move" (rename) **within the same file system/drive** is just a metadata operation — updating the directory entry to point to the same inode/data blocks in a new location; the actual data never moves, so it's nearly instant. Moving/copying **across different drives** requires physically reading every data block from the source drive and writing it to the destination drive (since inode/block numbers are only meaningful within a single file system) — genuinely proportional to file size, hence much slower.

---

**Next up (File 14):** Disk Scheduling Algorithms *(new topic)* — FCFS, SSTF, SCAN, C-SCAN, LOOK, C-LOOK.
