# 01: Introduction to Computer Networks

## 1. What is a Computer Network?
- A **Computer Network** is a collection of **interconnected devices** (computers, servers, printers, mobile devices, etc.) that can **communicate and share resources** (data, files, hardware, internet access) with each other.
- Devices are connected via **communication links** (wired or wireless) and follow agreed-upon **protocols** (rules) to exchange data.

### Why Networks?
1. **Resource Sharing** — sharing files, printers, internet connections.
2. **Communication** — email, messaging, video calls.
3. **Reliability** — data can be replicated across multiple machines for backup.
4. **Cost efficiency** — shared hardware/software resources reduce costs.
5. **Scalability** — easy to add new devices/users to the network.

## 2. Types of Networks (by geographical scope)

| Type | Full Form | Coverage | Example |
|---|---|---|---|
| **PAN** | Personal Area Network | A few meters (around one person) | Bluetooth headphones, smartwatch connected to phone |
| **LAN** | Local Area Network | A single building/campus | Office network, home WiFi |
| **MAN** | Metropolitan Area Network | A city | Cable TV network, city-wide WiFi |
| **WAN** | Wide Area Network | Country/Continent/Global | The Internet itself, corporate networks spanning countries |

> **Extra — Additional network scopes sometimes asked:**
> - **CAN (Campus Area Network):** Connects multiple LANs within a limited geographical area, like a university campus (a middle ground between LAN and MAN).
> - **SAN (Storage Area Network):** A specialized high-speed network providing access to consolidated, block-level data storage — used in enterprise data centers.

## 3. Network Topologies
The **topology** describes how devices (nodes) are physically or logically arranged and connected in a network.

### a. Bus Topology
- All devices connect to a **single central cable** (the "bus" / backbone).
- **Pros:** Cheap, easy to install for small networks.
- **Cons:** If the main cable fails, the **entire network goes down**; performance degrades as more devices are added (shared bandwidth).

```
   A       B       C       D       E
   |       |       |       |       |
===+=======+=======+=======+=======+===   <- single shared backbone cable
                                          (terminators at both ends)
```

### b. Star Topology
- All devices connect to a **central device** (hub/switch).
- **Pros:** Easy to install/manage; failure of one device/cable doesn't affect others.
- **Cons:** If the **central device fails**, the entire network goes down (single point of failure).
- **Most common topology in modern LANs.**

```
        A       B
         \     /
          \   /
   E ------[HUB/SWITCH]------ C
          /   \
         /     \
        F       D
```

### c. Ring Topology
- Each device connects to exactly **two other devices**, forming a **circular** data path. Data travels in one direction (or both, in dual-ring setups).
- **Pros:** Equal access for all devices; performs reasonably well under heavy load compared to bus.
- **Cons:** Failure of **one device/link can disrupt the entire ring** (unless a dual-ring/redundant setup is used).

```
          A -------- B
         /            \
        E              C
         \            /
          D ---------
      (data flows around the ring, e.g., A→B→C→D→E→A)
```

### d. Mesh Topology
- Every device is connected to **every other device** (Full Mesh), or connected to several other devices (Partial Mesh).
- **Pros:** Highly **fault-tolerant** — multiple paths mean failure of one link doesn't isolate any device; robust and reliable.
- **Cons:** Very **expensive** and complex to install/maintain (requires `n(n-1)/2` links for `n` devices in a full mesh).

```
        A-----------B
        | \       / |
        |  \     /  |
        |   \   /   |
        |    \ /    |
        |     X     |
        |    / \    |
        |   /   \   |
        |  /     \  |
        | /       \ |
        C-----------D
   (every node directly connected to every other node)
```

### e. Tree (Hierarchical) Topology
- A combination of **star topologies** connected via a bus — hierarchical, parent-child structure.
- **Pros:** Scalable, easy to manage/isolate faults at branch level.
- **Cons:** If the **root/backbone** fails, the whole network (or large parts of it) is affected.

```
                    [Root Switch]
                    /           \
             [Switch B]       [Switch C]
             /       \         /       \
            D         E       F         G
        (leaf devices connect to branch switches,
         branch switches connect up to the root)
```

### f. Hybrid Topology
- A combination of **two or more** different topologies (e.g., star-bus, star-ring), used to leverage the advantages of each in different parts of a large network.

```
   [Star Network 1]                [Star Network 2]
    A   B   C                       F   G   H
     \  |  /                         \  |  /
    [Switch 1] ===backbone bus=== [Switch 2]
   (two independent star topologies, joined via a bus backbone)
```

> **Extra — Topology comparison table:**

| Topology | Fault Tolerance | Cost | Scalability | Common Use |
|---|---|---|---|---|
| Bus | Low (single cable failure = network down) | Low | Poor | Small, legacy networks |
| Star | Medium (central device is SPOF) | Medium | Good | Most modern LANs/offices |
| Ring | Low-Medium | Medium | Medium | Legacy Token Ring networks, some fiber backbones |
| Mesh | Very High | Very High | Poor (cost grows quadratically) | Critical infrastructure, WAN backbones |
| Tree | Medium | Medium | Good | Large organizations with hierarchical structure |

## 4. Network Components (Devices)

| Device | Layer (OSI) | Function |
|---|---|---|
| **Repeater** | Physical (L1) | Regenerates/amplifies a weakening signal to extend cable length/range. |
| **Hub** | Physical (L1) | A multi-port repeater; simply broadcasts incoming data to **all** connected ports (no intelligence, no filtering). |
| **Bridge** | Data Link (L2) | Connects two LAN segments; filters traffic based on **MAC addresses**, reducing unnecessary traffic compared to a hub. |
| **Switch** | Data Link (L2) | A smarter, multi-port bridge; forwards data **only to the specific port** where the destination MAC address resides (using a MAC address table). |
| **Router** | Network (L3) | Connects **different networks** together (e.g., LAN to the Internet); forwards packets based on **IP addresses**, using routing tables. |
| **Gateway** | Any/All layers | Connects networks using **different protocols** entirely (translates between them); acts as an entry/exit point between dissimilar networks. |
| **Modem** | Physical (L1) | **Mo**dulates/**dem**odulates signals — converts digital data to analog (for transmission over telephone lines) and back. |
| **Access Point (AP)** | Data Link (L2) | Allows wireless devices to connect to a wired network via WiFi. |

### Typical Device Placement in a Network

```
 [Internet / WAN]
        |
     [Router]  <-- connects LAN to outside networks (L3, IP-based)
        |
     [Switch]  <-- connects devices within the LAN (L2, MAC-based)
     /   |   \
[PC-A] [PC-B] [Access Point] --(WiFi)--> [Laptop] [Phone]
```

> **Extra — Hub vs Switch vs Router (a VERY common interview question):**

| Aspect | Hub | Switch | Router |
|---|---|---|---|
| OSI Layer | Physical (L1) | Data Link (L2) | Network (L3) |
| Intelligence | None — broadcasts to all ports | Uses MAC address table to forward selectively | Uses routing table, IP addresses to forward |
| Collision Domain | Single (shared) collision domain for all ports | Each port is its own collision domain | Each interface is a separate collision AND broadcast domain |
| Connects | Devices within the same network | Devices within the same network | Different networks (e.g., LAN to WAN) |
| Speed/Efficiency | Slowest (broadcasts everything, causes collisions) | Faster (only sends to relevant port) | Handles inter-network routing decisions/logic |

## 5. Client-Server vs Peer-to-Peer (P2P) Architecture

### Client-Server Model
- **Clients** request services/resources; a dedicated **Server** provides/manages those resources centrally.
- **Pros:** Centralized management, easier security/backup, consistent performance under controlled load.
- **Cons:** Server is a potential **single point of failure**; server can become a bottleneck under heavy load.
- **Examples:** Web browsing (browser = client, web server = server), email.

### Peer-to-Peer (P2P) Model
- All devices (**peers**) are **equal** — each can act as both a client and a server, sharing resources directly with each other.
- **Pros:** No single point of failure (decentralized), scales well as more peers join (more peers = more shared resources).
- **Cons:** Harder to secure/manage centrally; performance/availability of specific data depends on which peers are currently online.
- **Examples:** BitTorrent, some blockchain networks.

> **Extra — Client-Server vs P2P comparison:**

| Aspect | Client-Server | Peer-to-Peer |
|---|---|---|
| Architecture | Centralized | Decentralized |
| Single Point of Failure | Yes (server) | No |
| Management/Security | Easier (centralized control) | Harder (distributed control) |
| Scalability | Limited by server capacity | Scales with number of peers |
| Example | Web servers, email | BitTorrent, Skype (early versions) |

---

## Interview Questions & Answers

**Q1. What is a Computer Network, and what are its main benefits?**
A Computer Network is a collection of interconnected devices that communicate and share resources using agreed-upon protocols. Its main benefits include resource sharing (files, printers, internet), enabling communication, improving reliability through data replication, reducing costs via shared infrastructure, and easy scalability to add new devices/users.

**Q2. What is the difference between LAN, MAN, and WAN?**
LAN (Local Area Network) covers a small area like a single building or campus. MAN (Metropolitan Area Network) covers a city-wide area, connecting multiple LANs. WAN (Wide Area Network) covers a very large geographical area, potentially spanning countries or continents — the Internet itself is the largest example of a WAN.

**Q3. Compare Star, Bus, Ring, and Mesh topologies in terms of fault tolerance.**
Bus topology has low fault tolerance — a single cable failure brings down the entire network. Star topology has medium fault tolerance — individual device failures don't affect others, but the central hub/switch is a single point of failure. Ring topology has low-to-medium fault tolerance — a single broken link can disrupt the whole ring unless redundant/dual-ring setups are used. Mesh topology has the highest fault tolerance, since multiple redundant paths exist between devices, so no single link failure isolates any node.

**Q4. Why is Star topology the most commonly used topology in modern networks?**
Because it offers a good balance of manageability (each device connects independently to a central switch, making it easy to add/remove devices and isolate faults) and reasonable fault tolerance (one device's cable failing doesn't affect the rest of the network) — while being far more affordable and practical than a full Mesh topology, at the cost of a single point of failure at the central device.

**Q5. What is the key difference between a Hub, a Switch, and a Router?**
A Hub operates at the Physical layer and simply broadcasts incoming data to all connected ports with no intelligence. A Switch operates at the Data Link layer and intelligently forwards data only to the specific port where the destination device (identified by MAC address) resides, using a MAC address table. A Router operates at the Network layer and connects entirely different networks together, forwarding packets based on IP addresses using routing tables.

**Q6. Why is a Hub considered inefficient compared to a Switch?**
Because a Hub broadcasts every incoming frame to ALL connected ports, regardless of the intended destination — this creates a single shared collision domain across all ports, increasing the likelihood of data collisions and wasting bandwidth on devices that don't need to receive that particular data. A Switch, by contrast, learns MAC addresses and forwards frames only to the specific port needed, giving each port its own collision domain and much better overall network efficiency.

**Q7. What is the difference between a Bridge and a Router?**
A Bridge operates at the Data Link layer (Layer 2) and connects/filters traffic between two segments of the SAME network based on MAC addresses. A Router operates at the Network layer (Layer 3) and connects entirely DIFFERENT networks together, making forwarding decisions based on IP addresses and routing tables — routers are needed to route traffic across the wider Internet, while bridges just segment traffic within a local network.

**Q8. What is a Gateway, and how is it different from a Router?**
A Gateway is a device (or software) that connects two networks using entirely different protocols or architectures, performing protocol translation between them (it can operate at any layer, depending on what translation is needed). A Router, in contrast, connects networks that use the same underlying protocol suite (typically IP), simply forwarding packets between them based on IP addresses without needing to translate between different protocol stacks.

**Q9. What is the difference between a Client-Server architecture and a Peer-to-Peer architecture?**
In Client-Server architecture, a centralized server provides resources/services to multiple clients that request them — offering centralized management and security but creating a potential single point of failure and bottleneck at the server. In Peer-to-Peer architecture, all devices (peers) are equal, each acting as both client and server, sharing resources directly with each other — this eliminates a single point of failure and scales naturally with more peers, but is harder to manage/secure centrally.

**Q10. Give a real-world example of when you'd choose a Mesh topology despite its high cost.**
Critical infrastructure networks — such as military communication networks, core Internet backbone links between major ISPs, or industrial control systems — where uninterrupted connectivity and maximum fault tolerance are absolutely essential, and the cost of downtime far outweighs the higher installation/maintenance cost of a full or partial mesh topology.

**Q11. What is the difference between a collision domain and a broadcast domain, and how do Hubs, Switches, and Routers differ in this regard?**
A collision domain is a network segment where data packets can "collide" if two devices transmit simultaneously; a broadcast domain is the set of devices that receive a broadcast frame sent by any one device in that domain. A Hub puts all connected devices into a single, shared collision domain (and broadcast domain). A Switch creates a separate collision domain per port, but all ports remain within the same broadcast domain. A Router separates both collision domains AND broadcast domains at each of its interfaces, since it operates at the network layer and doesn't forward broadcast traffic between different networks by default.

**Q12. Why might a device like a Modem still be relevant in an interview discussion about network devices?**
A Modem (modulator-demodulator) is relevant because it performs the essential Physical-layer function of converting digital data from a computer into analog signals suitable for transmission over analog media (like traditional telephone lines or cable systems), and converting incoming analog signals back into digital data — this concept of modulation/demodulation is foundational to understanding how digital networks interface with different physical transmission media, a topic often explored further in Physical Layer / Data Transmission discussions.
