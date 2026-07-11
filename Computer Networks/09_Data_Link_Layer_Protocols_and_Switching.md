# 09: Data Link Layer Protocols, MAC Addressing, Switching, VLAN & STP

## 1. MAC Addressing
- A **MAC (Media Access Control) address** is a **physical, hardware-based address** burned into a device's Network Interface Card (NIC) by the manufacturer.
- **48 bits (6 bytes)** long, typically written as **12 hexadecimal digits** in the form `XX:XX:XX:XX:XX:XX`.
- **Globally unique** (in theory) — the first 24 bits (3 bytes) identify the **manufacturer (OUI — Organizationally Unique Identifier)**, and the last 24 bits are a unique serial number assigned by that manufacturer.

```
MAC Address: 00:1A:2B:3C:4D:5E
             |________| |________|
              OUI (Mfr)   Device-specific
              (24 bits)    (24 bits)
```

> **Extra — MAC address vs IP address (extremely common interview question):**

| Aspect | MAC Address | IP Address |
|---|---|---|
| Assigned by | Manufacturer (hardcoded on NIC) | Network administrator / DHCP (logical, configurable) |
| Layer | Data Link (Layer 2) | Network (Layer 3) |
| Scope | Unique within a local network (physical identity) | Identifies a device's location on a network (can change) |
| Permanence | Fixed (though can be "spoofed"/changed in software) | Can change (e.g., DHCP lease renewal, moving networks) |
| Format | 48-bit, hexadecimal (e.g., 00:1A:2B:3C:4D:5E) | 32-bit (IPv4, e.g., 192.168.1.1) or 128-bit (IPv6) |
| Purpose | Identifies a physical device on the LOCAL network segment | Identifies a device's logical location, enabling routing ACROSS networks |

## 2. Data Link Layer Protocols

### HDLC (High-Level Data Link Control)
- A **bit-oriented** protocol (uses bit stuffing) for point-to-point and multipoint links.
- Supports three frame types: **Information frames** (carry actual data), **Supervisory frames** (flow/error control), **Unnumbered frames** (link management, e.g., setup/teardown).

### PPP (Point-to-Point Protocol)
- Commonly used to establish a direct connection between two nodes, e.g., over dial-up, DSL, or leased lines.
- Provides **authentication** (via PAP or CHAP protocols), **error detection**, and can carry **multiple network layer protocols** simultaneously.

## 3. Switching Recap (from Data Link/Network Layer perspective)

### Circuit Switching
- A **dedicated physical path/circuit** is established between sender and receiver for the **entire duration** of the communication, before data transfer begins.
- **Example:** Traditional telephone networks (PSTN).
- **Pros:** Guaranteed, consistent bandwidth/quality once connected. **Cons:** Inefficient use of resources if the connection sits idle (the circuit is still reserved/unusable by others).

### Packet Switching
- Data is broken into **packets**, each independently routed through the network (potentially via different paths), and reassembled at the destination.
- **Example:** The modern Internet (IP-based).
- **Pros:** More efficient bandwidth utilization (shared among many flows). **Cons:** Variable delay (jitter), packets can arrive out of order, requiring reassembly.

### Message Switching
- An entire message is sent as one unit, stored temporarily at each intermediate node ("**store-and-forward**"), then forwarded to the next node when a link becomes available.
- **Example:** Historic telegraph/email-relay style systems.
- **Mostly obsolete** in modern networking, replaced by packet switching.

> **Extra — Circuit vs Packet vs Message Switching comparison:**

| Aspect | Circuit Switching | Packet Switching | Message Switching |
|---|---|---|---|
| Path setup | Dedicated path reserved upfront | No dedicated path; routed dynamically per packet | Entire message stored/forwarded hop-by-hop |
| Resource efficiency | Poor (reserved even when idle) | Good (shared dynamically) | Moderate |
| Delay | Consistent once connected (low, predictable) | Variable (jitter possible) | High (store-and-forward at every hop) |
| Modern relevance | Legacy telephone networks | The Internet (dominant model today) | Largely obsolete |

## 4. Switch Learning & Forwarding (how a Switch actually works)
1. A switch maintains a **MAC address table** (also called a CAM table), mapping MAC addresses to the physical port they're reachable through.
2. **Learning:** When a frame arrives on a port, the switch records the **source MAC address** and the **incoming port** in its table.
3. **Forwarding:** For the destination MAC address:
   - If it's **known** (in the table), the switch forwards the frame **only to that specific port**.
   - If it's **unknown** (not yet learned), the switch **floods** the frame to **all ports** (except the one it came in on) — similar to a hub's behavior, but only as a fallback.
4. **Aging:** Entries in the MAC table expire after a period of inactivity, keeping the table current as devices move/change.

```
       [Switch]
      Port1 Port2 Port3 Port4
        |     |     |     |
        A     B     C     D

MAC Table (learned over time):
Port1 -> MAC of A
Port2 -> MAC of B
Port3 -> MAC of C
Port4 -> MAC of D

If A sends a frame to C: switch looks up C's MAC -> forwards ONLY out Port3
(B and D never see this traffic — unlike a Hub, which would flood to all ports)
```

## 5. VLAN (Virtual LAN)
- A **VLAN** logically segments a single physical switch/network into **multiple separate broadcast domains**, even though devices might be physically connected to the same switch.
- Devices in **different VLANs cannot communicate directly** without going through a **Router** (or a Layer 3 switch) — just like devices on physically separate LANs.
- **Benefits:**
  1. **Improved security** — isolates sensitive traffic (e.g., a Finance VLAN separate from a Guest WiFi VLAN).
  2. **Reduced broadcast traffic** — broadcasts stay contained within their own VLAN, improving overall network performance.
  3. **Flexible logical grouping** — devices can be grouped by department/function regardless of physical location/cabling.
- **Trunk Ports:** Carry traffic for **multiple VLANs** over a single physical link (e.g., between two switches), using **VLAN tagging (802.1Q)** to mark which VLAN each frame belongs to.

```
        [Switch]
   VLAN 10 (Sales)      VLAN 20 (Engineering)
    Port1  Port2          Port3  Port4
      |     |                |     |
      A     B                C     D

A and B can talk to each other directly (same VLAN 10).
C and D can talk to each other directly (same VLAN 20).
A CANNOT talk to C directly — needs a Router/L3 switch to cross VLANs.
```

## 6. Spanning Tree Protocol (STP)
- **Problem it solves:** In networks with **redundant physical links** between switches (added intentionally for fault tolerance), those redundant paths can create **Layer 2 loops** — causing **broadcast storms** (a broadcast frame circulates endlessly, multiplying and consuming all available bandwidth) since, unlike IP packets, Ethernet frames have **no TTL (Time To Live)** to naturally expire.
- **STP (IEEE 802.1D)** solves this by **logically disabling** redundant links, creating a **loop-free, tree-shaped logical topology**, while still keeping the redundant physical links available as **backups** — if the active path fails, STP automatically re-enables a previously blocked link to restore connectivity.

### How STP Works (high-level)
1. Elects a **Root Bridge** (the switch with the lowest Bridge ID) as the "top" of the logical tree.
2. Each other switch determines its **shortest path (least cost)** to the Root Bridge, and designates a **Root Port** accordingly.
3. Redundant links that would create a loop are put into a **Blocking state** (not forwarding data, but still listening for topology changes).
4. If an active link fails, STP **recalculates** the topology and can transition a previously blocked port to active, restoring connectivity (though this recalculation historically took ~30-50 seconds with classic STP; newer standards like **RSTP — Rapid STP** converge much faster).

```
        [Switch A] (Root Bridge)
         /              \
   [Switch B]        [Switch C]
         \              /
          \            /
           [Redundant Link]  <- STP puts this in BLOCKING state
                              to prevent a loop, but keeps it as
                              a standby backup path
```

---

## Interview Questions & Answers

**Q1. What is a MAC Address, and how is it different from an IP Address?**
A MAC Address is a 48-bit physical hardware address, hardcoded onto a device's network interface card by its manufacturer, used to identify a device on its LOCAL network segment (Data Link layer). An IP Address is a logical, configurable address (assigned by a network administrator or DHCP) used to identify a device's location and enable routing ACROSS different networks (Network layer) — MAC addresses are generally fixed/permanent, while IP addresses can change depending on network configuration.

**Q2. How does a Switch "learn" which devices are connected to which ports?**
A switch inspects the SOURCE MAC address of every incoming frame and records that MAC address alongside the port it arrived on, in its internal MAC address (CAM) table. Over time, as different devices send traffic, the switch builds up a complete mapping of MAC addresses to ports, allowing it to make intelligent, targeted forwarding decisions instead of blindly broadcasting like a hub.

**Q3. What happens when a Switch receives a frame destined for a MAC address it hasn't learned yet?**
The switch "floods" the frame — forwarding it out to ALL ports except the one it arrived on (similar to how a hub would behave), since it doesn't yet know which specific port leads to that destination MAC address. Once the actual destination device responds, the switch learns its MAC address/port mapping and can forward future traffic to it directly, without needing to flood again.

**Q4. What is a VLAN, and why would an organization use one?**
A VLAN (Virtual LAN) logically segments a single physical network/switch infrastructure into multiple separate broadcast domains, even if the devices are physically connected to the same switch hardware. Organizations use VLANs to improve security (isolating sensitive traffic, like Finance, from other traffic, like Guest WiFi), reduce unnecessary broadcast traffic (broadcasts stay contained within their own VLAN), and flexibly group devices logically by department/function rather than by physical cabling location.

**Q5. Can devices on different VLANs communicate directly with each other? If not, how do they communicate?**
No, devices on different VLANs cannot communicate directly with each other, since they belong to separate broadcast domains — this is functionally similar to them being on entirely separate physical LANs. For inter-VLAN communication to happen, traffic must pass through a Router (or a Layer 3-capable switch), which can route between the different VLAN subnets, just as it would route between any two distinct IP networks.

**Q6. What problem does the Spanning Tree Protocol (STP) solve?**
STP solves the problem of Layer 2 network loops that can occur when redundant physical links exist between switches (often added intentionally for fault tolerance). Without STP, these loops would cause broadcast storms — since Ethernet frames have no TTL (unlike IP packets), a broadcast frame could circulate endlessly around the loop, rapidly multiplying and consuming all available network bandwidth, effectively crashing the network.

**Q7. How does STP prevent loops while still allowing redundant links to exist for fault tolerance?**
STP elects a Root Bridge and calculates the shortest path from every other switch to that Root Bridge, then logically puts any redundant links that would create a loop into a "Blocking" state — these links don't forward regular data traffic, but continue to listen for topology changes. If an active link in the network fails, STP recalculates the topology and can transition a previously blocked link into an active forwarding state, restoring connectivity using that redundant path as a backup.

**Q8. What is the difference between Circuit Switching and Packet Switching?**
Circuit Switching establishes a dedicated physical path/circuit between sender and receiver for the entire duration of communication before any data is transferred (like a traditional telephone call) — this guarantees consistent bandwidth but is inefficient if the connection sits idle. Packet Switching breaks data into independently-routed packets that share network resources dynamically with other traffic (like the modern Internet) — this is more efficient in resource utilization, but introduces variable delay and potential out-of-order packet arrival.

**Q9. Why is Message Switching considered largely obsolete in modern networks?**
Message Switching requires storing an ENTIRE message at each intermediate node before forwarding it to the next hop ("store-and-forward" for the complete message), which introduces significant delay, especially for large messages, and requires substantial storage capacity at each intermediate node. Modern Packet Switching improves on this by breaking messages into smaller packets that can be forwarded hop-by-hop with much lower per-hop delay and more efficient use of network resources, making it the dominant approach in virtually all modern networks, including the Internet.

**Q10. What is a Trunk Port in the context of VLANs, and why is it needed?**
A Trunk Port is a special type of switch port configured to carry traffic for MULTIPLE VLANs simultaneously over a single physical link, typically used to connect two switches together. It's needed because, without trunking, you'd need a separate physical cable/link for each individual VLAN between switches — trunk ports use VLAN tagging (via the 802.1Q standard) to mark each frame with its VLAN membership, allowing a single physical link to efficiently carry traffic from many different VLANs while keeping them logically separated.

**Q11. Why does classic STP take a relatively long time (~30-50 seconds) to converge after a topology change, and what improvement addresses this?**
Classic STP's convergence process involves several sequential states (Blocking, Listening, Learning, Forwarding) that each switch port must transition through with built-in timers to ensure network stability and avoid temporary loops during the transition — this cautious, staged approach is what causes the relatively slow convergence time. RSTP (Rapid Spanning Tree Protocol, IEEE 802.1w) was developed specifically to address this limitation, using a more efficient handshake mechanism between switches that allows for much faster convergence (often just a few seconds) after a topology change, while still preventing loops.

**Q12. In a company network, why might you want to place VoIP phones on a separate VLAN from regular data traffic (like employee computers)?**
Placing VoIP traffic on its own dedicated VLAN allows network administrators to apply specific Quality of Service (QoS) prioritization to voice traffic (which is highly sensitive to latency/jitter for call quality), separate from potentially bandwidth-heavy or bursty regular data traffic from computers. It also improves security by isolating the phone system's traffic and any associated management/signaling protocols from the general data network, reducing the attack surface and potential for interference between the two types of traffic.
