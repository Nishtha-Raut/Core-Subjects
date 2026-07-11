# 24: Network Performance Metrics

## 1. Bandwidth
- The **maximum theoretical data-carrying capacity** of a link, typically measured in **bits per second** (bps, Kbps, Mbps, Gbps).
- Think of it as the **"width of the pipe"** — how much data COULD flow through per second, under ideal conditions.

## 2. Throughput
- The **actual amount of data successfully transferred** over a link in a given time period — the **real-world, measured** rate.
- **Throughput is always ≤ Bandwidth** — actual performance is affected by overhead (protocol headers), congestion, errors/retransmissions, and other real-world factors that prevent hitting the theoretical maximum.

> **Extra — Bandwidth vs Throughput analogy:** Bandwidth is like the **maximum speed limit** on a highway (e.g., 100 km/h) — the theoretical best case. Throughput is the **actual average speed** a car achieves on that highway, accounting for traffic, red lights, and other real-world conditions — always less than or equal to the speed limit.

## 3. Latency (Delay)
- The **time taken** for a single bit/packet of data to travel from sender to receiver.
- Composed of **four components**:

| Component | Description |
|---|---|
| **Propagation Delay** | Time for a signal to physically travel across the medium — depends on distance and the medium's signal speed (e.g., speed of light in fiber). `Propagation Delay = Distance / Propagation Speed` |
| **Transmission Delay** | Time to actually PUSH all the bits of a packet onto the link — depends on packet size and link bandwidth. `Transmission Delay = Packet Size / Bandwidth` |
| **Processing Delay** | Time a router/switch takes to examine a packet's header and decide where to forward it. |
| **Queuing Delay** | Time a packet spends WAITING in a router's buffer/queue before it can actually be transmitted, due to congestion/other traffic. |

```
Total Latency = Propagation Delay + Transmission Delay + Processing Delay + Queuing Delay
```

> **Extra — Worked example (common numerical interview question):**
> A 1000-byte (8000-bit) packet is sent over a link with Bandwidth = 1 Mbps, and the link is 2000 km long, with a propagation speed of 2×10^8 m/s. Assume negligible processing/queuing delay.
> 1. **Transmission Delay** = Packet Size / Bandwidth = `8000 bits / 1,000,000 bps = 0.008 sec = 8 ms`
> 2. **Propagation Delay** = Distance / Speed = `2,000,000 m / (2×10^8 m/s) = 0.01 sec = 10 ms`
> 3. **Total Latency** ≈ `8 ms + 10 ms = 18 ms`

## 4. Bandwidth-Delay Product (BDP)
- Represents the **maximum amount of data that can be "in flight"** on a link at any given moment (data that has been sent, but not yet acknowledged) — essentially the **"capacity" of the link's pipe itself**, in terms of data volume.
- **Formula:** `BDP = Bandwidth × Round-Trip Time (RTT)`

> **Extra — Why BDP matters for TCP performance (common follow-up interview question):** For TCP to fully utilize a high-bandwidth, high-latency link (like a satellite or trans-continental connection — the classic "Long Fat Network" or LFN), the TCP **window size** must be at least as large as the BDP — otherwise, the sender will run out of "sendable" data (having sent its entire window) and be forced to sit idle, WAITING for acknowledgments, before it's allowed to send more, even though the link itself still has plenty of unused capacity. This is precisely why TCP's **Window Scaling** option (extending beyond the original 16-bit window size field's ~64KB limit) was introduced — to support the much larger window sizes needed on modern high-BDP links.

## 5. Jitter
- The **variation in latency** between consecutively received packets — not the latency itself, but its **inconsistency**.
- Critical for **real-time applications** (VoIP, video conferencing) — even if average latency is acceptable, HIGH jitter causes choppy, inconsistent playback, since packets arrive at unpredictable intervals rather than a smooth, steady stream.
- Often mitigated using a **jitter buffer** at the receiver — deliberately holding/delaying received packets briefly, to smooth out arrival-time variations before playback, at the cost of adding a small amount of consistent additional latency.

## 6. Packet Loss
- The **percentage of packets that fail to reach their destination**, typically due to network congestion, errors, or hardware failures.
- Different protocols handle this differently: **TCP** detects and retransmits lost packets (trading some delay for reliability); **UDP** does nothing about it (the application must handle loss itself, if needed — or simply tolerate it, as many real-time media applications do).

## 7. Summary Table — Key Metrics at a Glance

| Metric | What it Measures | Typical Unit |
|---|---|---|
| **Bandwidth** | Theoretical maximum capacity | bps, Mbps, Gbps |
| **Throughput** | Actual achieved data transfer rate | bps, Mbps, Gbps |
| **Latency** | Time for data to travel one-way (or round-trip) | ms |
| **Jitter** | Variation in latency between packets | ms |
| **Packet Loss** | Percentage of packets that don't arrive | % |
| **BDP** | Maximum data "in flight" on a link at once | bits or bytes |

---

## Interview Questions & Answers

**Q1. What is the difference between Bandwidth and Throughput?**
Bandwidth is the theoretical maximum data-carrying capacity of a link, representing the best-case scenario under ideal conditions. Throughput is the actual, real-world measured rate of data successfully transferred over that link, which is always less than or equal to bandwidth, due to real-world factors like protocol overhead, congestion, and packet loss/retransmission.

**Q2. What are the four components that make up total network latency?**
Propagation Delay (time for a signal to physically travel across the medium, based on distance and signal speed), Transmission Delay (time to actually push all the bits of a packet onto the link, based on packet size and bandwidth), Processing Delay (time a router takes to examine and make a forwarding decision for a packet), and Queuing Delay (time a packet spends waiting in a router's buffer due to congestion before it can be transmitted).

**Q3. What is the difference between Propagation Delay and Transmission Delay?**
Propagation Delay depends on the physical DISTANCE the signal must travel and the medium's signal speed (e.g., how fast light travels through fiber) — it's about how long it takes a single bit to physically traverse the link. Transmission Delay depends on the packet's SIZE and the link's bandwidth — it's about how long it takes to push ALL the bits of an entire packet onto the link in the first place, before that data even starts propagating toward the destination.

**Q4. What is the Bandwidth-Delay Product (BDP), and why is it significant for TCP performance?**
BDP represents the maximum amount of data that can be "in flight" (sent but not yet acknowledged) on a link at any given time, calculated as Bandwidth × Round-Trip Time. It's significant for TCP because, for a sender to fully utilize a high-bandwidth, high-latency link, its congestion/flow-control window size must be AT LEAST as large as the BDP — otherwise, the sender will exhaust its allowed "in-flight" data and be forced to idle waiting for acknowledgments, even though the underlying link still has unused capacity available.

**Q5. What is Jitter, and why is it particularly problematic for real-time applications like VoIP?**
Jitter is the VARIATION in latency between consecutively received packets, rather than the latency itself. It's particularly problematic for real-time applications because even if AVERAGE latency is perfectly acceptable, high jitter means packets arrive at unpredictable, inconsistent intervals rather than a smooth, steady stream — this causes choppy, stuttering audio/video playback, since the application can't reliably predict exactly when the next piece of data will arrive.

**Q6. How does a Jitter Buffer help address the jitter problem, and what trade-off does it introduce?**
A Jitter Buffer works by deliberately holding/delaying received packets briefly at the receiver's end, effectively smoothing out the inconsistent arrival-time variations before the data is actually played back/processed, resulting in a much steadier, more consistent playback experience. The trade-off is that this deliberate buffering introduces a small amount of ADDITIONAL, consistent latency overall, since packets are intentionally held for a moment rather than processed the instant they arrive.

**Q7. How do TCP and UDP differ in their handling of packet loss?**
TCP actively detects packet loss (through mechanisms like timeouts or duplicate ACKs) and automatically retransmits the lost data, trading some additional delay for guaranteed reliability/completeness. UDP does nothing about packet loss at the protocol level — if a UDP datagram is lost, it's simply gone, and it's entirely up to the APPLICATION to either implement its own loss-handling logic if needed, or simply tolerate the loss (which is common for many real-time media applications, where a lost packet just causes a brief, minor glitch rather than requiring a delay-inducing retransmission).

**Q8. Calculate the total latency for a 500-byte (4000-bit) packet sent over a 2 Mbps link that is 1000 km long, assuming a propagation speed of 2×10^8 m/s and negligible processing/queuing delay.**
Transmission Delay = Packet Size / Bandwidth = 4000 bits / 2,000,000 bps = 0.002 sec = 2 ms. Propagation Delay = Distance / Speed = 1,000,000 m / (2×10^8 m/s) = 0.005 sec = 5 ms. Total Latency ≈ 2 ms + 5 ms = 7 ms.

**Q9. Why might a network connection have high bandwidth but still feel "slow" to a user browsing the web?**
This is often due to HIGH LATENCY rather than insufficient bandwidth — web browsing typically involves many small, sequential requests/responses (e.g., loading a page, then its CSS, then various images, potentially involving multiple round-trips), and if each round-trip has significant latency (e.g., due to long propagation delay from a geographically distant server), the CUMULATIVE effect of many sequential round-trips can make browsing feel sluggish, even if the underlying bandwidth would easily support transferring the actual total data volume involved very quickly, if it could all be sent at once without waiting for each round-trip.

**Q10. Why is BDP sometimes called the "size of the pipe," and how does this relate to TCP Window Scaling?**
BDP represents the total volume of data that can be actively "in transit" within the link at any given moment — conceptually like the physical VOLUME/CAPACITY of a pipe that's already "full" of flowing data, based on how much data fits given the pipe's width (bandwidth) and length (delay/RTT). TCP's original window size field was limited to 16 bits (max ~64KB), which became insufficient to fully utilize the BDP of modern high-bandwidth, high-latency links (like satellite or trans-continental fiber connections) — TCP Window Scaling was introduced specifically to allow much larger window sizes, enabling TCP connections to keep enough data "in flight" to fully utilize these high-BDP links' available capacity.

**Q11. Give a real-world example illustrating the difference between latency and jitter, using a video call scenario.**
Latency in a video call refers to the overall DELAY between when someone speaks and when the other person hears/sees it — a video call with 200ms latency has a noticeable but potentially tolerable delay. Jitter, separately, refers to the CONSISTENCY of that delay — if latency fluctuates unpredictably (e.g., sometimes 100ms, sometimes 400ms, moment to moment) even while AVERAGING around 200ms, this inconsistency (high jitter) causes the audio/video to sound/appear choppy and stuttering, which can actually be MORE disruptive to a smooth conversation than a slightly higher but perfectly CONSISTENT latency would be.

**Q12. Why does Queuing Delay specifically increase during periods of network congestion, while the other latency components (Propagation, Transmission, Processing) generally remain relatively constant?**
Propagation Delay depends on physical distance (fixed), Transmission Delay depends on packet size and link bandwidth (both generally fixed for a given packet/link), and Processing Delay depends on the router's fixed per-packet examination time — none of these are significantly affected by how much OTHER traffic is also trying to use the network at the same time. Queuing Delay, however, is DIRECTLY caused by congestion — when more packets arrive at a router than it can immediately forward, they must WAIT in a buffer/queue; the more congested the network (more competing traffic), the LONGER this queue becomes, and the longer individual packets must wait their turn, directly increasing Queuing Delay specifically during congested periods.
