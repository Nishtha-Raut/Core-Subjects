# 15: Congestion Control

## 1. What is Congestion?
- **Congestion** occurs when the **network (routers/links)** becomes overloaded — too much traffic being sent relative to the network's capacity to handle it — leading to **packet loss, increased delay, and reduced throughput**.
- **Congestion Control** is fundamentally different from **Flow Control** (LEC-07):
  - **Flow Control:** Prevents a fast SENDER from overwhelming a slow RECEIVER (an end-point problem).
  - **Congestion Control:** Prevents too much traffic from overwhelming the NETWORK itself (routers/links in between) — a network-wide problem.

> **Extra — Flow Control vs Congestion Control comparison:**

| Aspect | Flow Control | Congestion Control |
|---|---|---|
| Concerned with | Receiver's buffer capacity | Network's (routers/links) overall capacity |
| Scope | Between sender and receiver (end-to-end) | Involves the entire network path |
| Mechanism example | Receiver's advertised window size | Congestion window (cwnd), managed by the sender based on network feedback |

## 2. TCP Congestion Control — Core Mechanism
- TCP maintains a **Congestion Window (cwnd)** — in addition to the receiver's advertised (flow-control) window — representing how much data the sender believes it can safely send **without overwhelming the network**.
- **Actual amount sendable = min(cwnd, receiver's advertised window)** — TCP respects whichever constraint is smaller.
- TCP treats **packet loss** (detected via timeout or duplicate ACKs) as a signal that the network is congested, and reduces its sending rate in response.

## 3. TCP Congestion Control Phases

```
cwnd (size)
   ^
   |                    /\
   |                   /  \___    <- Congestion Avoidance (linear growth)
   |                  /       \___
   |     Slow Start  /            \___ (loss detected -> cwnd cut)
   |    (exponential)/
   |    /\/         /
   |   /            
   |  /
   | /
   +----------------------------------------> time
```

### a. Slow Start
- Begins with a **small cwnd** (e.g., 1 MSS — Maximum Segment Size).
- For **every ACK received**, cwnd **increases by 1 MSS** — since this happens for every segment ACKed within a round-trip, cwnd effectively **doubles every RTT (Round Trip Time)** — this is **exponential growth**.
- Continues until cwnd reaches a threshold called **ssthresh (slow start threshold)**, or until a packet loss is detected.

### b. Congestion Avoidance
- Once cwnd reaches **ssthresh**, TCP switches to a much more **conservative, linear growth**: cwnd increases by roughly **1 MSS per RTT** (rather than doubling) — this is the **AIMD** approach (see below).
- Continues until packet loss is detected.

### c. Congestion Detection & Reaction
- **On Timeout (severe congestion signal):**
  1. **ssthresh is set to half** of the current cwnd.
  2. **cwnd is reset all the way down to 1 MSS**, and Slow Start begins again from scratch.
- **On 3 Duplicate ACKs (Fast Retransmit — a milder congestion signal, since some segments are still getting through):**
  1. **ssthresh is set to half** of the current cwnd.
  2. **cwnd is set to ssthresh** (NOT reset all the way to 1) — TCP jumps straight into Congestion Avoidance, skipping a full Slow Start restart. This is called **Fast Recovery**.

> **Extra — Why the different reaction to Timeout vs Duplicate ACKs?** A **Timeout** indicates a segment was lost with NO subsequent segments getting through at all — a sign of potentially severe congestion, warranting a full, cautious restart via Slow Start. **Duplicate ACKs**, however, indicate that later segments (after the lost one) ARE still successfully arriving — meaning the network isn't nearly as badly congested, so TCP can afford a much less drastic response (Fast Recovery) instead of a full reset.

## 4. AIMD (Additive Increase, Multiplicative Decrease)
- The core congestion-avoidance philosophy behind TCP's behavior:
  - **Additive Increase:** While things are going well (ACKs received, no loss), cwnd grows **slowly and linearly** (by a small, fixed additive amount per RTT) — cautious, gradual probing for more available bandwidth.
  - **Multiplicative Decrease:** As soon as congestion (loss) is detected, cwnd is **cut drastically** (typically halved, or reset to 1) — an aggressive, immediate backoff to quickly relieve network pressure.
- This creates TCP's characteristic **"sawtooth" pattern** in cwnd over time (visible in the phase diagram above) — a hallmark, easily recognizable graph in networking interviews/exams.

> **Extra — Why AIMD, and not some other combination (like Multiplicative Increase, Multiplicative Decrease)?** AIMD has a proven mathematical property: it converges toward **fair bandwidth sharing** among multiple competing TCP flows over time. Growing cautiously (additive) avoids overshooting and re-triggering congestion too quickly, while backing off aggressively (multiplicative) ensures a fast, decisive response when congestion is actually detected — this specific combination is what gives TCP its stability and fairness properties across many simultaneous connections sharing a network.

## 5. Nagle's Algorithm
- Addresses the inefficiency of sending many **very small** TCP segments (e.g., single-keystroke data in an old-school Telnet session), each carrying a large proportional header overhead relative to the tiny amount of actual data.
- **Rule:** If there is unacknowledged data already in flight, **buffer/hold** any new small amount of data, and only send it once either (a) a full-sized segment's worth of data has accumulated, or (b) an ACK for the previously sent data is received.
- **Trade-off:** Improves network efficiency (fewer, larger segments) but can introduce small additional latency — often **disabled** (via the `TCP_NODELAY` socket option) for latency-sensitive applications (like real-time gaming or interactive terminals) where waiting to batch small messages together is undesirable.

## 6. QoS (Quality of Service) — Related Concept
- While Congestion Control reacts to congestion, **QoS** proactively manages traffic to provide differentiated service levels for different types of traffic, based on their specific needs.

### Key QoS Parameters
| Parameter | Meaning |
|---|---|
| **Bandwidth** | Data-carrying capacity of the link |
| **Latency (Delay)** | Time taken for data to travel from sender to receiver |
| **Jitter** | Variation in latency between consecutive packets — critical for real-time audio/video, where inconsistent delay causes choppy playback |
| **Packet Loss** | Percentage of packets that fail to arrive |

- **Techniques:** Traffic prioritization/classification (e.g., prioritizing VoIP traffic over regular web browsing traffic), traffic shaping/policing (controlling the rate of traffic entering the network), and dedicated bandwidth reservation for critical applications.

---

## Interview Questions & Answers

**Q1. What is the difference between Flow Control and Congestion Control?**
Flow Control manages the data rate between a specific sender and receiver, preventing the sender from overwhelming the RECEIVER's buffer capacity (an end-to-end, two-party concern). Congestion Control manages the data rate to prevent overwhelming the NETWORK itself (the routers and links in between), which is a network-wide concern potentially affecting many different flows sharing the same infrastructure.

**Q2. Explain TCP's Slow Start phase.**
Slow Start begins with a small congestion window (cwnd), typically 1 MSS (Maximum Segment Size). For every ACK received, cwnd increases by 1 MSS — since this effectively happens for every segment acknowledged within a round-trip time, the overall congestion window roughly DOUBLES every RTT, representing exponential growth. This continues until cwnd reaches the slow start threshold (ssthresh) or a packet loss is detected.

**Q3. What happens when cwnd reaches ssthresh during Slow Start?**
TCP transitions from Slow Start's exponential growth into the Congestion Avoidance phase, where cwnd growth becomes much more conservative — increasing by roughly just 1 MSS per round-trip time (linear growth), rather than doubling, as TCP cautiously probes for additional available bandwidth without risking triggering congestion too aggressively.

**Q4. What is the difference in TCP's response to a Timeout versus 3 Duplicate ACKs?**
On a Timeout (indicating no segments are getting through at all, a sign of potentially severe congestion), TCP sets ssthresh to half the current cwnd, and resets cwnd all the way down to 1 MSS, restarting Slow Start from scratch. On 3 Duplicate ACKs (Fast Retransmit — indicating later segments ARE still getting through, so congestion is likely less severe), TCP still halves ssthresh, but sets cwnd directly to the new ssthresh value (not all the way down to 1), jumping straight into Congestion Avoidance — this milder response is called Fast Recovery.

**Q5. What does AIMD stand for, and why is this specific combination used by TCP?**
AIMD stands for Additive Increase, Multiplicative Decrease — TCP grows its congestion window slowly and linearly (additively) when things are going well, but cuts it drastically (multiplicatively, typically halved) as soon as congestion/loss is detected. This specific combination is used because it has a proven mathematical property of converging toward fair bandwidth sharing among multiple competing TCP flows over time, while still responding quickly and decisively to actual congestion when it occurs.

**Q6. Why does TCP's congestion window graph typically show a "sawtooth" pattern over time?**
Because of AIMD's behavior — cwnd grows slowly and steadily (the gradual upward slope of the "tooth") during periods without congestion, then gets abruptly and drastically cut (the sharp vertical drop) whenever congestion/packet loss is detected, before the slow, gradual growth begins again from this lower point — this repeated cycle of gradual increase followed by sharp decrease produces the characteristic sawtooth pattern visible in a plot of cwnd over time.

**Q7. What problem does Nagle's Algorithm solve?**
Nagle's Algorithm addresses the inefficiency of sending many very small TCP segments (each carrying a disproportionately large header overhead relative to their tiny amount of actual payload data) — such as single keystrokes in an old interactive terminal session. It buffers/holds small amounts of new data (when there's already unacknowledged data in flight) until either enough data accumulates to fill a full segment, or an acknowledgment for the previous data is received, resulting in fewer, more efficiently-sized segments being sent overall.

**Q8. Why might a real-time application (like online gaming) want to disable Nagle's Algorithm?**
Because Nagle's Algorithm intentionally introduces a small delay by buffering/holding small pieces of data before sending them, in order to batch them more efficiently — but for latency-sensitive real-time applications, this added delay (even if small) can be undesirable, since immediate delivery of each small update matters more than the network efficiency gained from batching. Such applications typically disable Nagle's Algorithm using the `TCP_NODELAY` socket option, prioritizing low latency over bandwidth efficiency.

**Q9. What is Jitter, and why does it matter specifically for real-time audio/video applications?**
Jitter is the VARIATION in latency (delay) between consecutive packets arriving at the destination — even if average latency is low, if it fluctuates significantly from packet to packet, this inconsistency causes problems for real-time audio/video applications specifically, since they need a steady, predictable stream of data to play back smoothly; high jitter causes choppy, stuttering playback, even if overall packet loss and average latency are both otherwise acceptable.

**Q10. How does QoS differ from Congestion Control, and how might they work together?**
Congestion Control is primarily a REACTIVE mechanism — it responds to detected congestion (packet loss) by reducing sending rate. QoS is a more PROACTIVE approach — it involves classifying, prioritizing, and shaping different types of traffic based on their specific requirements (e.g., ensuring VoIP traffic gets priority over regular file downloads) to prevent certain critical traffic types from being negatively affected by congestion in the first place. They can work together — QoS mechanisms can help ensure that even when a network does experience some congestion (triggering congestion control responses), critical, latency-sensitive traffic is still prioritized and protected as much as possible relative to less time-sensitive traffic.

**Q11. If a TCP connection experiences a Timeout very early in its lifecycle (while cwnd is still small, close to its Slow Start starting value), what does this suggest about ssthresh's usefulness at that point?**
Since ssthresh gets set to half of the CURRENT cwnd value upon a Timeout, and cwnd was already quite small at that early point, the resulting new ssthresh value would also be quite small — meaning TCP would transition into Congestion Avoidance's slower, linear growth phase very quickly after restarting Slow Start, even though cwnd is still very low. This illustrates how ssthresh dynamically adapts based on actual observed network conditions, rather than being a fixed, predetermined value.

**Q12. Why is Congestion Control considered essential for the overall stability of a large, shared network like the Internet, rather than just being a nice-to-have optimization?**
Without congestion control, many simultaneous TCP connections would all continue sending data as fast as their local flow-control windows allowed, regardless of actual network capacity — this could easily lead to a phenomenon called "congestion collapse," where the network becomes so overloaded with excessive traffic (and resulting retransmissions of lost packets) that its EFFECTIVE useful throughput drops dramatically, even as raw traffic volume increases. TCP's congestion control mechanisms (AIMD, Slow Start, etc.) are specifically designed to prevent this scenario, ensuring that as network load increases, senders proactively and cooperatively back off, maintaining overall network stability and reasonably fair bandwidth sharing among all the many competing flows.
