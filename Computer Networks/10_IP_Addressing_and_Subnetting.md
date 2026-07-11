# 10: IP Addressing & Subnetting

## 1. What is an IP Address?
- A **logical address** assigned to a device on a network, used at the **Network Layer** for routing data across networks.
- **IPv4** addresses are **32 bits**, written as **4 octets** (dotted decimal notation), e.g., `192.168.1.1` — each octet ranges from 0–255.

```
IPv4 Address: 192  .  168  .   1   .   1
             (8bits) (8bits) (8bits) (8bits)  = 32 bits total
```

## 2. Structure of an IP Address
- Every IP address is logically divided into two parts:
  - **Network ID (Network portion):** Identifies which network the device belongs to.
  - **Host ID (Host portion):** Identifies the specific device within that network.
- The **Subnet Mask** determines where the boundary between Network ID and Host ID falls.

```
IP Address:    192.168.1.  10
Subnet Mask:   255.255.255.0
               |__Network__|Host|
               (first 3 octets = network, last octet = host)
```

## 3. Classful Addressing (Legacy System)
- The **original** IPv4 addressing scheme, dividing the address space into fixed **classes** based on the first few bits.

| Class | First Bits | First Octet Range | Default Mask | Network/Host Split | Use Case |
|---|---|---|---|---|---|
| **A** | 0 | 0 – 127 | 255.0.0.0 (/8) | 8 bits network, 24 bits host | Very large networks (millions of hosts) |
| **B** | 10 | 128 – 191 | 255.255.0.0 (/16) | 16 bits network, 16 bits host | Medium-large networks (~65,000 hosts) |
| **C** | 110 | 192 – 223 | 255.255.255.0 (/24) | 24 bits network, 8 bits host | Small networks (~254 hosts) |
| **D** | 1110 | 224 – 239 | N/A (not for hosts) | — | Multicast |
| **E** | 1111 | 240 – 255 | N/A (not for hosts) | — | Reserved/Experimental |

> **Extra — Why Classful addressing was abandoned:** Classful addressing was **wasteful** — a company needing 300 hosts would have to be assigned an entire Class B network (65,534 available addresses), wasting the remaining ~65,000+ addresses. This inefficiency, combined with the rapid growth of the Internet, led directly to the adoption of **Classless (CIDR) addressing**.

## 4. Classless Addressing — CIDR (Classless Inter-Domain Routing)
- Instead of fixed class boundaries, CIDR allows the **network/host split to occur at ANY bit position**, specified using **"slash notation"** — e.g., `/24` means the first 24 bits are the network portion.
- Written as: `IP_address/prefix_length`, e.g., `192.168.1.0/24`.
- Allows much more **efficient allocation** — a network needing just 300 hosts can be given a `/23` block (510 usable hosts) instead of an entire wasteful Class B.

> **Extra — Quick CIDR prefix-to-hosts reference table (memorize for interviews):**

| CIDR (/n) | Subnet Mask | Total Addresses | Usable Hosts (−2 for Network+Broadcast) |
|---|---|---|---|
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 (commonly used for point-to-point router links) |

## 5. Special/Reserved IP Addresses

| Address | Purpose |
|---|---|
| `0.0.0.0` | Represents "this network" or an unspecified address |
| `255.255.255.255` | Limited broadcast address (broadcasts to all devices on the local network) |
| `127.0.0.0/8` | Loopback range (e.g., `127.0.0.1` = "localhost," refers to the device itself) |
| `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16` | Private IP address ranges (not routable on the public Internet; used within private/internal networks) |
| First address in a subnet | Network Address (identifies the network itself, not a usable host) |
| Last address in a subnet | Broadcast Address (used to send data to all hosts within that subnet) |

## 6. Subnetting — Worked Example
**Problem:** Given the network `192.168.1.0/24`, subnet it to create **4 subnets**.

**Step 1:** To create 4 subnets, we need enough bits to represent 4 distinct values: `2^n ≥ 4` → `n = 2` bits borrowed from the host portion.

**Step 2:** New prefix length = `/24 + 2 = /26`. New subnet mask = `255.255.255.192`.

**Step 3:** Each subnet now has `2^(32-26) = 2^6 = 64` total addresses (62 usable hosts, after subtracting Network + Broadcast addresses).

**Step 4:** List the resulting subnets:

| Subnet # | Network Address | Usable Host Range | Broadcast Address |
|---|---|---|---|
| 1 | 192.168.1.0/26 | 192.168.1.1 – 192.168.1.62 | 192.168.1.63 |
| 2 | 192.168.1.64/26 | 192.168.1.65 – 192.168.1.126 | 192.168.1.127 |
| 3 | 192.168.1.128/26 | 192.168.1.129 – 192.168.1.190 | 192.168.1.191 |
| 4 | 192.168.1.192/26 | 192.168.1.193 – 192.168.1.254 | 192.168.1.255 |

```
Original network: 192.168.1.0/24  (256 addresses)
                          |
           split into 4 subnets (borrow 2 bits)
                          |
   /26        /26         /26         /26
[Subnet1]  [Subnet2]  [Subnet3]  [Subnet4]
 .0-.63    .64-.127   .128-.191  .192-.255
```

## 7. VLSM (Variable Length Subnet Masking)
- Standard subnetting divides a network into **equal-sized** subnets — but real-world departments often need **different numbers of hosts** (e.g., Sales needs 50 hosts, IT needs 10, a router link needs just 2).
- **VLSM** allows subnets of **different sizes** to be carved out of the same original network block, avoiding wasted address space that would occur if every subnet had to be the same (largest-needed) size.

> **Extra — Worked VLSM example:** Given `192.168.1.0/24`, allocate for: Dept A (100 hosts), Dept B (50 hosts), Dept C (20 hosts), and a Router link (2 hosts).
> 1. **Dept A (100 hosts):** needs `2^n - 2 ≥ 100` → `n=7` (`2^7-2=126`) → use a `/25` subnet: `192.168.1.0/25` (usable: .1–.126)
> 2. **Dept B (50 hosts):** needs `2^n - 2 ≥ 50` → `n=6` (`2^6-2=62`) → use a `/26` subnet: `192.168.1.128/26` (usable: .129–.190)
> 3. **Dept C (20 hosts):** needs `2^n - 2 ≥ 20` → `n=5` (`2^5-2=30`) → use a `/27` subnet: `192.168.1.192/27` (usable: .193–.222)
> 4. **Router link (2 hosts):** needs `2^n - 2 ≥ 2` → `n=2` (`2^2-2=2`) → use a `/30` subnet: `192.168.1.224/30` (usable: .225–.226)
>
> Notice how VLSM assigns **larger blocks first**, then progressively smaller blocks — this is the standard practical approach to avoid fragmentation/overlap issues.

---

## Interview Questions & Answers

**Q1. What is the difference between the Network ID and Host ID portions of an IP address?**
The Network ID identifies which specific network a device belongs to, while the Host ID identifies the specific individual device within that network. The Subnet Mask determines exactly where the boundary between these two portions falls within the 32-bit IPv4 address.

**Q2. What is the difference between Classful and Classless (CIDR) IP addressing?**
Classful addressing divides the IPv4 address space into fixed classes (A, B, C, etc.), each with a predetermined, rigid network/host bit split — this often led to significant address wastage. Classless (CIDR) addressing allows the network/host boundary to be placed at ANY bit position (indicated via slash notation like /24), enabling much more efficient, flexible allocation of IP address blocks that closely match actual host requirements.

**Q3. Why was Classful addressing considered wasteful, and give an example?**
Because it forced organizations into fixed-size address blocks that rarely matched their actual needs — for example, a company needing just 300 host addresses would have to be allocated an entire Class B network (with 65,534 available addresses), wasting well over 65,000 unused addresses that couldn't be allocated to anyone else, given the strict classful boundaries.

**Q4. How many usable host addresses are available in a /28 subnet, and why do we subtract 2?**
A /28 subnet has `2^(32-28) = 2^4 = 16` total addresses, but only 14 are usable for hosts — we subtract 2 because the FIRST address in any subnet is reserved as the Network Address (identifying the subnet itself, not assignable to a host), and the LAST address is reserved as the Broadcast Address (used to send data to all hosts in that subnet).

**Q5. What is the purpose of Subnetting?**
Subnetting divides a larger network into smaller, more manageable sub-networks (subnets), which helps improve network organization/management, reduces broadcast traffic (broadcasts stay contained within a smaller subnet rather than a huge flat network), improves security (by segmenting different groups of devices), and allows more efficient allocation of IP address space to match actual departmental/organizational needs.

**Q6. Walk through how you'd subnet a /24 network into 8 equal subnets.**
To create 8 subnets, we need `2^n ≥ 8`, so `n = 3` bits must be borrowed from the host portion. The new prefix length becomes `/24 + 3 = /27`, giving a subnet mask of 255.255.255.224. Each resulting subnet will have `2^(32-27) = 2^5 = 32` total addresses (30 usable hosts each), with subnet boundaries falling at every 32 addresses (e.g., .0/27, .32/27, .64/27, .96/27, .128/27, .160/27, .192/27, .224/27).

**Q7. What is VLSM, and why is it useful compared to standard fixed-size subnetting?**
VLSM (Variable Length Subnet Masking) allows subnets of DIFFERENT sizes to be created from the same original network block, rather than forcing every subnet to be the same fixed size. This is useful because real-world network segments often have very different host count requirements (e.g., a large department needing 100 hosts versus a router link needing only 2) — VLSM lets you allocate appropriately-sized blocks for each need, avoiding the significant address wastage that would occur if every subnet had to match the size of the LARGEST required subnet.

**Q8. What are Private IP address ranges, and why are they important?**
Private IP address ranges (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) are reserved blocks of IP addresses that are NOT routable on the public Internet — they can be freely reused within any organization's internal/private network without conflicting with addresses used elsewhere in the world. They're important because they allow the vast number of devices on private networks worldwide to use IP addressing internally without requiring a unique, globally-routable public IP address for every single device, helping conserve the limited IPv4 address space (and enabling NAT to bridge private networks to the public Internet).

**Q9. What is the Loopback address, and what is it used for?**
The Loopback address range is 127.0.0.0/8, most commonly represented by 127.0.0.1, and it refers to "this same device" — any traffic sent to a loopback address is routed internally back to the sending device itself, without ever actually leaving onto the physical network. It's commonly used for testing/development purposes (e.g., running a local web server and accessing it via `localhost` or `127.0.0.1` without needing actual network connectivity).

**Q10. Given the network 172.16.0.0/16, how would you subnet it to support 500 hosts per subnet, and how many such subnets could you create?**
For 500 hosts, we need `2^n - 2 ≥ 500`, so `n = 9` (`2^9 - 2 = 510` usable hosts) — meaning we need 9 bits for the host portion, giving a subnet mask of `/23` (32 - 9 = 23). Since the original network is a /16, and each subnet uses a /23 (borrowing 23-16=7 bits for subnetting), we can create `2^7 = 128` such subnets from the original /16 block.

**Q11. What is the difference between a Network Address and a Broadcast Address within a subnet?**
The Network Address is the FIRST address in a subnet's range (where all host bits are set to 0) — it identifies the subnet as a whole and cannot be assigned to any individual host device. The Broadcast Address is the LAST address in a subnet's range (where all host bits are set to 1) — it's used to send a single packet that should be delivered to ALL hosts within that subnet simultaneously, and also cannot be assigned to an individual host.

**Q12. In VLSM design, why is it standard practice to allocate the LARGEST address blocks first, before allocating smaller ones?**
Allocating larger blocks first ensures they can be placed on proper address boundaries without fragmentation issues — if you allocated smaller blocks first and then tried to fit a larger block afterward, you might find that the larger block doesn't fit cleanly into the remaining, potentially fragmented address space (since larger blocks require specific alignment based on their size). Starting with the largest requirements and working down to the smallest ensures efficient, non-overlapping, cleanly-aligned address allocation throughout the entire process.
