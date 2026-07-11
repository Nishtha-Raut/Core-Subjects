# 11: IPv6 & Fragmentation

## 1. Why IPv6?
- **IPv4** uses 32-bit addresses, giving approximately **4.3 billion** unique addresses — this seemed like plenty in the 1980s, but the explosive growth of the Internet (billions of devices: phones, IoT, servers) has led to **IPv4 address exhaustion**.
- **IPv6** was designed to solve this, using **128-bit** addresses, giving approximately **340 undecillion** (3.4 × 10^38) addresses — for all practical purposes, effectively unlimited.

## 2. IPv6 Address Format
- **128 bits**, written as **8 groups of 4 hexadecimal digits**, separated by colons.

```
IPv6 Address: 2001:0db8:85a3:0000:0000:8a2e:0370:7334
              |___| |___| |___| |___| |___| |___| |___| |___|
               16bit 16bit 16bit 16bit 16bit 16bit 16bit 16bit  = 128 bits total
```

### Shortening Rules
1. **Leading zeros** in each group can be omitted: `0db8` → `db8`.
2. **One** consecutive run of all-zero groups can be replaced with `::` (double colon) — but only **once** per address, to avoid ambiguity about how many groups were compressed.

```
Full:        2001:0db8:0000:0000:0000:0000:1234:5678
Step 1 (drop leading zeros): 2001:db8:0:0:0:0:1234:5678
Step 2 (compress zero run):  2001:db8::1234:5678
```

## 3. IPv4 vs IPv6 Comparison

| Aspect | IPv4 | IPv6 |
|---|---|---|
| Address length | 32 bits | 128 bits |
| Address format | Dotted decimal (e.g., 192.168.1.1) | Hexadecimal, colon-separated (e.g., 2001:db8::1) |
| Total addresses | ~4.3 billion | ~340 undecillion |
| Header complexity | More complex, variable-length options | Simplified, fixed-length header (faster processing by routers) |
| Fragmentation | Done by both routers AND the sending host | Only done by the SENDING HOST (routers do not fragment) |
| Checksum in header | Yes | No (removed — relies on upper layers like TCP/UDP and Link layer for error checking) |
| Built-in Security (IPsec) | Optional, add-on | Originally mandated as built-in (though implementation varies in practice) |
| Configuration | Manual or DHCP | Supports **Stateless Address Autoconfiguration (SLAAC)** — devices can self-configure |
| NAT Requirement | Commonly required (address scarcity) | Not needed (abundant address space allows every device a unique public address) |

## 4. IPv6 Address Types

| Type | Purpose |
|---|---|
| **Unicast** | Identifies a single, specific interface — data sent to it goes to exactly that one interface (same concept as IPv4 unicast). |
| **Multicast** | Identifies a group of interfaces — data sent to it is delivered to ALL interfaces in that group. |
| **Anycast** | Identifies a group of interfaces, but data sent to it is delivered to only the NEAREST (per routing distance) interface in that group — used for things like load-distributed DNS root servers. |

> **Extra — Notably, IPv6 has NO broadcast address type at all** (unlike IPv4's `255.255.255.255`) — broadcast functionality is instead handled through Multicast, which is considered more efficient and controlled.

## 5. Fragmentation

### What is Fragmentation?
- Every network link has a **Maximum Transmission Unit (MTU)** — the largest packet size it can carry in a single frame (commonly 1500 bytes for standard Ethernet).
- If a packet is **larger than the MTU** of a link it needs to cross, it must be broken into smaller pieces — this is **Fragmentation**.
- Fragments are **reassembled** only at the **final destination** (not at intermediate routers), using fields in the IP header: **Identification** (same value for all fragments of one original packet), **Flags** (More Fragments bit), and **Fragment Offset** (position of this fragment's data within the original packet).

```
Original Packet (2000 bytes) needs to cross a link with MTU = 1000 bytes:

[--------- 2000 byte packet ---------]
              |
              v (fragmentation)
[--1000 bytes--][--1000 bytes--]
  Fragment 1      Fragment 2
  (Offset=0,       (Offset=1000,
   More=1)          More=0 - last fragment)
```

### IPv4 vs IPv6 Fragmentation — Key Difference
- **IPv4:** Fragmentation can happen at **any router** along the path, if a packet exceeds the next link's MTU — this adds processing overhead at every router that has to fragment.
- **IPv6:** Routers **do NOT fragment** packets at all. Instead, IPv6 relies on **Path MTU Discovery (PMTUD)** — the sending host determines the smallest MTU along the entire path beforehand, and fragments the packet (if needed) only at the source, before sending. If a packet ends up too large for a link anyway, the router simply **drops it** and sends back an ICMPv6 "Packet Too Big" message to the sender, prompting it to resend with a smaller size.

> **Extra — Why IPv6 removed router-based fragmentation:** Router-based fragmentation is computationally expensive and slows down packet forwarding at every hop that needs to do it. By shifting the responsibility entirely to the sending host (via Path MTU Discovery), IPv6 routers can operate much faster and more simply, since they never need to inspect and potentially fragment packets — they can just forward or drop, a much simpler operation.

> **Extra — Path MTU Discovery (PMTUD) process (commonly asked as a follow-up):**
> 1. Sender initially sends a packet at the size of its own local MTU, with the IPv6 equivalent of the "Don't Fragment" concept implicitly always active (since IPv6 routers never fragment anyway).
> 2. If a router along the path has a smaller MTU on its outgoing link, it drops the oversized packet and sends back an **ICMPv6 "Packet Too Big"** message, indicating the maximum MTU that link can support.
> 3. The sender reduces its packet size accordingly and retransmits — this process may repeat if multiple links along the path have progressively smaller MTUs, until the sender settles on a size that fits the smallest MTU on the entire path.

---

## Interview Questions & Answers

**Q1. Why was IPv6 developed, and what core problem does it solve?**
IPv6 was developed primarily to solve IPv4 address exhaustion — IPv4's 32-bit address space provides only about 4.3 billion unique addresses, which has proven insufficient given the explosive growth of Internet-connected devices (phones, IoT devices, servers, etc.) worldwide. IPv6's 128-bit address space provides approximately 340 undecillion addresses, which is effectively unlimited for all practical foreseeable purposes.

**Q2. How is an IPv6 address formatted, and what are the two main shortening rules?**
An IPv6 address is 128 bits, written as 8 groups of 4 hexadecimal digits separated by colons. The two shortening rules are: (1) leading zeros within each group can be omitted (e.g., "0db8" becomes "db8"), and (2) one single consecutive run of all-zero groups can be replaced with a double colon "::" — but this compression can only be used ONCE per address, to avoid ambiguity about exactly how many zero groups were compressed.

**Q3. What is the most significant difference in how fragmentation is handled between IPv4 and IPv6?**
In IPv4, fragmentation can occur at ANY router along a packet's path if the packet exceeds the MTU of the next link. In IPv6, routers do NOT perform fragmentation at all — fragmentation, if needed, is done ONLY by the original sending host, using Path MTU Discovery to determine the appropriate packet size beforehand; if a packet is too large for a link anyway, the router simply drops it and notifies the sender via an ICMPv6 message, rather than fragmenting it itself.

**Q4. What is Path MTU Discovery (PMTUD), and why is it important in IPv6?**
Path MTU Discovery is the process by which a sending host determines the smallest MTU along the entire path to a destination, by initially sending full-sized packets and adjusting downward based on "Packet Too Big" ICMP messages received from routers along the way if a packet is too large for a given link. It's especially important in IPv6 because, since IPv6 routers never fragment packets themselves, the SENDING host must proactively determine an appropriately-sized packet to avoid having packets dropped mid-path.

**Q5. What is the difference between Unicast, Multicast, and Anycast addressing in IPv6?**
Unicast delivers data to exactly one specific interface. Multicast delivers data to ALL interfaces that are members of a defined multicast group. Anycast delivers data to only the NEAREST interface (based on routing distance) among a group of interfaces sharing the same anycast address — commonly used for services like distributed DNS root servers, where you want a client to reach the closest available server instance.

**Q6. Does IPv6 have a broadcast address, like IPv4's 255.255.255.255?**
No, IPv6 does not have a dedicated broadcast address type at all. Instead, the functionality traditionally provided by broadcast in IPv4 is handled through Multicast addressing in IPv6, which is considered more efficient and controlled, since it only reaches devices that have explicitly joined the relevant multicast group, rather than blasting to every device on the network indiscriminately.

**Q7. Why does the IPv6 header not include a checksum field, unlike IPv4?**
The IPv6 designers removed the header checksum to simplify and speed up packet processing at every router — since error-checking is already reliably handled by mechanisms at other layers (like the Data Link layer's frame check sequence, and the Transport layer's TCP/UDP checksums), having IPv4's redundant header checksum recalculated at every single hop (since fields like TTL/Hop Limit change at each hop, requiring checksum recalculation) was seen as unnecessary overhead that IPv6 could eliminate.

**Q8. What is SLAAC (Stateless Address Autoconfiguration), and how does it benefit IPv6 devices?**
SLAAC allows an IPv6 device to automatically generate and configure its own IP address without requiring a DHCP server, typically by combining a network prefix advertised by a local router with a locally-generated interface identifier (often derived from the device's MAC address). This benefits IPv6 devices by simplifying network configuration — devices can join a network and obtain a working, routable address with minimal or no manual/centralized configuration required.

**Q9. Why is NAT (Network Address Translation) generally unnecessary in IPv6 networks, unlike in IPv4?**
NAT became widely necessary in IPv4 primarily as a workaround for address scarcity — allowing many devices on a private network to share a single, or a small number of, public IPv4 addresses. Since IPv6's address space is so vastly larger, every device can be assigned its own unique, globally-routable public IPv6 address directly, eliminating the fundamental need for NAT's address-sharing function (though NAT-like mechanisms might still occasionally be used in IPv6 for other reasons, like network renumbering flexibility or particular security policies).

**Q10. If a packet needs to cross multiple links with different MTU sizes, how does IPv6 ensure the packet reaches its destination without being dropped along the way?**
The sending host uses Path MTU Discovery — it initially attempts to send a packet, and if any router along the path has a smaller MTU than the packet size, that router drops the packet and sends an ICMPv6 "Packet Too Big" message back to the sender, indicating the maximum size that specific link can support. The sender then reduces its packet size accordingly and resends; this process can repeat multiple times if there are several progressively smaller MTU links along the path, until the sender converges on a packet size that successfully fits through every link on the entire path to the destination.

**Q11. What are the practical implications of IPv6's much larger header simplification compared to IPv4, in terms of router performance?**
IPv6's fixed-length, simplified header format (with fewer fields and no built-in checksum requiring per-hop recalculation) allows routers to process and forward packets more quickly and efficiently, since there's less per-packet computational overhead compared to IPv4's variable-length header with optional fields and mandatory checksum recalculation at every hop. This design choice directly supports higher throughput and better scalability for routers handling IPv6 traffic at a large scale.

**Q12. Why might an organization still need to support both IPv4 and IPv6 simultaneously (dual-stack), rather than fully switching to IPv6?**
Because a significant portion of existing Internet infrastructure, legacy devices, and other networks/services still primarily (or exclusively) use IPv4, full IPv6-only adoption isn't yet universally possible — an organization needs to remain compatible/reachable by both IPv4-only and IPv6-capable clients/services during this extended transition period. Dual-stack configurations (where devices/routers support both protocols simultaneously) are a common practical approach to ensure compatibility across the mixed IPv4/IPv6 landscape of the current Internet.
