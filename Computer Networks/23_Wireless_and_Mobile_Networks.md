# 23: Wireless & Mobile Networks

## 1. WiFi Standards (IEEE 802.11)

| Standard | Marketing Name | Frequency Band | Max Theoretical Speed | Notes |
|---|---|---|---|---|
| 802.11b | — | 2.4 GHz | 11 Mbps | Legacy |
| 802.11g | — | 2.4 GHz | 54 Mbps | Legacy |
| 802.11n | WiFi 4 | 2.4 / 5 GHz | 600 Mbps | Introduced MIMO (Multiple Input Multiple Output) |
| 802.11ac | WiFi 5 | 5 GHz | ~3.5 Gbps | Wider channels, more MIMO streams |
| 802.11ax | WiFi 6 / 6E | 2.4 / 5 / 6 GHz | ~9.6 Gbps | OFDMA (better multi-device efficiency), improved battery life (Target Wake Time) |

> **Extra — 2.4 GHz vs 5 GHz band trade-off (common interview question):**

| Aspect | 2.4 GHz | 5 GHz |
|---|---|---|
| Range | Longer (better wall/obstacle penetration) | Shorter (worse obstacle penetration) |
| Speed | Lower | Higher |
| Interference | More crowded (Bluetooth, microwaves, many devices share this band) | Less crowded, more channels available |
| Best for | Coverage across a larger area | High-speed, short-range use (e.g., same room as router) |

## 2. WiFi Architecture — Basic Concepts

```
                [Access Point (AP)]
               /        |         \
         [Laptop]   [Phone]    [Smart TV]
        (BSS - Basic Service Set: all devices
         connected to ONE access point)
```

- **BSS (Basic Service Set):** A single Access Point and all the client devices connected to it.
- **ESS (Extended Service Set):** Multiple BSSs (multiple Access Points) working together, typically sharing the SAME network name (SSID), allowing devices to **roam** seamlessly between APs (e.g., moving through a large office building with multiple APs).
- **SSID (Service Set Identifier):** The human-readable network name (e.g., "Home_WiFi_5G") that identifies a specific wireless network.

## 3. WiFi Security Standards

| Standard | Security Level | Notes |
|---|---|---|
| **WEP** (Wired Equivalent Privacy) | Very weak | Obsolete, easily crackable — should never be used |
| **WPA** (WiFi Protected Access) | Weak-Moderate | Improvement over WEP, but still has known vulnerabilities |
| **WPA2** | Strong | Uses AES encryption; standard for many years, still widely used |
| **WPA3** | Strongest (current) | Improved encryption, better protection against offline password-guessing attacks, forward secrecy |

## 4. Cellular Network Generations

```
1G ---> 2G ---> 3G ---> 4G (LTE) ---> 5G
Analog  Digital  Mobile  Mobile      Ultra-fast,
voice   voice+   Internet Broadband   low-latency,
only    SMS      (basic)  Internet    massive IoT
                                       connectivity
```

### 1G (1980s)
- **Analog** voice calls only — no data, no digital encryption (easily intercepted).

### 2G (1990s)
- First **Digital** cellular standard — introduced **voice + SMS** text messaging.
- **GSM (Global System for Mobile Communications)** became the dominant global standard.
- Basic data services introduced later (GPRS, EDGE — very slow by modern standards).

### 3G (2000s)
- Introduced proper **mobile Internet/data** access — enabled basic web browsing, email, video calling on mobile devices for the first time.
- Speeds typically in the range of a few Mbps.

### 4G / LTE (2010s)
- **All-IP network** (unlike earlier generations which had separate voice/data architectures) — voice calls themselves are carried as data (**VoLTE — Voice over LTE**).
- Significantly higher speeds (tens to hundreds of Mbps), enabling HD video streaming, modern app-based mobile experiences.

### 5G (2019 onwards)
- **Three key improvement areas:**
  1. **Enhanced Mobile Broadband (eMBB):** Extremely high speeds (potentially multi-Gbps).
  2. **Ultra-Reliable Low-Latency Communication (URLLC):** Very low latency (as low as ~1ms) — critical for applications like autonomous vehicles, remote robotic surgery.
  3. **Massive Machine-Type Communication (mMTC):** Supports a huge density of connected devices per area — critical for large-scale **IoT (Internet of Things)** deployments.
- Uses higher-frequency **millimeter wave (mmWave)** spectrum for peak speeds (though this has very short range/poor obstacle penetration, requiring many more, closely-spaced small cell towers) alongside lower/mid-band frequencies for broader coverage.

> **Extra — Cellular Generations comparison table (very common interview question):**

| Generation | Key Capability | Typical Speed | Era |
|---|---|---|---|
| 1G | Analog voice | N/A (voice only) | 1980s |
| 2G | Digital voice + SMS | ~kbps | 1990s |
| 3G | Mobile Internet | ~Mbps | 2000s |
| 4G/LTE | Mobile Broadband, VoLTE | ~10s-100s Mbps | 2010s |
| 5G | Ultra-fast, low-latency, massive IoT | ~Gbps potential | 2019+ |

## 5. Handoff/Handover in Cellular Networks
- **Handoff (Handover):** The process of transferring an active call/data session from one cell tower to another, as a mobile device physically moves between coverage areas — done **seamlessly**, ideally without the user noticing any interruption.
- **Hard Handoff:** The connection to the OLD tower is broken **before** the connection to the NEW tower is established ("break-before-make") — brief interruption possible.
- **Soft Handoff:** The device maintains connections to **BOTH** the old and new towers **simultaneously** for a brief period, before fully dropping the old one ("make-before-break") — smoother, no interruption, but requires more complex coordination and temporarily more network resources.

## 6. Satellite Communication Basics

| Orbit Type | Altitude | Latency | Coverage per Satellite | Example Use |
|---|---|---|---|---|
| **GEO** (Geostationary) | ~35,786 km | High (~500-600ms round-trip) | Very large (fixed position relative to Earth) | Satellite TV, some older satellite Internet |
| **MEO** (Medium Earth Orbit) | ~2,000-35,786 km | Medium | Medium | GPS satellites |
| **LEO** (Low Earth Orbit) | ~160-2,000 km | Low (~20-40ms) | Small (satellites move relative to Earth, need many satellites for continuous coverage) | Modern satellite Internet (e.g., Starlink), needs large constellations |

> **Extra — Why LEO satellites need to work in large "constellations":** Because LEO satellites orbit much closer to Earth, they move relatively quickly across the sky (from a ground observer's perspective) and only cover a small geographic area at any given moment — providing continuous, uninterrupted service to a location requires MANY satellites working together in a coordinated constellation, so as one satellite moves out of range, another is already in position to seamlessly take over.

---

## Interview Questions & Answers

**Q1. What is the trade-off between the 2.4 GHz and 5 GHz WiFi frequency bands?**
2.4 GHz offers longer range and better penetration through walls/obstacles, but lower maximum speeds and more interference (since it's a more crowded band, shared with many other devices like Bluetooth and microwaves). 5 GHz offers significantly higher speeds and less interference (more available channels), but shorter range and worse obstacle penetration, making it best suited for high-speed use relatively close to the access point.

**Q2. What is the difference between a BSS and an ESS in WiFi architecture?**
A BSS (Basic Service Set) consists of a single Access Point and all the client devices connected to it. An ESS (Extended Service Set) consists of MULTIPLE BSSs (multiple Access Points), typically sharing the same network name (SSID), allowing devices to roam seamlessly between different Access Points as they physically move through a larger coverage area (like a big office building) without needing to manually reconnect.

**Q3. Why is WEP considered obsolete and insecure for WiFi security?**
WEP (Wired Equivalent Privacy) uses a fundamentally weak encryption implementation with known cryptographic vulnerabilities that allow attackers to crack the network's password/key relatively easily and quickly using widely available tools, making it essentially ineffective as a real security measure by modern standards — it has been superseded by WPA, and then WPA2/WPA3, which use much stronger, more secure encryption approaches.

**Q4. What is the key architectural difference between 4G/LTE and earlier cellular generations (like 3G)?**
4G/LTE introduced an all-IP network architecture, where even voice calls are carried as data packets (VoLTE — Voice over LTE), unlike earlier generations which maintained separate, distinct architectures for voice and data services. This unified, all-IP approach, combined with significantly improved speeds, enabled the modern app-based mobile experience with HD video streaming and responsive, data-intensive applications.

**Q5. What are the three key improvement areas that 5G specifically targets?**
Enhanced Mobile Broadband (eMBB) — extremely high peak speeds; Ultra-Reliable Low-Latency Communication (URLLC) — very low latency, critical for applications like autonomous vehicles or remote surgery; and Massive Machine-Type Communication (mMTC) — supporting a very high density of connected devices per area, essential for large-scale IoT deployments.

**Q6. Why does 5G use millimeter wave (mmWave) spectrum for its highest speeds, and what is the trade-off?**
mmWave spectrum offers a huge amount of available bandwidth, enabling 5G's highest peak speeds. The trade-off is that mmWave signals have very short range and very poor penetration through obstacles (even things like windows or foliage can significantly block them), requiring a much denser network of smaller, more closely-spaced "small cell" towers to provide consistent coverage, compared to the larger coverage area a single traditional cell tower using lower-frequency spectrum can provide.

**Q7. What is Handoff (Handover) in a cellular network, and why is it necessary?**
Handoff is the process of transferring an active call or data session from one cell tower to another, as a mobile device physically moves between the coverage areas of different towers. It's necessary to maintain continuous, uninterrupted service for a moving user — without handoff, a call or data session would simply drop whenever the user moved out of range of their originally-connected tower.

**Q8. What is the difference between a Hard Handoff and a Soft Handoff?**
A Hard Handoff follows a "break-before-make" approach — the connection to the OLD tower is fully disconnected BEFORE the connection to the NEW tower is established, potentially causing a brief, noticeable interruption. A Soft Handoff follows a "make-before-break" approach — the device maintains simultaneous connections to BOTH the old and new towers for a brief overlapping period, ensuring a smoother, generally imperceptible transition, though this requires more complex network coordination and temporarily consumes more network resources.

**Q9. What is the difference between GEO, MEO, and LEO satellite orbits, particularly in terms of latency?**
GEO (Geostationary) satellites orbit very far from Earth (~35,786 km), providing very large coverage per satellite but with HIGH latency (~500-600ms round-trip) due to the large physical distance signals must travel. MEO satellites orbit at a medium distance, offering medium latency and coverage (commonly used for GPS). LEO (Low Earth Orbit) satellites orbit much closer to Earth (~160-2,000 km), providing much LOWER latency (~20-40ms) due to the shorter distance, but cover a smaller area per satellite and require large, coordinated constellations of many satellites working together to provide continuous coverage to any given location.

**Q10. Why do modern satellite Internet services (like Starlink) use LEO satellites instead of traditional GEO satellites?**
LEO satellites offer dramatically lower latency (~20-40ms) compared to GEO satellites (~500-600ms), since they orbit much closer to Earth, resulting in a much shorter physical distance for signals to travel — this lower latency makes LEO-based satellite Internet far more suitable for latency-sensitive modern applications (like video calls, online gaming, or general responsive web browsing) compared to older GEO-based satellite Internet services, which suffered from very noticeable lag due to their much higher inherent latency.

**Q11. What does OFDMA (introduced in WiFi 6 / 802.11ax) improve compared to previous WiFi standards?**
OFDMA (Orthogonal Frequency-Division Multiple Access) allows a single WiFi channel to be efficiently divided and used by MULTIPLE devices simultaneously, rather than one device using the entire channel exclusively for each transmission (as in previous standards). This significantly improves network efficiency specifically in environments with MANY connected devices (like a busy office or public WiFi hotspot), reducing latency and improving overall throughput by allowing more devices to be served concurrently and efficiently.

**Q12. Why might a large, multi-floor office building need MULTIPLE Access Points configured as a single ESS, rather than just one very powerful Access Point?**
A single Access Point, regardless of its power/strength, has inherent physical range limitations and can be significantly degraded by obstacles like walls and floors in a large, multi-floor building. Using multiple Access Points, configured together as a single ESS (sharing the same SSID), allows for adequate coverage throughout the ENTIRE building, while also allowing users' devices to seamlessly ROAM between different Access Points as they physically move between different areas/floors, maintaining a continuous, uninterrupted connection to what appears to the user as a single, unified WiFi network throughout the whole building.
