# LEC-17: Clustering in DBMS

## 1. Introduction
- **Database Clustering** (making **Replica-sets**) is the process of combining **more than one server or instance** connecting to a **single database**.
- Sometimes one server may **not be adequate** to manage the amount of data or the number of requests — that is when a **Data Cluster** is needed.
- Database clustering, SQL server clustering, and SQL clustering are closely associated, since SQL is the language used to manage database information.
- Essentially: **Replicate the same dataset on different servers.**

## 2. Advantages of Clustering

### a. Data Redundancy
- Clustering of databases helps with data redundancy, as we store the **same data at multiple servers**.
- **Don't confuse this** data redundancy (repetition) with the *bad* kind of redundancy that leads to anomalies (as discussed in Normalisation). The redundancy clustering offers is **required and intentional**, and is kept consistent through **synchronisation**.
- In case any of the servers fails due to any possible reason, the data is still available at other servers to access.

### b. Load Balancing
- Load balancing (or the resulting scalability) doesn't come by default with the database — it has to be brought in by clustering regularly, and also depends on the setup.
- Load balancing **allocates the workload** among the different servers that are part of the cluster.
- This means more users can be supported, and if there's a huge spike in traffic, there's a higher assurance the system will be able to support the new traffic — **one machine won't get hit with everything**.
- This provides **scaling seamlessly** as required, and **links directly to high availability**.
- Without load balancing, a particular machine could get overworked and traffic would slow down, leading to a decrement of traffic to **zero** (system going down).

### c. High Availability
- When you can access a database, it implies that it is **available**. High availability refers to the amount of time a database is considered available.
- The amount of availability you need greatly depends on the number of transactions you're running on your database, and how often you're running analytics on your data.
- With database clustering, we can reach extremely high levels of availability, due to load balancing and having extra machines. In case a server shuts down, the database will still be available (via the remaining servers).

## 3. How does Clustering Work?
- In cluster architecture, all requests are **split across many computers**, so that an individual user request is executed and produced by a **number of computer systems** (rather than just one).
- The clustering is serviceable definitely by the ability of **load balancing** and **high-availability**.
- If one node **collapses**, the request is handled by **another node**. Consequently, there are **few or no possibilities** of absolute system failures.

> **Extra — Clustering vs Replication vs Sharding (frequently confused, worth clarifying for interviews):**

| Concept | What it does | Primary goal |
|---|---|---|
| **Clustering** | Combines multiple servers/instances to work together, connecting to what functions as a single logical database (often via replica-sets) | High availability, load balancing, fault tolerance |
| **Replication** (the mechanism *behind* clustering) | Copies the same dataset across multiple servers | Data redundancy, availability, disaster recovery |
| **Sharding** (covered in LEC-18) | Splits (partitions) *different* portions of data across multiple servers, rather than copying the same data | Horizontal scalability for large datasets/high write throughput |

> **Extra — Types of clustering setups (common follow-up interview topic):**
> - **Active-Passive Clustering:** One node actively handles all requests (primary), while one or more standby (passive) nodes stay synced and ready to take over if the primary fails. Simpler, but the passive nodes sit idle most of the time.
> - **Active-Active Clustering:** Multiple nodes actively handle requests simultaneously (similar in spirit to the Multi-Primary Replication pattern covered in LEC-19), improving resource utilization and load distribution, but requiring more complex conflict-resolution/synchronization logic.

---

## Interview Questions & Answers

**Q1. What is Database Clustering, and why is it needed?**
Database Clustering is the process of combining multiple servers/instances to connect to what functions as a single logical database, typically by replicating the same dataset across those servers. It's needed when a single server becomes inadequate to handle the volume of data or number of requests, providing better performance, availability, and fault tolerance than a standalone single-server setup.

**Q2. What are the three main advantages of Database Clustering?**
Data Redundancy (the same data is available on multiple servers, so a single server failure doesn't cause data loss/unavailability), Load Balancing (workload is distributed across multiple servers, allowing the system to handle more users/traffic), and High Availability (the overall system remains accessible even if individual servers fail, since other servers in the cluster can continue serving requests).

**Q3. Isn't "data redundancy" supposed to be a bad thing in database design? How is it different in the context of clustering?**
In the context of Normalisation, uncontrolled/unintentional data redundancy is bad because it can lead to update/insert/delete anomalies and data inconsistency. In the context of Clustering, redundancy is intentional and controlled — the same data is deliberately copied to multiple servers and kept synchronized, specifically to improve availability and fault tolerance, not as an accidental side-effect of poor design.

**Q4. Does load balancing come automatically with database clustering?**
No — load balancing (and the scalability it provides) doesn't come by default just from clustering servers together; it needs to be explicitly configured and depends on the specific setup used, typically involving a load balancer component that distributes incoming requests across the clustered servers.

**Q5. How does clustering achieve high availability?**
By having multiple servers hold copies of the same data (via replication) and distributing incoming requests among them (via load balancing), the failure of any single server doesn't bring down the whole system — remaining servers in the cluster continue to serve requests, keeping the database accessible.

**Q6. What happens when a node in a cluster fails, from the perspective of an incoming request?**
If one node collapses/fails, the incoming request is automatically redirected to and handled by another available node in the cluster, rather than failing outright — this greatly reduces the chances of the entire system experiencing downtime or complete failure.

**Q7. What is the difference between Clustering and Sharding?**
Clustering typically involves replicating the SAME data across multiple servers to improve availability and load handling for reads (and sometimes writes). Sharding, by contrast, involves splitting DIFFERENT portions of the data across multiple servers (each server holds only a subset of the total data), primarily to scale out storage capacity and write throughput for very large datasets.

**Q8. What is the difference between Active-Passive and Active-Active clustering?**
In Active-Passive clustering, only one node actively serves requests at a time while other nodes remain on standby, ready to take over if the active node fails — simple but leaves standby resources underutilized. In Active-Active clustering, multiple nodes simultaneously serve requests, better utilizing available resources and improving load distribution, but requiring more complex synchronization to avoid conflicts.

**Q9. Why might a system's traffic-handling capacity "decrement to zero" without proper load balancing, even in a clustered setup?**
If load balancing isn't properly implemented, incoming traffic might still get funneled disproportionately to a single machine within the cluster rather than being spread out — that machine can become overworked, slow down, and eventually fail or become unresponsive, effectively taking the system down despite having multiple servers available, since they weren't actually sharing the load.

**Q10. In what scenarios would a company decide it needs to implement database clustering?**
When a single database server can no longer efficiently handle the volume of data or the number of concurrent user requests (as seen in the Cab Booking App case study in LEC-19), when the business requires higher uptime/availability guarantees than a single point of failure can provide, or when the system needs to gracefully scale to accommodate growing traffic without major architectural rewrites.
