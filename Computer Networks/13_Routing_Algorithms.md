# 13: Routing Algorithms

## 1. What is Routing?
- **Routing** is the process by which a router determines the **best path** to forward a packet toward its destination network, using a **routing table**.
- Routing algorithms are the logic used to **build and update** these routing tables, especially in dynamic networks where topology/links can change.

## 2. Static vs Dynamic Routing

| Aspect | Static Routing | Dynamic Routing |
|---|---|---|
| Configuration | Manually configured by an administrator | Automatically learned/updated via routing protocols |
| Adaptability | Doesn't adapt to network changes automatically | Automatically adapts to topology changes (link failures, new routes) |
| Overhead | No extra bandwidth/CPU used for route calculation | Uses bandwidth/CPU for periodic updates and route computation |
| Best for | Small, stable networks | Large, complex, or frequently changing networks |

## 3. Categories of Dynamic Routing Algorithms

```
Dynamic Routing Algorithms
       |
       ├── Distance Vector   (RIP)
       ├── Link State        (OSPF)
       └── Path Vector       (BGP)
```

## 4. Distance Vector Routing

### Core Idea
- Each router maintains a table of **distances (costs)** to every known destination network, and periodically **shares its entire routing table** with its **directly connected neighbors** only.
- Based on the **Bellman-Ford algorithm**.
- Often summarized as: **"Routing by rumor"** — a router doesn't know the full network topology, it just trusts what its neighbors tell it about distances to other networks.

### How It Works
1. Each router initializes its table with distance = **0** to itself, and distance = **∞** (infinity) to all other networks (initially unknown).
2. Each router periodically sends its **entire routing table** to its **directly connected neighbors**.
3. Upon receiving a neighbor's table, a router checks: *"Is going through this neighbor a SHORTER path to any destination than what I currently have?"* If yes, it **updates** its own table accordingly.
4. This process repeats until the network reaches a stable state (**convergence**), where no further updates occur.

```
Router A ---(cost 1)--- Router B ---(cost 1)--- Router C

Router A's initial table: A=0, B=1 (direct), C=∞ (unknown)
After receiving B's table (which says B->C costs 1):
Router A updates: A->C = A->B(1) + B->C(1) = 2
```

### Problems with Distance Vector

**a. Count-to-Infinity Problem**
- If a link fails, incorrect/stale information can circulate between routers, each incrementally "believing" a slightly longer (but still seemingly valid) path exists via the other — the cost slowly increases ("counts up") toward infinity over many update cycles before the routers finally realize the destination is truly unreachable.

**b. Solutions to Count-to-Infinity**
- **Split Horizon:** A router does **not** advertise a route back to the same neighbor it originally learned that route from (prevents simple back-and-forth loops).
- **Route Poisoning:** When a route becomes unreachable, it's explicitly advertised with an **infinite cost** (rather than just being silently removed), quickly informing neighbors it's genuinely down.
- **Split Horizon with Poison Reverse:** Combines both — actively advertises the poisoned (infinite-cost) route back to the neighbor it came from, rather than just omitting it.
- **Hold-down Timers:** After a route is marked unreachable, the router ignores any new information about that route for a period, giving the network time to stabilize before accepting updates.

### RIP (Routing Information Protocol)
- A widely-known implementation of Distance Vector routing.
- Uses **hop count** as its metric (simply counts the number of routers/hops to a destination).
- **Maximum hop count = 15** — any route requiring 16 hops is considered **unreachable** ("infinity" in RIP's context), which is a deliberate design choice to keep the Count-to-Infinity problem bounded/small.
- Updates broadcast **every 30 seconds** by default.

## 5. Link State Routing

### Core Idea
- Each router builds a **complete map (topology) of the entire network**, then independently calculates the **shortest path** to every destination using **Dijkstra's algorithm**.
- Instead of sharing entire routing tables, each router shares information only about its **own direct links** (Link State Advertisements / LSAs) — but this information is **flooded to ALL routers** in the network (not just direct neighbors).

### How It Works
1. Each router discovers its **directly connected neighbors** and the cost to reach them.
2. Each router creates a **Link State Advertisement (LSA)** describing its direct links, and **floods** this LSA to **every other router** in the network.
3. Every router now has identical, complete information about the ENTIRE network topology (having received everyone else's LSAs too).
4. Each router independently runs **Dijkstra's Shortest Path algorithm** on this complete topology map to compute the best path to every destination.

```
     A --1-- B
     |       |
     4       2
     |       |
     C --1-- D

Every router floods its LSA (own direct links) to ALL other routers.
Each router ends up with the FULL map above, and independently
runs Dijkstra to compute shortest paths from itself to everywhere.
```

### OSPF (Open Shortest Path First)
- The most widely used Link State routing protocol.
- Uses **cost** (often based on link bandwidth) as its metric, rather than simple hop count.
- Converges **much faster** than RIP after a topology change, since routers have a complete map and can immediately recompute, rather than waiting for multiple rounds of neighbor-to-neighbor table exchanges.
- Supports **hierarchical design** using **Areas**, improving scalability for very large networks.

> **Extra — Distance Vector vs Link State comparison table (very common interview question):**

| Aspect | Distance Vector (e.g., RIP) | Link State (e.g., OSPF) |
|---|---|---|
| Information shared | Entire routing table | Only own direct link info (LSA) |
| Shared with | Direct neighbors only | Flooded to ALL routers in the network |
| Each router's view | Only knows distances, not full topology | Has a COMPLETE map of the network topology |
| Algorithm used | Bellman-Ford | Dijkstra's Shortest Path |
| Convergence speed | Slower | Faster |
| Scalability | Poor for large networks (count-to-infinity, slow convergence) | Better for large networks (with hierarchical Areas) |
| Metric | Typically hop count | Typically cost (bandwidth-based) |

## 6. Path Vector Routing — BGP (Border Gateway Protocol)
- Used for routing **between different Autonomous Systems (AS)** — i.e., between different organizations'/ISPs' networks — making it the protocol that **holds the entire global Internet together**.
- An **Autonomous System (AS)** is a large network (or group of networks) under a single administrative control (e.g., a large ISP, a big tech company's global network).
- Similar in spirit to Distance Vector, but instead of just tracking distance/cost, BGP tracks the **entire path (sequence of AS numbers)** a route has traversed — this allows BGP to easily detect and avoid **routing loops** (if a router sees its own AS number already in a proposed path, it rejects that path).
- BGP decisions are influenced heavily by **policy** (business/political agreements between ISPs), not purely by shortest-path/lowest-cost metrics like interior routing protocols.

> **Extra — IGP vs EGP (Interior vs Exterior Gateway Protocols) — a common follow-up distinction:**

| Aspect | IGP (Interior Gateway Protocol) | EGP (Exterior Gateway Protocol) |
|---|---|---|
| Used within | A single Autonomous System | Between different Autonomous Systems |
| Examples | RIP, OSPF | BGP |
| Optimization goal | Typically shortest path / lowest cost | Often policy-based (business agreements), not just shortest path |
| Scale | Smaller (single organization's network) | Massive (the entire global Internet) |

---

## Interview Questions & Answers

**Q1. What is the difference between Static and Dynamic Routing?**
Static Routing involves manually configuring routes by a network administrator, which don't automatically adapt if the network topology changes (e.g., a link fails) — suitable for small, stable networks. Dynamic Routing uses routing protocols that automatically discover and adapt routes based on real-time network conditions, using some bandwidth/CPU overhead for periodic updates and calculations, but scaling much better for large or frequently-changing networks.

**Q2. Explain the core idea behind Distance Vector routing.**
In Distance Vector routing, each router maintains a table of distances (costs) to every known destination, and periodically shares its ENTIRE routing table only with its directly connected neighbors. Routers update their own tables if a neighbor's advertised information suggests a shorter path exists through that neighbor — this is often described as "routing by rumor," since a router never sees the full network topology, only trusts what its immediate neighbors report.

**Q3. What is the Count-to-Infinity problem in Distance Vector routing, and why does it happen?**
Count-to-Infinity occurs when a link/route fails, but stale, incorrect information continues circulating between routers — each router incrementally believes a slightly longer (but seemingly still valid) alternate path exists via a neighbor, causing the "cost" to a now-unreachable destination to slowly increase over many update cycles before the routers finally recognize it's truly unreachable. It happens because Distance Vector routers only know DISTANCES reported by neighbors, not the actual paths/topology, so they can't easily detect that a neighbor's "alternate route" is actually looping back through themselves.

**Q4. What techniques are used to solve or mitigate the Count-to-Infinity problem?**
Split Horizon (a router doesn't advertise a route back to the same neighbor it learned that route from, preventing simple back-and-forth loops), Route Poisoning (explicitly advertising a failed route with an infinite cost, rather than silently removing it, to quickly inform neighbors), Split Horizon with Poison Reverse (combining both techniques), and Hold-down Timers (temporarily ignoring new updates about a recently-failed route, giving the network time to stabilize).

**Q5. Why does RIP limit its hop count to a maximum of 15?**
RIP deliberately limits hop count to 15 (treating 16 as "infinity"/unreachable) specifically to keep the impact of the Count-to-Infinity problem small and bounded — since RIP is a Distance Vector protocol susceptible to this issue, capping the maximum meaningful distance value ensures that even in a worst-case count-to-infinity scenario, the process resolves relatively quickly rather than counting up indefinitely.

**Q6. Explain the core idea behind Link State routing, and how it differs fundamentally from Distance Vector.**
In Link State routing, each router only shares information about its OWN direct links (via a Link State Advertisement, LSA), but this information is FLOODED to every other router in the entire network — meaning every router ends up with an identical, complete map of the whole network topology. Each router then independently runs Dijkstra's Shortest Path algorithm on this complete map to determine the best route to every destination. This differs fundamentally from Distance Vector, where routers only exchange summarized distance information with immediate neighbors and never see the actual, complete network topology.

**Q7. Why does Link State routing (like OSPF) typically converge faster than Distance Vector routing (like RIP) after a topology change?**
Because Link State routers have a COMPLETE map of the network topology at all times, when a change occurs (like a link failure), the affected router can quickly flood an updated LSA, and all routers can almost immediately recompute new shortest paths using Dijkstra's algorithm on their updated topology map. Distance Vector routers, by contrast, must wait for updated distance information to slowly propagate hop-by-hop between neighbors over multiple update cycles, which takes considerably longer to fully stabilize (converge) across the network.

**Q8. What algorithm does Distance Vector routing use, and what algorithm does Link State routing use?**
Distance Vector routing (like RIP) is based on the Bellman-Ford algorithm. Link State routing (like OSPF) uses Dijkstra's Shortest Path algorithm, run independently by each router once it has the complete network topology map.

**Q9. What is BGP, and why is it described as holding the global Internet together?**
BGP (Border Gateway Protocol) is the routing protocol used to route traffic BETWEEN different Autonomous Systems (large networks under separate administrative/organizational control, like different ISPs or major tech companies). It's described as holding the Internet together because it's the protocol responsible for exchanging routing information across the boundaries of all these separate, independently-operated networks, allowing traffic to successfully find its way across the vast, decentralized collection of networks that collectively make up the global Internet.

**Q10. What is the key difference between BGP (Path Vector) and simple Distance Vector routing?**
While BGP shares some conceptual similarity with Distance Vector (both are based on exchanging routing information with neighbors), BGP tracks the ENTIRE PATH (the specific sequence of Autonomous System numbers a route has traversed) rather than just a simple distance/cost metric. This allows BGP to easily detect and reject routing loops (if a router sees its own AS number already present in a proposed path, it knows accepting that path would create a loop, and rejects it) — something plain Distance Vector protocols struggle with, leading to issues like Count-to-Infinity.

**Q11. What is the difference between an Interior Gateway Protocol (IGP) and an Exterior Gateway Protocol (EGP)? Give an example of each.**
An IGP (like RIP or OSPF) is used for routing WITHIN a single Autonomous System (one organization's network), typically optimizing purely for shortest path or lowest cost. An EGP (like BGP) is used for routing BETWEEN different Autonomous Systems, and its routing decisions are often influenced heavily by business/policy agreements between the organizations involved, not purely by shortest-path metrics — reflecting the more complex, politically/commercially-influenced nature of inter-organizational Internet routing.

**Q12. Why might a large enterprise network use OSPF internally, but rely on BGP at its connection point(s) to the public Internet?**
OSPF (an IGP) is well-suited for efficiently routing traffic WITHIN the enterprise's own network, since it converges quickly and optimizes for genuinely shortest/lowest-cost paths, which is exactly what's needed for internal traffic under a single administrative authority. However, at the boundary where the enterprise's network connects to the broader public Internet (interacting with other organizations'/ISPs' separate Autonomous Systems), BGP is required, since routing decisions there must account for policy, peering agreements, and loop-prevention across multiple independently-operated networks — a fundamentally different problem than optimizing routes purely within one's own internal network.
