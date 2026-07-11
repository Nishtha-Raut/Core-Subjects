# 14: Transport Layer — TCP vs UDP

## 1. Role of the Transport Layer
- Provides **process-to-process** (application-to-application) delivery, using **port numbers** to identify specific applications/services on a device (as opposed to the Network layer's host-to-host delivery using IP addresses).
- Two primary protocols: **TCP** (reliable, connection-oriented) and **UDP** (unreliable, connectionless).

## 2. TCP (Transmission Control Protocol)

### Key Characteristics
- **Connection-oriented:** A connection must be **established** (via handshake) before data transfer begins.
- **Reliable:** Guarantees delivery via acknowledgments, retransmission of lost segments, and sequencing.
- **Ordered delivery:** Data arrives at the application in the **same order** it was sent, even if individual segments arrive out of order over the network (TCP reorders them).
- **Flow control:** Uses a sliding window mechanism to prevent overwhelming the receiver.
- **Congestion control:** Adapts sending rate based on network conditions (see LEC-15).
- **Byte-stream oriented:** Treats data as a continuous stream of bytes, not as discrete messages.

### TCP Header (Key Fields)

| Field | Purpose |
|---|---|
| Source Port / Destination Port | Identify sending/receiving application |
| Sequence Number | Tracks the byte-position of this segment within the overall stream |
| Acknowledgment Number | Indicates the next expected byte from the other side |
| Flags (SYN, ACK, FIN, RST, PSH, URG) | Control bits for connection management |
| Window Size | Advertises how much data the receiver is currently willing to accept (flow control) |
| Checksum | Error detection for the segment |

## 3. TCP Three-Way Handshake (Connection Establishment)

```
Client                                Server
   |                                     |
   |------------- SYN ------------------>|   (Client: "I want to connect, my seq=X")
   |                                     |
   |<---------- SYN-ACK -----------------|   (Server: "OK, my seq=Y, ACKing your X+1")
   |                                     |
   |------------- ACK ------------------>|   (Client: "ACKing your Y+1, connection established")
   |                                     |
   [Connection Established — data transfer begins]
```

1. **SYN:** Client sends a segment with the **SYN** flag set, and an initial sequence number (ISN).
2. **SYN-ACK:** Server responds with **both SYN and ACK** flags set — acknowledging the client's SYN, and sending its own ISN.
3. **ACK:** Client sends a final **ACK**, acknowledging the server's SYN — connection is now **established**, and data transfer can begin.

> **Extra — Why 3 steps, not 2?** A simple 2-way handshake wouldn't allow BOTH sides to confirm that the other side is ready and their sequence numbers are agreed upon — TCP connections are **full-duplex** (both sides send and receive independently), so each side needs to both send AND acknowledge its own initial sequence number, requiring this 3-step exchange to fully synchronize both directions.

## 4. TCP Connection Termination (Four-Way Handshake)

```
Client                                Server
   |                                     |
   |------------- FIN ------------------>|   (Client: "I'm done sending")
   |                                     |
   |<------------ ACK -------------------|   (Server: "OK, acknowledged" — but server may still be sending)
   |                                     |
   |<------------ FIN -------------------|   (Server: "I'm done sending too")
   |                                     |
   |------------- ACK ------------------>|   (Client: "OK, acknowledged" — connection fully closed)
   |                                     |
   [Connection Terminated]
```

- Requires **4 steps** (not 3) because TCP connections are full-duplex — each direction must be **independently closed**. The server's ACK and FIN are often sent as separate steps (rather than combined like SYN-ACK), since the server might still have data left to send even after the client says it's done.

## 5. UDP (User Datagram Protocol)

### Key Characteristics
- **Connectionless:** No handshake — data is simply sent, with **no guarantee** it arrives.
- **Unreliable:** No acknowledgments, no retransmission — if a datagram is lost, it's simply gone (unless the application layer handles this itself).
- **No ordering guarantee:** Datagrams can arrive out of order, and UDP does nothing to reorder them.
- **No flow/congestion control:** Sends as fast as the application wants, regardless of network conditions.
- **Message-oriented:** Each `send()` call corresponds to one discrete datagram, preserving message boundaries (unlike TCP's continuous byte stream).
- **Much lower overhead** — smaller header (just 8 bytes vs TCP's minimum 20 bytes), no handshake delay.

### UDP Header (Much Simpler)

| Field | Purpose |
|---|---|
| Source Port / Destination Port | Identify sending/receiving application |
| Length | Length of the UDP datagram (header + data) |
| Checksum | Error detection (optional in IPv4, mandatory in IPv6) |

## 6. TCP vs UDP — Full Comparison Table

| Aspect | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (3-way handshake) | Connectionless |
| Reliability | Reliable (ACKs, retransmission) | Unreliable (best-effort, no guarantees) |
| Ordering | Guaranteed in-order delivery | No ordering guarantee |
| Speed | Slower (overhead of handshake, ACKs, control) | Faster (minimal overhead) |
| Header size | 20 bytes (minimum) | 8 bytes |
| Flow Control | Yes (sliding window) | No |
| Congestion Control | Yes | No |
| Data boundaries | Byte-stream (no message boundaries preserved) | Message/datagram-oriented (boundaries preserved) |
| Use cases | Web browsing (HTTP), Email (SMTP), File transfer (FTP) — anywhere correctness matters | Video/audio streaming, VoIP, DNS, online gaming — anywhere speed matters more than perfect reliability |

> **Extra — Why would anyone choose UDP's unreliability on purpose?** For real-time applications like video calls or live gaming, a **late** packet (waiting for TCP's retransmission of a lost piece) is often WORSE than a **lost** packet (which the application can just skip past, e.g., a tiny video glitch) — UDP's speed and lack of retransmission delay make it the better choice specifically BECAUSE it doesn't try to guarantee delivery, avoiding the head-of-line blocking and delay that TCP's reliability mechanisms would introduce.

## 7. TCP States (simplified overview — connection lifecycle)

```
CLOSED -> (send SYN) -> SYN_SENT -> (recv SYN-ACK, send ACK) -> ESTABLISHED
                                                                      |
                                                            (data transfer happens)
                                                                      |
                                                          (send/recv FIN) -> ... -> CLOSED
```

- Common states you may be asked about: `LISTEN` (server waiting for connections), `SYN_SENT`, `SYN_RECEIVED`, `ESTABLISHED`, `FIN_WAIT`, `TIME_WAIT` (waits briefly after closing to handle any delayed/duplicate packets), `CLOSED`.

---

## Interview Questions & Answers

**Q1. What is the fundamental difference between TCP and UDP?**
TCP is a connection-oriented, reliable protocol that guarantees ordered delivery of data through acknowledgments, retransmission, flow control, and congestion control, at the cost of higher overhead and latency. UDP is a connectionless, unreliable protocol that simply sends data with no guarantees of delivery, ordering, or congestion awareness, but with much lower overhead and latency, making it faster for applications that can tolerate some data loss.

**Q2. Walk through the TCP Three-Way Handshake process.**
The client sends a SYN segment with its initial sequence number, indicating it wants to establish a connection. The server responds with a SYN-ACK segment, acknowledging the client's SYN and including its own initial sequence number. The client then sends a final ACK segment, acknowledging the server's SYN — at this point, the connection is fully established and both sides can begin exchanging data.

**Q3. Why does TCP use a Three-Way Handshake instead of just a simple two-step exchange?**
Because TCP connections are full-duplex — both sides need to independently send data and have that data acknowledged. A simple two-step handshake would only allow one side to confirm readiness; the three-way handshake ensures BOTH the client and server can synchronize and confirm their respective initial sequence numbers, fully establishing reliable communication in both directions before actual data transfer begins.

**Q4. Why does TCP connection termination require four steps, while establishment only requires three?**
Because TCP is full-duplex, each direction of the connection must be closed independently. While SYN and ACK can be combined into a single SYN-ACK segment during establishment (since both are essentially requesting/acknowledging in the same step), during termination, when one side sends a FIN, the OTHER side might still have more data left to send even after acknowledging that FIN — so its own FIN is typically sent as a separate, later step once it's truly done sending everything, resulting in four distinct steps overall (FIN, ACK, FIN, ACK) rather than three.

**Q5. What does it mean that UDP is "connectionless"?**
It means UDP doesn't perform any handshake or setup process before sending data — a sender simply transmits datagrams directly to the destination's IP/port without first establishing or verifying that the receiver is ready or even reachable, and without maintaining any ongoing "connection state" between sender and receiver.

**Q6. Why might a real-time video call application choose UDP over TCP, despite UDP's lack of reliability?**
Because in real-time communication, a packet that arrives LATE (due to TCP waiting for retransmission of an earlier lost packet, which also blocks delivery of all subsequent, already-received data until the missing piece arrives — "head-of-line blocking") is often more disruptive to the user experience than a packet that's simply LOST and skipped entirely (causing a brief, minor glitch). UDP's lack of retransmission and ordering guarantees means data can be delivered to the application immediately as it arrives, without waiting, making it much better suited for latency-sensitive real-time applications where speed matters more than perfect completeness.

**Q7. What is the difference between TCP's "byte-stream" orientation and UDP's "message" orientation?**
TCP treats all data as one continuous, undifferentiated stream of bytes — the application doesn't see distinct "boundaries" between individual send() calls; TCP might combine or split data into segments however it sees fit for efficient transmission. UDP, by contrast, preserves message boundaries — each individual send() call corresponds to exactly one discrete datagram, and the receiving application receives that exact same data in the exact same discrete chunks/sizes as it was originally sent.

**Q8. Why is the UDP header (8 bytes) so much smaller than the TCP header (minimum 20 bytes)?**
Because UDP doesn't need to track and manage all the additional information TCP requires for its reliability, ordering, flow control, and connection management features — things like sequence numbers, acknowledgment numbers, window size, and various control flags. UDP's header only needs the bare minimum: source/destination ports, a length field, and an optional checksum, since it doesn't provide any of TCP's more sophisticated (and overhead-requiring) guarantees.

**Q9. What is the purpose of the TCP Window Size field, and how does it relate to Flow Control?**
The Window Size field, sent by the receiver in each TCP segment, advertises how much additional data (in bytes) the receiver is currently willing and able to accept/buffer, without becoming overwhelmed. This directly implements Flow Control (as discussed in the Sliding Window protocol) — the sender uses this advertised window size to determine how much data it can safely transmit before needing to wait for further acknowledgment/window updates from the receiver.

**Q10. Give three real-world examples each of applications that typically use TCP versus UDP, and briefly justify why.**
TCP examples: Web browsing (HTTP/HTTPS) — needs complete, correctly-ordered page content; Email (SMTP) — needs guaranteed, complete delivery of messages; File transfer (FTP) — needs every byte of a file to arrive correctly and completely. UDP examples: Video/voice calls (VoIP) — needs low latency, can tolerate occasional dropped frames/glitches; Online multiplayer gaming — needs fast, real-time updates, where slightly stale/lost position data is less disruptive than lag from waiting on retransmission; DNS queries — typically small, simple request/response exchanges where the minor overhead of TCP's full reliability isn't usually necessary (though DNS can fall back to TCP for larger responses).

**Q11. What is the TIME_WAIT state in TCP, and why does it exist?**
TIME_WAIT is a state a connection enters briefly after the final ACK of the four-way termination handshake is sent, before the connection resources are fully released. It exists to handle the possibility that this final ACK might be lost, in which case the other side might resend its FIN — the TIME_WAIT state ensures the connection can still properly respond to and acknowledge a retransmitted FIN, and also helps ensure any old, delayed duplicate packets from this now-closed connection don't get mistakenly associated with a new connection that might reuse the same port numbers shortly afterward.

**Q12. If a UDP application needs some level of reliability (e.g., a custom game protocol wants to know if certain critical messages were received), how might it achieve this, since UDP itself provides none?**
The application itself would need to implement its own reliability mechanisms on top of UDP — for example, adding its own sequence numbers and application-level acknowledgments for specific important messages, along with custom retransmission logic if an expected acknowledgment isn't received within a timeout. This gives the application fine-grained CONTROL over exactly which messages need reliability (critical game state changes) versus which can be safely allowed to be lost (frequent, less critical position updates), rather than TCP's all-or-nothing approach to reliability for the entire data stream.
