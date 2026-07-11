# LEC-21: The Master-Slave Database Concept

## 1. Introduction
- **Master-Slave** is a general way to **optimise IO** in a system where the number of requests goes way too high, such that a single DB server is not able to handle it efficiently.
- It's a form of **Pattern 3 in LEC-19** (Database Scaling Pattern) — **Command Query Responsibility Segregation (CQRS)**.

## 2. How It Works
- The **true/latest data** is kept in the **Master DB**, thus **write operations are directed there**.
- **Reading operations** are done **only from slaves**.
- This architecture serves the purpose of safeguarding **site reliability, availability, reduced latency**, etc.
- If a site receives a lot of traffic and the only available database is one master, it will be **overloaded** with both reading and writing requests — making the entire system slow for everyone on the site.
- **DB replication** takes care of distributing data from the Master machine to Slave machines. This can be **synchronous or asynchronous**, depending upon the system's needs.

```
                        Replicate
Master Database  -------------------->  Slave Database (mongoDB)  <----Read----> App Service
 (PostgreSQL)     -------------------->  Slave Database (mongoDB)  <----Read----> App Service
       ^                                                                    |
       |___________________________Write__________________________________|
```

> **Extra — Synchronous vs Asynchronous Replication:**

| Aspect | Synchronous Replication | Asynchronous Replication |
|---|---|---|
| Write acknowledgment | Master waits for slave(s) to confirm the write before acknowledging success to the client | Master acknowledges the write immediately, then propagates to slaves in the background |
| Consistency | Stronger — slaves are always up to date with master at the time of ack | Weaker — brief replication lag possible; slaves may temporarily serve stale data |
| Performance/Latency | Slower writes (waits on network round-trip to slave) | Faster writes (doesn't wait for replication) |
| Risk on master failure | Minimal data loss (slave already has the data) | Possible data loss (if master fails before replicating latest writes) |
| Typical use case | Systems where consistency/durability outweighs write latency (e.g., financial systems) | Systems where speed/availability matters more, and brief staleness is acceptable (e.g., social feeds) |

> **Extra — Why "Master-Slave" connects directly to earlier topics:**
> - It's the real-world implementation of **CQRS** (LEC-19, Pattern 3) — separating command (write) responsibility from query (read) responsibility.
> - It directly relates to the **CP vs AP** trade-off from the **CAP Theorem** (LEC-20) — e.g., MongoDB's single-primary replica-set design (used as the Slave DB example in the diagram) is a CP-oriented system, where only the primary/master accepts writes to preserve consistency.
> - It's also a specific case of **Database Clustering** (LEC-17), since it involves multiple machines (via replication) forming what functions as a single logical database.

## 3. Advantages of Master-Slave Architecture
1. **Read scalability** — read traffic can be distributed across multiple slave nodes, since most applications are read-heavy.
2. **Reduced load on master** — the master only has to handle writes, freeing it up to process them faster.
3. **Improved availability/failover potential** — if configured properly, a slave can be promoted to master if the original master fails.
4. **Reduced latency** — slaves can be geographically distributed closer to users for faster reads (ties into Pattern 7 — Data Centre Wise Partition, LEC-19).

## 4. Limitations of Master-Slave Architecture
1. **Single point of failure for writes** — if the master goes down, no new writes can be accepted until a new master is elected/promoted (unless a more complex multi-primary setup, per LEC-19 Pattern 4, is used).
2. **Replication lag** — with asynchronous replication, slaves can temporarily serve stale data.
3. **Write scalability is not solved** — all writes still funnel through a single master, which can become a bottleneck as write volume grows (this is exactly the problem that led to Pattern 4: Multi-Primary Replication, in the LEC-19 case study).

---

## Interview Questions & Answers

**Q1. What is the Master-Slave database concept, and why is it used?**
Master-Slave is a database replication architecture where one server (the Master) handles all write operations and holds the authoritative/true copy of the data, while one or more Slave servers hold replicated copies and handle read operations. It's used to optimize I/O and improve performance when a single database server can no longer efficiently handle the combined volume of read and write requests.

**Q2. Why are write operations directed only to the Master, and read operations only to Slaves?**
Directing writes only to the Master ensures there's a single, authoritative source of truth for the data, avoiding write conflicts that would arise if multiple servers accepted writes independently. Directing reads to Slaves offloads read traffic (which is typically much higher volume than writes in most applications) away from the Master, freeing it up to process writes faster and improving overall system throughput.

**Q3. How does Master-Slave replication relate to CQRS?**
CQRS (Command Query Responsibility Segregation) is the broader architectural principle of separating write ("command") operations from read ("query") operations. Master-Slave replication is a direct, practical implementation of this principle at the database level — writes (commands) go to the Master, and reads (queries) go to the Slaves.

**Q4. What is the difference between synchronous and asynchronous replication in a Master-Slave setup?**
In synchronous replication, the Master waits for the Slave(s) to confirm they've received and applied a write before acknowledging success to the client — this gives stronger consistency but slower write performance. In asynchronous replication, the Master acknowledges the write immediately and replicates to Slaves in the background — this is faster for writes but introduces potential replication lag, where Slaves may briefly serve stale data.

**Q5. What is replication lag, and why does it matter?**
Replication lag is the delay between when a write is committed on the Master and when that same write is reflected on the Slave(s), typically occurring with asynchronous replication. It matters because during this lag window, a read from a Slave might return outdated data — which can cause user-facing inconsistencies (e.g., a user updates their profile, but on refresh, the read-replica still shows the old data).

**Q6. What is the main limitation of the basic Master-Slave architecture, and how is it typically addressed?**
The main limitation is that write scalability isn't solved — since all writes must go through a single Master, that Master can become a bottleneck as write volume grows, and it's also a single point of failure for writes. This is typically addressed by evolving to more advanced patterns like Multi-Primary Replication (where multiple nodes can accept writes) or Sharding (distributing different portions of data, including their writes, across multiple independent master nodes).

**Q7. What happens if the Master server fails in a Master-Slave setup?**
Since only the Master can accept write requests, if it fails, no new writes can be processed until a new Master is established — typically by promoting one of the existing Slave servers to become the new Master (a process often called "failover"). Read operations may still continue to work via the remaining Slaves during this time, but the system loses write capability temporarily.

**Q8. Why might a system choose asynchronous replication despite the risk of stale reads?**
Because asynchronous replication offers significantly better write performance and lower latency, since the Master doesn't have to wait for network round-trips to Slave servers before acknowledging a write. For many applications (e.g., social media feeds, content platforms) where briefly stale reads are an acceptable trade-off, this performance benefit outweighs the downside of temporary inconsistency.

**Q9. How does the Master-Slave concept relate to the CAP theorem?**
A Master-Slave setup with a single primary accepting writes (like MongoDB's replica-set design) is generally CP-oriented (Consistency + Partition Tolerance) — it prioritizes having one consistent source of truth for writes over allowing multiple nodes to accept conflicting writes during a network partition, potentially sacrificing some availability if the Master is unreachable and no failover has occurred yet.

**Q10. Give a real-world example of when a Master-Slave setup would be a good architectural choice.**
An application with a heavy read-to-write ratio, such as a news website or blogging platform, where content is written relatively infrequently (by a small number of authors) but read extremely frequently (by many visitors) — Master-Slave replication lets you scale out read capacity via multiple Slave replicas serving that heavy read traffic, while the Master handles the comparatively lighter write load efficiently.

**Q11. Why does the diagram in the notes show the Master DB as PostgreSQL and Slave DBs as MongoDB — is this a strict requirement of Master-Slave architecture?**
No, this isn't a strict requirement — Master-Slave replication is typically implemented using the SAME database technology for both Master and Slaves (e.g., MySQL master with MySQL replicas), since replication protocols are usually engine-specific. The mixed diagram in the notes is more of an illustrative/conceptual representation of the general master-slave read/write split pattern rather than a literal recommendation to mix different database engines between master and slave roles.

**Q12. How does promoting a Slave to Master (failover) affect the overall Master-Slave topology?**
When a Slave is promoted to become the new Master (typically after the original Master fails), the system's write traffic is redirected to this newly promoted node, and the remaining Slaves are reconfigured to replicate from it instead of the old Master. This process needs careful coordination (often handled by orchestration tools or the database's built-in replica-set management, like MongoDB's automatic primary election) to avoid data loss or conflicting writes during the transition.
