# 03 — Boot Process, 32-bit vs 64-bit OS, and Memory Hierarchy

## 1. What Happens When You Turn On Your Computer?

Step by step:

1. **PC On** — power is supplied.
2. **CPU initializes itself** and looks for a firmware program called the **BIOS**, stored in a **BIOS chip** (a ROM chip on the motherboard that allows access & setup of the computer system at the most basic level).
   - In **modern PCs**, the CPU loads **UEFI** (Unified Extensible Firmware Interface) instead of legacy BIOS.
3. **CPU runs the BIOS**, which tests and initializes system hardware, and loads configuration settings. If something's wrong (e.g., missing RAM), an error is thrown and the boot process stops.
   - This is called **POST** (Power-On Self-Test).
   - UEFI can do a lot more than just initialize hardware — it's essentially a **tiny operating system**. E.g., Intel CPUs have the **Intel Management Engine**, which powers features like remote management of business PCs.
4. **BIOS hands off responsibility** for booting your PC to your OS's **bootloader**.
   - The BIOS looks at the **MBR (Master Boot Record)** — a special boot sector at the beginning of a disk. The MBR contains code that loads the rest of the OS, known as the **bootloader**.
   - The BIOS executes the bootloader, which takes over and begins booting the actual OS (Windows, Linux, etc.).
   - In other words: the BIOS/UEFI examines a storage device for a small program (in the MBR, or on an **EFI system partition** for UEFI systems) and runs it.
5. **The bootloader** is a small program with the big task of booting the rest of the OS — it **boots the Kernel first**, then hands off to **User Space**.
   - Windows uses **Windows Boot Manager** (`bootmgr.exe`).
   - Most Linux systems use **GRUB**.
   - Macs use **boot.efi**.

### Quick Summary Flow
```
Power On -> CPU looks for BIOS/UEFI -> POST (hardware test) 
   -> BIOS/UEFI finds MBR/EFI partition -> runs Bootloader 
   -> Bootloader loads Kernel -> Kernel initializes -> User Space starts (login screen, GUI, etc.)
```

**Real-life analogy:** Think of booting a computer like starting a big factory in the morning.
- **POST** = the security guard doing a walk-through to check all machines are physically present and working (not "is water in the boiler" level check, just "is everything plugged in").
- **Bootloader (GRUB/Windows Boot Manager)** = the shift supervisor who decides which production line (OS — if you dual-boot Windows/Linux) to start up today.
- **Kernel loading** = turning on the main power grid and core machinery.
- **User space starting** = workers (applications) showing up and starting their jobs once the factory (kernel) is running.

---

## 2. 32-bit vs 64-bit OS

| Property | 32-bit | 64-bit |
|---|---|---|
| Register size | 32-bit registers | 64-bit registers |
| Addressable memory | `2^32` unique addresses ≈ **4 GB** of physical memory | `2^64` unique addresses ≈ **17,179,869,184 GB** |
| Data processed per cycle | 32 bits (4 bytes) | 64 bits (8 bytes) |

### Advantages of 64-bit over 32-bit:

1. **Addressable Memory**: `2^32` vs `2^64` memory addresses — 64-bit can use vastly more RAM.
2. **Resource usage**: Installing more RAM on a 32-bit system doesn't help beyond ~4GB; upgrading to 64-bit lets you actually use that extra RAM.
3. **Performance**: All calculations happen in registers. Bigger registers = bigger calculations per cycle.
   - 32-bit processor executes **4 bytes** of data per instruction cycle.
   - 64-bit processor executes **8 bytes** of data per instruction cycle.
   - (A CPU can execute thousands to billions of instruction cycles per second, depending on design.)
4. **Compatibility**: A **64-bit CPU can run both 32-bit and 64-bit OS**. A 32-bit CPU can **only** run a 32-bit OS.
5. **Better graphics performance**: 8-byte graphics calculations make graphics-intensive apps run faster.

**Real-life analogy:** Imagine registers as **shopping carts**. A 32-bit cart can carry 4 items per trip; a 64-bit cart carries 8 items per trip. If you have 1000 items to move (data to process), the 64-bit cart finishes in half the trips — hence faster performance for data-heavy operations.

---

## 3. Storage Devices / Memory Hierarchy

### The Hierarchy (fastest & most expensive at top → slowest & cheapest at bottom)

```
             Register           |
             Cache               } Primary Memory
             Main Memory (RAM)  |
             -----------------------
             Electronic Disk (SSD)  |
             Magnetic Disk (HDD)     } Secondary Memory
             Optical Disk (CD/DVD)   |
             Magnetic Tapes         |
```

### Definitions

| Level | Description |
|---|---|
| **Register** | Smallest unit of storage, **part of the CPU itself**. Holds an instruction, storage address, or data (bit sequence/characters) being used immediately by the CPU. |
| **Cache** | Additional memory that temporarily stores frequently used instructions/data for quicker CPU access. |
| **Main Memory** | RAM. |
| **Secondary Memory** | Storage media (HDD, SSD, etc.) where the computer permanently stores data & programs. |

### Comparison

| Criterion | Comparison |
|---|---|
| **Cost** | Primary storage is costlier than secondary. Registers are the **most expensive** (expensive semiconductors + engineering effort). Secondary storage is cheaper per GB. |
| **Access Speed** | Primary > Secondary. Within primary: **Registers > Cache > Main Memory**. |
| **Storage Size** | Secondary has vastly more capacity. |
| **Volatility** | Primary memory is **volatile** (data lost on power-off). Secondary is **non-volatile** (data persists). |

**Real-life analogy:** Think of it like your desk and library setup while studying:
- **Register** = the single sheet of paper directly in your hand — instant access, but you can only hold one thing.
- **Cache** = the small stack of notes right in front of you on the desk — quick to grab, holds a handful of frequently used things.
- **Main Memory (RAM)** = your desk drawer — takes a moment to open, but holds much more.
- **Secondary Memory (Disk)** = the library across campus — huge capacity, cheap to "store" things there, but takes real time to walk over and fetch a book, and it's still there even after you leave (non-volatile) unlike your desk which gets cleared (volatile) when you leave for the day.

---

## Interview Q&A — Boot Process, 32/64-bit, Memory Hierarchy

**Q1: What's the difference between BIOS and UEFI?**
> BIOS is legacy firmware stored in a ROM chip that does basic hardware initialization (POST) and looks at the MBR to find a bootloader. UEFI is a modern replacement — faster, supports larger disks (GPT partitioning, unlike MBR's 2TB limit), has a graphical interface, supports secure boot, and functions almost like a mini pre-OS environment.

**Q2: Why can't a 32-bit OS use more than ~4GB of RAM even if you physically install 16GB?**
> Because a 32-bit address bus/register can only represent `2^32` unique memory addresses = 4GB. Any RAM beyond that has no addressable "slot" for the CPU to reference it in a purely 32-bit addressing scheme (in practice OS-level workarounds like PAE exist, but a plain 32-bit OS is fundamentally capped).

**Q3: Explain why registers are faster than RAM, using the memory hierarchy.**
> Registers are physically located inside the CPU itself — literally zero travel distance/time for data. Cache is slightly further (on-chip but separate), RAM is off-chip and requires a bus transfer — introducing latency at each hop away from the CPU core. Speed inversely correlates with distance and capacity in the memory hierarchy.

**Q4: Why is cache "temporary" and smaller than RAM if it's faster?**
> Faster memory (SRAM used in caches) is significantly more expensive per byte than the DRAM used for RAM, and physically larger transistor-wise per bit — so there's a cost/space trade-off. This is why caches only hold a curated set of frequently-used data (via caching algorithms like LRU), rather than everything.

**Q5: What's the practical difference between primary and secondary memory in terms of a running application, say a browser?**
> When Chrome is open and actively rendering a page, that page's DOM/data lives in RAM (primary, volatile) — fast but disappears when you close Chrome or the power goes out. If you download a file via Chrome, it gets written to your SSD/HDD (secondary, non-volatile) — persists across reboots.

---

**Next up (File 4):** Process Management — PCB, Process States, Queues, Swapping, Context Switching, Orphan & Zombie Processes.
