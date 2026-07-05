# 17 — OS Security Basics *(New Topic — Not in Original Notes)*

> Not in your original notes, but security fundamentals are increasingly asked about in interviews, especially for backend/infra/systems roles. This ties together protection mechanisms mentioned throughout (memory protection, system calls, user/kernel mode) into a security-focused view.

## 1. Authentication vs Authorization

| Term | Question it answers | Example |
|---|---|---|
| **Authentication** | "**Who are you?**" | Logging in with a username/password, fingerprint, SSH key |
| **Authorization** | "**What are you allowed to do?**" | Checking if this authenticated user has permission to delete a file |

**Real-life analogy:** Authentication is like a **bouncer checking your ID** at the door to confirm who you are. Authorization is like the **wristband color** you get after entering — a "VIP" wristband lets you into the backstage area, a "general admission" one doesn't, even though both people were equally authenticated at the door.

---

## 2. Privilege Rings / Protection Rings

- Modern CPUs (x86) implement **protection rings** — hardware-enforced privilege levels.

```
        Ring 3 (Least privileged)  <- User applications
            |
        Ring 2  <- (rarely used today)
            |
        Ring 1  <- (rarely used today)
            |
        Ring 0 (Most privileged)   <- Kernel
```

- **Ring 0**: the kernel runs here — full, unrestricted access to hardware. This is the "**Kernel Mode**" from File 2.
- **Ring 3**: user applications run here — restricted, must go through **system calls** to request privileged operations. This is "**User Mode**" from File 2.
- Rings 1 and 2 exist in the hardware spec but are largely unused by mainstream OSes (historically intended for device drivers with intermediate privilege).
- Modern virtualization adds a further concept: **Ring -1** (hypervisor mode), where a hypervisor (like VMware ESXi or KVM) runs *below* even the guest OS's kernel, managing multiple virtual machines.

**Real-life analogy:** Think of a **secure government building** with clearance levels. Ring 0 (kernel) is like the President's inner security detail — full access to everything. Ring 3 (user apps) is like a general visitor — you must go through a **formal request process** (system call) even to get a meeting with someone who *does* have clearance, rather than freely walking into restricted areas yourself.

---

## 3. Memory Protection as a Security Mechanism

This directly extends **File 10's** relocation/limit register discussion — but framed for security:

- **Address space isolation**: each process has its own virtual address space (File 12) — Process A literally **cannot** address Process B's memory, because A's logical addresses only translate (via A's own page table) into A's own physical frames.
- **Buffer Overflow**: a classic vulnerability where a program writes **more data into a buffer than it was allocated for**, overwriting adjacent memory (potentially including the **return address** on the stack) — attackers exploit this to redirect program execution to malicious code.

```
Stack (growing downward, simplified):
+------------------+
|  Return Address   |  <-- if overwritten by overflow, attacker controls where execution jumps next!
+------------------+
|  Saved Registers  |
+------------------+
|  Local Buffer[8]  |  <-- if you write 20 bytes into an 8-byte buffer, it overflows upward into the above
+------------------+
```

### Modern Mitigations
| Mitigation | What it does |
|---|---|
| **ASLR (Address Space Layout Randomization)** | Randomizes where the stack, heap, and libraries are loaded in memory each run, making it much harder for an attacker to predict exact addresses to jump to. |
| **DEP / NX bit (No-eXecute)** | Marks certain memory regions (like the stack) as **non-executable** — even if an attacker manages to inject malicious code into a buffer, the CPU refuses to *execute* instructions from that region. |
| **Stack Canaries** | A known random value placed just before the return address on the stack; before a function returns, the OS/compiler checks if the canary value is still intact — if it's been overwritten (by a buffer overflow), the program aborts before the corrupted return address can be used. |

**Real-life analogy:** ASLR is like a bank randomly rearranging the layout of its vault room every single day — even if a robber knows roughly "the vault is somewhere in this building" (the general memory region), they can't reliably predict the exact walk-in path (exact addresses) without extensive fresh reconnaissance each time. A **stack canary** is like a tripwire alarm placed just before the vault door — if a robber tunnels through the wall (overflowing a buffer) and disturbs the tripwire on the way, the alarm sounds *before* they reach the vault door (return address), even if they haven't actually opened it yet.

---

## 4. Access Control Models

### 4.1 Access Control List (ACL)
- Each **resource** (file, etc.) has a list specifying **which users/groups** have **which permissions** (read/write/execute).
- **Unix permission bits** (`rwxr-xr--`) are a simplified form of ACL — 3 permission sets (owner, group, others) × 3 permission types (read, write, execute).

```
-rwxr-xr--  1 alice  staff  ...  script.sh
 |||||||||
 |||||||└── others: read only
 ||||└└──── group: read + execute
 └└└─────── owner (alice): read + write + execute
```

### 4.2 Role-Based Access Control (RBAC)
- Instead of assigning permissions directly to individual users, permissions are assigned to **roles** (e.g., "admin," "editor," "viewer"), and users are assigned to roles.
- Much easier to manage at scale — e.g., promoting an employee just means changing their role, not re-configuring dozens of individual permission entries.

**Real-life analogy:** ACL is like a **guest list at a private party door**, checked name-by-name. RBAC is like an **office building's keycard system** — instead of programming each individual employee's card with a custom list of doors, you assign them to a "Role: Engineering" badge type, and that badge type is pre-configured with access to the engineering floors. Change someone's role, and their access updates automatically — no need to reprogram their card individually.

---

## 5. Principle of Least Privilege

> A process/user should be granted **only the minimum permissions necessary** to perform its function — nothing more.

- **Why it matters:** if a process running with excessive privileges gets compromised (e.g., via a buffer overflow exploit), the attacker inherits **all** of that process's privileges. Running things with minimal necessary privilege limits the "blast radius" of any single compromise.
- **Real-world implementation examples:**
  - Running web server processes as a **non-root user** (`www-data`, `nginx`, etc.) instead of `root`, so a web app exploit can't directly take over the whole system.
  - **Sandboxing/containers** (Docker, discussed more in File 18) restrict what system resources a process can see/touch, even if it's compromised.
  - **`sudo`** — instead of permanently logging in as root, users temporarily elevate privileges only for specific commands, and every use is **logged** (auditability).

**Real-life analogy:** This is exactly why a hotel gives housekeeping staff a **master key that only opens guest room doors**, not one that also opens the hotel's cash safe or security office — even if a housekeeping key gets lost or stolen (the "process" is compromised), the damage is contained to guest rooms, not the entire hotel's most sensitive assets.

---

## 6. Sandboxing (Conceptual Preview — Full Detail in File 18)

- A **sandbox** is an isolated execution environment that restricts what a program can do — limiting its access to the file system, network, and other processes.
- Directly builds on **memory protection + system call restriction** concepts already covered: a sandboxed process's system calls might be **filtered** (e.g., via Linux's `seccomp` — "secure computing mode" — which restricts which system calls a process is even allowed to *invoke*).

**Real-life analogy:** A sandbox is like letting a toddler play in an actual **sandbox** in the backyard rather than the whole house — they can dig, build, and make a mess freely within that contained area, but they physically **cannot** reach the kitchen knives or the front door, no matter what they try, because the boundary is enforced structurally, not just by trust/instruction.

---

## Interview Q&A — OS Security Basics

**Q1: How does memory protection (relocation/limit registers, page tables) directly serve as a security mechanism?**
> Because each process's logical addresses are translated (via its own page table, checked against limit registers) into physical addresses *only within that process's allocated frames*, a process **structurally cannot** address another process's or the OS's memory — even a malicious process can't simply "guess" an address and read another process's secrets, because the hardware-enforced translation makes such addresses meaningless/invalid for that process, triggering a trap (segmentation fault) instead of succeeding.

**Q2: What is a buffer overflow, and name two modern mitigations against it.**
> A buffer overflow occurs when a program writes more data into a fixed-size buffer than it can hold, overwriting adjacent memory — potentially including a function's return address on the stack, letting an attacker redirect program execution. Two mitigations: **ASLR** (randomizes memory layout, making exploit addresses unpredictable) and **stack canaries** (detect stack corruption before a corrupted return address is used) — DEP/NX bit (making the stack non-executable) is a third commonly cited one.

**Q3: Explain the Principle of Least Privilege with a concrete engineering example.**
> Running a web server process as a dedicated low-privilege user (e.g., `www-data`) instead of `root` means that if an attacker exploits a vulnerability in the web application, they only gain the limited permissions of `www-data` — they can't, for example, read `/etc/shadow` or install a system-wide rootkit, because the OS's permission system (File 13/17) structurally blocks that user from those actions, regardless of what the compromised process "wants" to do.

**Q4: What's the difference between authentication failing vs. authorization failing, in terms of HTTP status codes (a common practical follow-up)?**
> Authentication failure → **401 Unauthorized** ("I don't know who you are, or your credentials are invalid"). Authorization failure → **403 Forbidden** ("I know who you are, but you don't have permission to do this"). This distinction directly mirrors the authentication-vs-authorization conceptual split.

**Q5: How does `seccomp` (used heavily in Docker) relate to the system call concept from File 2?**
> `seccomp` lets you define a **whitelist/blacklist of system calls** a process is permitted to invoke. Recall from File 2 that system calls are the **only** gateway from user mode to kernel mode — by restricting *which* system calls a sandboxed process can even attempt, you dramatically shrink its "attack surface" against the kernel, even if the process itself is fully compromised by malicious code. It's security applied precisely at the user-mode/kernel-mode boundary.

**Q6: Real-world example: why do container orchestration systems like Kubernetes emphasize "run as non-root" as a security best practice?**
> Because a container, while isolated in many ways (namespaces, cgroups — File 18), still ultimately shares the **host kernel**. If a process running as `root` *inside* a container manages to exploit a kernel vulnerability or a container escape bug, running as root dramatically increases the potential damage (full host root access) compared to running as a low-privilege user, where even a successful escape would inherit only limited privileges — directly the Least Privilege principle applied to modern container security.

---

**Next up (File 18):** Virtualization & Containers *(new topic)* — Hypervisors (Type 1 vs Type 2), VMs vs Containers, Namespaces & Cgroups (how Docker actually works under the hood).
