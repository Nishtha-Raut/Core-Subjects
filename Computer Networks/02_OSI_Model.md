# 02: OSI Model

## 1. What is the OSI Model?
- **OSI** = **Open Systems Interconnection** Model.
- A **conceptual, 7-layer framework** developed by ISO (International Organization for Standardization) that standardizes how different networking systems communicate.
- Each layer has a **specific responsibility**, and communicates only with the layer directly **above** and **below** it.
- It's a **reference model** — real-world protocol stacks (like TCP/IP) don't map perfectly 1:1 onto it, but it's invaluable for **understanding, teaching, and troubleshooting** network communication conceptually.

## 2. The 7 Layers (Top to Bottom)

```
 ┌─────────────────────────────────────┐
 │ 7. Application Layer                 │  <- HTTP, FTP, SMTP, DNS
 ├─────────────────────────────────────┤
 │ 6. Presentation Layer                │  <- Encryption, Compression, Encoding
 ├─────────────────────────────────────┤
 │ 5. Session Layer                     │  <- Session establishment/mgmt
 ├─────────────────────────────────────┤
 │ 4. Transport Layer                   │  <- TCP, UDP (Segments)
 ├─────────────────────────────────────┤
 │ 3. Network Layer                     │  <- IP, Routers (Packets)
 ├─────────────────────────────────────┤
 │ 2. Data Link Layer                   │  <- Switches, MAC (Frames)
 ├─────────────────────────────────────┤
 │ 1. Physical Layer                    │  <- Cables, Bits, Signals
 └─────────────────────────────────────┘
```

> **Mnemonic (top to bottom):** "**A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing" → Application, Presentation, Session, Transport, Network, Data Link, Physical.
> **Mnemonic (bottom to top):** "**P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way" → Physical, Data Link, Network, Transport, Session, Presentation, Application.

## 3. Layer-by-Layer Breakdown

### Layer 1 — Physical Layer
- Deals with the **actual physical transmission of raw bits** (0s and 1s) over a physical medium (cable, radio waves).
- Concerns: voltage levels, cable types, connectors, data rates, bit synchronization, topology.
- **PDU (Protocol Data Unit): Bits**
- **Devices:** Hubs, Repeaters, Cables.

### Layer 2 — Data Link Layer
- Responsible for **node-to-node (hop-to-hop) delivery** of data within the same network, using **physical/MAC addresses**.
- Key functions: **Framing**, **Error detection** (not correction, typically), **Flow control**, **Media Access Control** (deciding who can transmit when, on a shared medium).
- Divided into two sub-layers:
  - **LLC (Logical Link Control):** Handles flow control, error checking.
  - **MAC (Media Access Control):** Handles addressing (MAC addresses) and channel access control.
- **PDU: Frames**
- **Devices:** Switches, Bridges.
- **Protocols:** Ethernet, PPP, HDLC.

### Layer 3 — Network Layer
- Responsible for **logical addressing** and **routing** — moving packets from the **source to destination across multiple networks** (end-to-end delivery).
- Key functions: **Logical addressing (IP)**, **Routing** (path determination), **Fragmentation** (splitting large packets to fit smaller MTU sizes).
- **PDU: Packets**
- **Devices:** Routers.
- **Protocols:** IP (IPv4/IPv6), ICMP, ARP (sometimes considered here or at L2/2.5), OSPF, BGP.

### Layer 4 — Transport Layer
- Responsible for **end-to-end (process-to-process) communication**, using **port numbers** to identify specific applications.
- Key functions: **Segmentation & reassembly**, **Flow control**, **Error control**, **Connection control** (connection-oriented vs connectionless).
- **PDU: Segments** (TCP) / **Datagrams** (UDP)
- **Protocols:** TCP (reliable, connection-oriented), UDP (unreliable, connectionless).

### Layer 5 — Session Layer
- Responsible for **establishing, managing, and terminating sessions** (dialogues) between two communicating applications.
- Key functions: **Session establishment/maintenance/termination**, **Synchronization** (checkpoints in a data stream, so a large transfer can resume from a checkpoint instead of restarting entirely), **Dialogue control** (who can transmit, and when — half-duplex vs full-duplex).
- **Protocols:** NetBIOS, RPC (Remote Procedure Call), PPTP.

### Layer 6 — Presentation Layer
- Also called the **"Translation Layer."**
- Responsible for **data representation** — ensuring data sent by the application layer of one system can be read by the application layer of another.
- Key functions: **Translation** (character encoding, e.g., ASCII to Unicode), **Encryption/Decryption**, **Compression/Decompression**.
- **Formats:** JPEG, MPEG, SSL/TLS (encryption often associated here conceptually, though implemented across layers in practice).

### Layer 7 — Application Layer
- The layer **closest to the end user** — provides network services **directly to user applications**.
- **Not** the application itself, but the interface/protocols that applications use to access network services.
- **Protocols:** HTTP/HTTPS, FTP, SMTP, DNS, DHCP, Telnet, SSH.

> **Extra — Quick summary table:**

| Layer | # | PDU | Key Concern | Example Protocols | Example Devices |
|---|---|---|---|---|---|
| Application | 7 | Data | User-facing services | HTTP, FTP, SMTP, DNS | — |
| Presentation | 6 | Data | Translation, encryption, compression | SSL/TLS, JPEG | — |
| Session | 5 | Data | Session mgmt, dialogue control | NetBIOS, RPC | — |
| Transport | 4 | Segments/Datagrams | Process-to-process delivery, reliability | TCP, UDP | — |
| Network | 3 | Packets | Logical addressing, routing | IP, ICMP, OSPF | Router |
| Data Link | 2 | Frames | Node-to-node delivery, MAC addressing | Ethernet, PPP | Switch, Bridge |
| Physical | 1 | Bits | Raw bit transmission | — | Hub, Repeater, Cable |

## 4. Encapsulation & Decapsulation
- As data moves **down** the OSI stack (sender side), each layer **adds its own header** (and sometimes trailer) to the data received from the layer above — this is **Encapsulation**.
- As data moves **up** the OSI stack (receiver side), each layer **strips off** its corresponding header/trailer — this is **Decapsulation**.

```
SENDER SIDE (Encapsulation — data flows DOWN the stack):

  Application  :         [ DATA ]
  Transport    :   [TCP Hdr | DATA ]                    <- now called a Segment
  Network      : [IP Hdr | TCP Hdr | DATA ]              <- now called a Packet
  Data Link    : [MAC Hdr | IP Hdr | TCP Hdr | DATA | MAC Trailer]  <- now a Frame
  Physical     : 1010110100101...                        <- now raw Bits


RECEIVER SIDE (Decapsulation — data flows UP the stack):

  Physical     : 1010110100101...
  Data Link    : [MAC Hdr | IP Hdr | TCP Hdr | DATA | MAC Trailer]  -> strip MAC header/trailer
  Network      : [IP Hdr | TCP Hdr | DATA ]              -> strip IP header
  Transport    : [TCP Hdr | DATA ]                        -> strip TCP header
  Application  :         [ DATA ]                         -> deliver to application
```

> **Extra — Why encapsulation matters conceptually:** Each layer only needs to understand its OWN header — it doesn't need to know or care about the contents/headers added by other layers. This is what allows the OSI model's **modularity**: you could swap out the Data Link layer's technology (e.g., Ethernet to WiFi) without the Network or Transport layers needing to change at all, since each layer's header is self-contained and independently interpretable.

---

## Interview Questions & Answers

**Q1. What is the OSI Model, and why was it created?**
The OSI (Open Systems Interconnection) Model is a 7-layer conceptual framework, developed by ISO, that standardizes how different networking systems and protocols communicate with each other. It was created to provide a common reference for understanding, designing, and troubleshooting network communication, and to allow different vendors' systems to interoperate by adhering to standardized layer responsibilities.

**Q2. Name the 7 layers of the OSI model, from top to bottom, and briefly state each layer's primary function.**
Application (user-facing network services like HTTP, FTP), Presentation (data translation, encryption, compression), Session (establishing/managing communication sessions), Transport (end-to-end, process-to-process delivery, e.g., TCP/UDP), Network (logical addressing and routing, e.g., IP), Data Link (node-to-node delivery using MAC addresses, framing), Physical (raw bit transmission over physical media).

**Q3. What is the PDU (Protocol Data Unit) at each layer of the OSI model?**
Physical layer: Bits. Data Link layer: Frames. Network layer: Packets. Transport layer: Segments (for TCP) or Datagrams (for UDP). Layers 5-7 (Session, Presentation, Application) are generally just referred to as Data.

**Q4. What is Encapsulation in the context of the OSI model?**
Encapsulation is the process by which each layer, as data moves down the OSI stack on the sending side, adds its own header (and sometimes trailer) to the data passed down from the layer above it — wrapping the original data with progressively more layer-specific metadata, until it's finally transmitted as raw bits at the Physical layer.

**Q5. What is the difference between the Data Link layer and the Network layer?**
The Data Link layer handles node-to-node (hop-to-hop) delivery of data WITHIN the same local network, using physical/MAC addresses, and deals with framing and media access control. The Network layer handles END-TO-END delivery of data ACROSS multiple different networks, using logical/IP addresses, and is responsible for routing packets from source to destination through potentially many intermediate networks.

**Q6. What is the difference between the Session layer and the Transport layer?**
The Transport layer is responsible for reliable (or unreliable) process-to-process data delivery between two hosts, using port numbers to identify specific applications, and handles segmentation, flow control, and error control. The Session layer sits above this and is responsible for establishing, maintaining, and terminating a logical "session" (an ongoing dialogue) between two communicating applications — including synchronization checkpoints and dialogue control (who can send when).

**Q7. What is the role of the Presentation layer, and why is it sometimes called the "Translation Layer"?**
The Presentation layer ensures that data sent by one system's application layer can be properly understood by another system's application layer, even if they use different internal data representations. It's called the "Translation Layer" because it handles tasks like character encoding conversion (e.g., ASCII to Unicode), data compression/decompression, and encryption/decryption — essentially "translating" data into a common, mutually understandable format.

**Q8. Which OSI layer do Routers operate at, and why?**
Routers operate at the Network layer (Layer 3), because their core function is to make forwarding decisions based on logical IP addresses and routing tables, in order to move packets between different networks — this is precisely the responsibility defined for the Network layer in the OSI model.

**Q9. Which OSI layer do Switches operate at, and why?**
Switches operate at the Data Link layer (Layer 2), because they make forwarding decisions based on physical MAC addresses within a single local network, learning which devices (MAC addresses) are reachable via which physical port — this aligns with the Data Link layer's responsibility for node-to-node delivery within the same network.

**Q10. Does real-world networking strictly follow the 7-layer OSI model? Explain.**
Not exactly — the OSI model is primarily a conceptual/teaching reference model. Real-world networking predominantly uses the TCP/IP model, which condenses the OSI model's 7 layers into 4 (or sometimes described as 5) layers, combining Session/Presentation/Application into a single Application layer. However, OSI remains extremely valuable for understanding and discussing networking concepts in a structured, layer-by-layer way, and terminology from OSI (like "Layer 2 switch" or "Layer 3 routing") is still commonly used in the industry.

**Q11. Why is the modularity of the OSI model considered a key advantage?**
Because each layer only needs to understand and process its own corresponding header/data — it doesn't need any knowledge of how other layers work internally. This means you can change or upgrade the technology used at one layer (e.g., switching from Ethernet cabling to WiFi at the Data Link/Physical layers) without needing to redesign or modify the layers above it (like the Network or Transport layers), since each layer's interface with adjacent layers remains consistent.

**Q12. At which OSI layer does data compression typically occur, and why is this significant for interview discussions?**
Data compression is conceptually associated with the Presentation layer (Layer 6), since this layer handles data representation and translation. However, it's worth noting for interviews that in real-world protocol implementations, compression can actually happen at various layers in practice (e.g., HTTP compression happens at the Application layer in real TCP/IP stacks) — highlighting again that the strict OSI layer boundaries are more of a conceptual teaching tool than something every real protocol implementation rigidly follows.
