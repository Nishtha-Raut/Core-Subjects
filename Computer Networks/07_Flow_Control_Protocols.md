# 07: Flow Control Protocols

## 1. What is Flow Control?
- **Flow Control** ensures that a **fast sender doesn't overwhelm a slow receiver** with data faster than it can process/buffer it.
- Without flow control, the receiver's buffer could **overflow**, causing data loss.
- Implemented at both the **Data Link layer** (hop-to-hop) and **Transport layer** (end-to-end, e.g., TCP).

## 2. Stop-and-Wait Protocol
- The **simplest** flow control method.
- Sender transmits **one frame**, then **waits** for an acknowledgment (ACK) from the receiver before sending the next frame.

```
Sender                          Receiver
  |------ Frame 0 ------------->|
  |                              | (processes frame)
  |<----------- ACK 0 -----------|
  |------ Frame 1 ------------->|
  |                              |
  |<----------- ACK 1 -----------|
```

- **Problem:** Very **inefficient** for high-bandwidth or high-latency (long-distance) links — the sender sits **idle** waiting for each ACK, drastically underutilizing available bandwidth.

> **Extra — Efficiency formula:** `Efficiency = Tt / (Tt + 2×Tp)`, where `Tt` = transmission time of one frame, `Tp` = propagation delay. As propagation delay (distance) grows relative to transmission time, efficiency drops sharply — this is exactly why Stop-and-Wait is impractical for satellite links or long-haul WANs.

## 3. Sliding Window Protocol
- Solves Stop-and-Wait's inefficiency by allowing the sender to transmit **multiple frames** (up to a "window size") **before** needing to wait for an acknowledgment.
- Both sender and receiver maintain a **"window"** — a range of sequence numbers that are currently valid to send/accept.
- As ACKs are received, the window **"slides"** forward, allowing more new frames to be sent.

```
Sender's window (size = 4), frames 0-7 available to send:

[0][1][2][3] 4  5  6  7      <- window currently covers frames 0-3
 ^^^^^^^^^^^
 can send all of these before needing an ACK

After ACK for frame 0 received, window slides forward:

 0 [1][2][3][4] 5  6  7      <- window now covers frames 1-4
```

## 4. Go-Back-N (GBN) ARQ
- A sliding window protocol where, if an error is detected (or a timeout occurs) for a particular frame, the sender **retransmits that frame AND all subsequent frames** already sent (even if some of those were received correctly) — "going back N" frames.
- **Receiver:** Uses a much simpler approach — it **only accepts frames in the correct, expected order**; any frame that arrives out of order is simply **discarded**, even if it was received without errors.
- **Cumulative ACK:** A single ACK can acknowledge multiple frames at once (ACK for frame N implicitly means all frames up to N were received correctly).

```
Sender sends: F0  F1  F2  F3  F4
                       X (F2 lost/corrupted)
Receiver gets: F0  F1  --   F3(discarded, out of order)  F4(discarded)
Receiver ACKs: ACK0 ACK1  (no ACK for F2, F3, F4 — they were discarded/not received correctly)

Sender times out waiting for ACK2, and retransmits F2, F3, F4 (even though F3 and F4
were originally transmitted — GBN wastefully resends them since receiver discarded them)
```

- **Simpler receiver** logic (doesn't need to buffer/reorder out-of-order frames), but **wastes bandwidth** by needlessly retransmitting correctly-received frames that just happened to arrive after a lost one.

## 5. Selective Repeat (SR) ARQ
- A more **efficient** (but more complex) sliding window protocol — only the **specific frame(s)** that were lost/corrupted are retransmitted, **not** the frames that follow it.
- **Receiver:** Must **buffer out-of-order frames** (accepting and storing frames even if they arrive out of sequence), and individually ACK/NACK each frame.
- Requires **individual ACKs** per frame (not cumulative), and both sender/receiver need larger buffers to hold out-of-order data.

```
Sender sends: F0  F1  F2  F3  F4
                       X (F2 lost/corrupted)
Receiver gets: F0  F1  --   F3(buffered, out of order)  F4(buffered)
Receiver ACKs: ACK0 ACK1  (NACK/timeout for F2)  ACK3  ACK4

Sender only retransmits F2 (the specific missing frame) — F3 and F4 don't need
to be resent, since the receiver already has them buffered correctly.
```

> **Extra — Go-Back-N vs Selective Repeat comparison table (very common interview question):**

| Aspect | Go-Back-N (GBN) | Selective Repeat (SR) |
|---|---|---|
| Retransmission scope | Retransmits the lost frame AND all subsequent frames | Retransmits ONLY the specific lost/corrupted frame |
| Receiver complexity | Simple — discards out-of-order frames | Complex — must buffer and reorder out-of-order frames |
| Receiver buffer needed | Minimal (only needs the next expected frame) | Larger (must hold multiple out-of-order frames) |
| Acknowledgment type | Cumulative ACK | Individual ACK per frame |
| Bandwidth efficiency | Lower (wastes bandwidth on unnecessary retransmissions) | Higher (only retransmits what's actually needed) |
| Sender window size vs Receiver window size | Sender window can be large; Receiver window = 1 | Both sender and receiver windows must be ≤ half the sequence number space (to avoid ambiguity) |

## 6. Sliding Window Size — Sequence Number Numerical
- If frames are numbered using `n` bits, the sequence numbers range from `0` to `2^n - 1`.

> **Extra — Worked example:** With `n = 3` bits for sequence numbers (0 to 7):
> - **Go-Back-N:** Maximum sender window size = `2^n - 1 = 7`.
> - **Selective Repeat:** Maximum sender (and receiver) window size = `2^(n-1) = 4` — this smaller limit is required specifically to avoid ambiguity between old and new frames when sequence numbers wrap around and are reused.

---

## Interview Questions & Answers

**Q1. What is Flow Control, and why is it needed?**
Flow Control is a mechanism that ensures a sender doesn't transmit data faster than a receiver can process and buffer it, preventing the receiver's buffer from overflowing and causing data loss. It's needed whenever there's a potential mismatch in processing speed or buffer capacity between a sender and a receiver.

**Q2. What is the main drawback of the Stop-and-Wait protocol?**
Its main drawback is poor bandwidth utilization/efficiency, especially over high-latency or high-bandwidth links — since the sender must send exactly one frame and then sit completely idle waiting for its acknowledgment before sending the next frame, a huge amount of potential transmission time is wasted waiting, particularly when the propagation delay is large relative to the actual frame transmission time (e.g., satellite links).

**Q3. How does the Sliding Window protocol improve upon Stop-and-Wait?**
Sliding Window allows a sender to transmit MULTIPLE frames (up to a defined window size) before needing to wait for an acknowledgment, rather than being limited to sending just one frame at a time. This keeps the communication channel much more continuously utilized, significantly improving throughput, especially over links with high propagation delay.

**Q4. What is the key difference between Go-Back-N and Selective Repeat ARQ protocols?**
In Go-Back-N, if a frame is lost or corrupted, the sender retransmits that frame AND all subsequent frames already sent, even ones that were actually received correctly by the receiver (since the receiver simply discards any out-of-order frames). In Selective Repeat, only the SPECIFIC lost/corrupted frame is retransmitted — the receiver buffers any correctly-received out-of-order frames instead of discarding them, making Selective Repeat more bandwidth-efficient but requiring more complexity and buffer space at the receiver.

**Q5. Why does Go-Back-N's receiver discard out-of-order frames, even if they were received without errors?**
Because Go-Back-N is designed to keep the RECEIVER'S logic simple — it only needs to track a single "next expected frame" sequence number, accepting frames strictly in order and discarding anything that doesn't match. This avoids the complexity (and buffer space) needed to store and later reorder out-of-sequence frames, at the cost of wasting bandwidth when the sender has to needlessly retransmit frames the receiver actually already received correctly (just out of order).

**Q6. Why does Selective Repeat require individual acknowledgments per frame, rather than cumulative acknowledgments like Go-Back-N?**
Because in Selective Repeat, the sender needs to know EXACTLY which specific frame(s) were lost or corrupted, so it can retransmit only those — a cumulative ACK (which just says "everything up to frame N was received") wouldn't provide enough granular information to identify a specific missing frame in the middle of a window of otherwise-successfully-received frames.

**Q7. Why must the window size in Selective Repeat be limited to at most half of the sequence number space (2^(n-1)), while Go-Back-N can use almost the full space (2^n - 1)?**
In Selective Repeat, both sender and receiver maintain their own sliding windows independently, and because the receiver buffers out-of-order frames, there's a risk of ambiguity if sequence numbers wrap around and a new frame's number could be confused with an old, already-acknowledged frame's number using the same value. Limiting the window to half the sequence number space ensures the sender's and receiver's windows never overlap in a way that creates this ambiguity. Go-Back-N doesn't have this issue as severely since its receiver only ever expects a single specific next frame (not a range of buffered ones), allowing it to safely use a larger window size.

**Q8. In what scenario would Go-Back-N actually perform WORSE than Selective Repeat, in practical terms?**
On a link with a high error rate (frequent lost/corrupted frames) combined with a large window size — since every single error in Go-Back-N triggers retransmission of that frame AND all frames sent after it (potentially many frames), whereas Selective Repeat would only need to retransmit the specific frame(s) that actually had errors, making Selective Repeat significantly more bandwidth-efficient in high-error-rate, large-window scenarios.

**Q9. Explain the Stop-and-Wait efficiency formula and what it tells us.**
The formula `Efficiency = Tt / (Tt + 2×Tp)` (where Tt = frame transmission time, Tp = one-way propagation delay) shows that efficiency depends on the ratio between the time actually spent transmitting data versus the total round-trip time spent waiting for an acknowledgment. It tells us that as propagation delay (Tp) grows large relative to transmission time (Tt) — such as over very long-distance or high-bandwidth links — efficiency drops sharply, since the sender spends proportionally more time idle, waiting, than actually transmitting useful data.

**Q10. If sequence numbers use 4 bits, what is the maximum window size for Go-Back-N versus Selective Repeat?**
With 4 bits, sequence numbers range from 0 to 15 (2^4 = 16 total values). For Go-Back-N, the maximum window size is `2^4 - 1 = 15`. For Selective Repeat, the maximum window size is `2^(4-1) = 2^3 = 8`, since Selective Repeat requires the window to be limited to at most half the total sequence number space to avoid ambiguity.

**Q11. Why is flow control implemented at both the Data Link layer AND the Transport layer — isn't this redundant?**
It's not truly redundant because they serve different scopes: Data Link layer flow control manages the flow of frames between two DIRECTLY connected nodes (a single physical hop, like a computer to its immediate switch), while Transport layer flow control (like TCP's sliding window) manages the flow of data between the ORIGINAL sender and the FINAL destination end-to-end, potentially across many intermediate hops/networks. Each layer addresses flow control at its own appropriate scope of responsibility.

**Q12. How does TCP's flow control (covered in later Transport Layer lectures) relate to the Sliding Window concept discussed here?**
TCP directly implements a form of the Sliding Window protocol at the Transport layer — using a "receive window" (advertised by the receiver in each TCP segment) to tell the sender how much additional data it's currently willing/able to buffer, allowing the sender to transmit multiple segments before needing acknowledgment, similar in principle to the Data Link layer's Sliding Window, just applied end-to-end across the full network path rather than a single physical hop.
