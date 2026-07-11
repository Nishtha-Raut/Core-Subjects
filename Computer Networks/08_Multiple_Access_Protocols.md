# 08: Multiple Access Protocols

## 1. Why Multiple Access Protocols?
- When multiple devices **share a single communication channel** (like a wireless medium or a shared bus cable), there needs to be a way to decide **who transmits when**, to avoid or manage **collisions** (when two or more devices transmit simultaneously and their signals interfere/corrupt each other).
- **Multiple Access Protocols** (also called **Medium Access Control / MAC protocols**) are the rules that govern this shared access.

## 2. Categories of Multiple Access Protocols

```
Multiple Access Protocols
       |
       ├── Random Access       (ALOHA, CSMA, CSMA/CD, CSMA/CA)
       ├── Controlled Access   (Reservation, Polling, Token Passing)
       └── Channelization      (FDMA, TDMA, CDMA)
```

## 3. Random Access Protocols
- Any station can transmit **at any time** — no coordination/turn-taking. This flexibility means **collisions are possible** and must be detected/handled.

### a. Pure ALOHA
- The **earliest and simplest** random access protocol (developed at the University of Hawaii).
- A station transmits **whenever it has data ready**, with no coordination at all.
- If a collision occurs (detected via lack of acknowledgment), the station **waits a random amount of time** and retransmits.
- **Vulnerable time** (time period during which a collision can occur) = **2 × Tt** (where Tt = time to transmit one frame) — because a collision can happen if another station starts transmitting anytime from one full frame-time before, to one full frame-time after, the current station's transmission.
- **Maximum channel utilization/efficiency ≈ 18.4%** (very low, due to high collision probability).

```
Station A: ----[Frame A]----
Station B:      ----[Frame B]----     <- overlaps with A = COLLISION
                 (both frames corrupted, must retransmit)
```

### b. Slotted ALOHA
- An improvement over Pure ALOHA — **time is divided into discrete slots**, and stations can **only start transmitting at the beginning of a slot** (not at any arbitrary time).
- This reduces the **vulnerable time to just Tt** (one frame-time), halving the collision window compared to Pure ALOHA.
- **Maximum channel utilization/efficiency ≈ 36.8%** — double that of Pure ALOHA.

> **Extra — Pure vs Slotted ALOHA comparison:**

| Aspect | Pure ALOHA | Slotted ALOHA |
|---|---|---|
| Transmission timing | Any time | Only at start of a time slot |
| Vulnerable time | 2 × Tt | 1 × Tt |
| Max efficiency | ~18.4% | ~36.8% |

### c. CSMA (Carrier Sense Multiple Access)
- Improves on ALOHA by having a station **"listen before talking"** — sense whether the channel is currently busy (carrier sensing) **before** transmitting.
- Reduces (but does **not eliminate**) collisions, since there's still a chance two stations sense an idle channel at nearly the same instant and transmit simultaneously (due to **propagation delay** — one station's signal hasn't reached the other yet).

**Persistence Strategies** (what to do if the channel is sensed busy):
- **1-Persistent CSMA:** Keep sensing continuously; transmit **immediately** once the channel becomes idle. Simple, but leads to higher collision chance if multiple stations are waiting.
- **Non-Persistent CSMA:** If busy, **wait a random amount of time**, then sense again. Reduces collisions, but can waste channel idle time (channel might sit idle while stations are in their random back-off).
- **P-Persistent CSMA:** If idle, transmit with probability `p`; with probability `(1-p)`, wait for the next slot and try again. Used mainly in slotted channels; balances collision reduction with reasonable channel utilization.

### d. CSMA/CD (Collision Detection)
- Extends CSMA by having stations **continue to monitor the channel WHILE transmitting** — if a collision is detected (garbled signal), the station **immediately stops transmitting**, sends a brief **jam signal** (to ensure all stations are aware of the collision), and then waits a random backoff time before retrying.
- Used in **classic wired Ethernet** (though largely obsolete now with full-duplex switched Ethernet, which eliminates collisions entirely by giving each device a dedicated point-to-point link).
- **Binary Exponential Backoff:** After each collision, the random wait-time range **doubles**, reducing the chance of repeated collisions between the same stations.

```
CSMA/CD flow:
1. Sense channel — is it idle?
2. If idle, start transmitting, WHILE continuing to listen.
3. If a collision is detected mid-transmission → STOP, send jam signal.
4. Wait random backoff time (exponentially increasing with each retry).
5. Go back to step 1.
```

### e. CSMA/CA (Collision Avoidance)
- Used in **wireless networks** (WiFi / 802.11), because **collision detection isn't feasible** in wireless media — a transmitting station generally can't simultaneously "listen" for a collision on the same channel it's transmitting on (its own signal overwhelms/masks any incoming weaker signal — this is the "**hidden terminal problem**" territory).
- Instead of detecting collisions after they happen, CSMA/CA tries to **avoid** them proactively:
  1. **Sense the channel** — if idle, wait for a short, fixed period (**IFS — InterFrame Space**), then transmit.
  2. If busy, wait using a **random backoff timer** before attempting again.
  3. Optionally uses **RTS/CTS (Request to Send / Clear to Send)** handshake — sender sends a short RTS frame, receiver replies with CTS, reserving the channel for the actual data transmission and helping solve the **hidden terminal problem** (where two senders can't hear each other directly but both can reach the same receiver).

> **Extra — CSMA/CD vs CSMA/CA comparison table (very common interview question):**

| Aspect | CSMA/CD | CSMA/CA |
|---|---|---|
| Used in | Wired networks (classic Ethernet) | Wireless networks (WiFi) |
| Approach | Detect collision AFTER it happens, then stop & retry | Try to AVOID collision proactively before it happens |
| Why the difference? | Wired medium allows simultaneous transmit+listen to detect collisions | Wireless medium makes simultaneous transmit+listen impractical (own signal drowns out incoming) |
| Key mechanism | Jam signal + binary exponential backoff | IFS wait + random backoff + optional RTS/CTS handshake |

## 4. Controlled Access Protocols
- Coordination ensures **only one station transmits at a time**, eliminating collisions entirely (at the cost of some coordination overhead).

### a. Reservation
- Time is divided into intervals; before each interval, stations **make reservations** for the slots they want to use.

### b. Polling
- A designated **primary/controller station** polls each secondary station in turn, asking "do you have data to send?" — only the polled station may transmit.

### c. Token Passing
- A special **token** (a small control frame) circulates around the network (often in a ring topology); only the station **currently holding the token** is allowed to transmit. After transmitting (or if it has nothing to send), it passes the token to the next station.
- Used in: **Token Ring**, **FDDI** networks (largely legacy now).

```
     A ----token----> B
     ^                 |
     |                token
     |                 |
     D <---token------ C
  (token circulates around the ring; only the holder may transmit)
```

## 5. Channelization Protocols
- The available channel bandwidth is **divided and shared** among stations using FDM, TDM, or CDM principles (covered in LEC-04 Multiplexing).
- **FDMA (Frequency Division Multiple Access):** Each station gets its own frequency band.
- **TDMA (Time Division Multiple Access):** Each station gets its own time slot.
- **CDMA (Code Division Multiple Access):** Each station uses a unique **code** to spread its signal across the entire available bandwidth simultaneously with others, and the receiver uses the matching code to extract the intended signal from the combined transmission — used in some cellular network standards.

---

## Interview Questions & Answers

**Q1. Why are Multiple Access Protocols needed?**
They're needed whenever multiple devices share a single communication channel (like a shared cable or wireless medium), to coordinate who transmits and when, preventing or managing collisions that would otherwise corrupt data when two or more devices transmit simultaneously.

**Q2. What is the difference between Pure ALOHA and Slotted ALOHA?**
Pure ALOHA allows a station to transmit at any arbitrary time whenever it has data ready, giving it a vulnerable (collision-prone) time window of 2×Tt (frame transmission time). Slotted ALOHA improves on this by dividing time into discrete slots and only allowing transmission at the start of a slot, reducing the vulnerable time to just 1×Tt — this doubles the maximum theoretical efficiency, from about 18.4% (Pure ALOHA) to about 36.8% (Slotted ALOHA).

**Q3. What is Carrier Sensing in CSMA, and why doesn't it completely eliminate collisions?**
Carrier Sensing means a station "listens" to check whether the channel is currently busy before attempting to transmit. It doesn't completely eliminate collisions because of propagation delay — two stations might both sense the channel as idle at nearly the same moment (before either one's transmission signal has had time to reach the other), and both decide to transmit simultaneously, still resulting in a collision.

**Q4. What is the difference between 1-Persistent, Non-Persistent, and P-Persistent CSMA?**
1-Persistent CSMA continuously senses the channel and transmits immediately once it becomes idle — simple but prone to higher collision rates if multiple stations are waiting. Non-Persistent CSMA waits a random amount of time before re-sensing if the channel is busy, reducing collisions but potentially wasting idle channel time. P-Persistent CSMA (typically used with slotted channels) transmits with probability p when the channel is idle, or waits for the next slot with probability (1-p), balancing collision reduction against channel utilization.

**Q5. How does CSMA/CD work, and where is it typically used?**
CSMA/CD (Collision Detection) has a station continue monitoring the channel WHILE it's transmitting — if it detects a collision (a garbled/interfered signal), it immediately stops transmitting, sends a brief jam signal to alert other stations, and then waits a random backoff time (which increases exponentially with repeated collisions) before retrying. It's historically used in classic wired Ethernet networks, though it's largely obsolete now with modern full-duplex switched Ethernet, which avoids collisions entirely through dedicated point-to-point links.

**Q6. Why can't CSMA/CD be used in wireless networks, and what's used instead?**
In wireless networks, a transmitting station generally cannot simultaneously and reliably "listen" for a collision on the same channel it's actively transmitting on, since its own outgoing signal is far stronger than any incoming signal it might be trying to detect (making collision detection impractical). Instead, wireless networks use CSMA/CA (Collision Avoidance), which proactively tries to avoid collisions before they happen, rather than detecting them after the fact.

**Q7. What is the Hidden Terminal Problem, and how does CSMA/CA address it?**
The Hidden Terminal Problem occurs when two stations can't directly sense/hear each other (perhaps due to distance or obstacles) but can both reach the same receiver — each station might sense the channel as idle (not hearing the other) and transmit simultaneously, causing a collision at the shared receiver that neither sender is aware of. CSMA/CA addresses this using an optional RTS/CTS (Request to Send / Clear to Send) handshake — the sender sends a short RTS frame, and the receiver replies with a CTS frame that all nearby stations (including "hidden" ones that can hear the receiver) can detect, reserving the channel and preventing them from transmitting during that reserved period.

**Q8. What is Token Passing, and what type of collisions does it prevent?**
Token Passing is a controlled access protocol where a special control frame (the "token") circulates among stations (often in a ring topology), and only the station currently holding the token is permitted to transmit. Since only one station can hold and use the token at any given time, this approach completely PREVENTS collisions altogether (unlike random access methods, which only detect or try to avoid them).

**Q9. What is the difference between Random Access, Controlled Access, and Channelization protocol categories?**
Random Access protocols (ALOHA, CSMA variants) allow any station to attempt transmission at any time, risking collisions that must then be detected/avoided/resolved. Controlled Access protocols (Polling, Token Passing, Reservation) coordinate access so that only one station can transmit at a time, eliminating collisions entirely but requiring coordination overhead. Channelization protocols (FDMA, TDMA, CDMA) divide the available channel itself (by frequency, time, or code) among stations, so each effectively gets its own dedicated "slice" of the channel to use without any contention.

**Q10. What is Binary Exponential Backoff, and why is it used in CSMA/CD?**
Binary Exponential Backoff is a strategy where, after each collision, a station's random wait-time range before retrying DOUBLES (grows exponentially) with each successive collision for the same frame. It's used to progressively reduce the likelihood of the same stations repeatedly colliding with each other, since spreading out their retry attempts over an increasingly wider random range makes it statistically less likely they'll pick overlapping retry times again.

**Q11. How does CDMA differ fundamentally from FDMA and TDMA?**
FDMA and TDMA both divide the shared channel into distinct, non-overlapping SEPARATE resources (either different frequency bands or different time slots) that each station uses exclusively. CDMA, in contrast, allows ALL stations to transmit simultaneously across the SAME entire frequency band and time, but each station's data is encoded using a unique mathematical "code" — the receiver uses the matching code to extract just the intended signal from the combined, overlapping transmission, effectively separating signals in the "code domain" rather than the frequency or time domain.

**Q12. Why might a modern wired Ethernet network no longer need CSMA/CD at all?**
Because modern Ethernet networks predominantly use full-duplex switched connections, where each device has its own dedicated, point-to-point link directly to a switch port (rather than sharing a single physical cable/bus with multiple other devices, as in old-style hub-based or coaxial Ethernet). Since each device essentially has an exclusive, collision-free path for both sending and receiving simultaneously, there's no shared medium left to contend for, making CSMA/CD's collision detection mechanism entirely unnecessary in these modern setups.
