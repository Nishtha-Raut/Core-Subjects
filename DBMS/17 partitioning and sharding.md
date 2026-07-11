# LEC-18: Partitioning & Sharding in DBMS (DB Optimisation)

## 1. Introduction
- A **big problem** can be solved easily when it is chopped into several smaller sub-problems — that is exactly what the **partitioning** technique does.
- It divides a big database containing data metrics and indexes into **smaller and handy slices of data**, called **partitions**.
- The partitioned tables are directly used by SQL queries **without any alteration**.
- Once the database is partitioned, the DDL can easily work on the smaller partitioned slices, instead of handling the giant database altogether — this is how partitioning **cuts down the problems** in managing large database tables.

## 2. Partitioning
- **Partitioning** is the technique used to divide stored database objects into **separate servers**.
- Due to this, there's an increase in **performance** and **controllability** of the data. We can manage huge chunks of data optimally.
- When we **horizontally scale** our machines/servers, it can give us a challenging time dealing with relational databases, as it's quite tough to maintain the relations.
- But if we apply partitioning to a database that is already scaled out (i.e., equipped with multiple servers), we can partition our database among those servers and handle big data easily.

## 3. Vertical Partitioning
- Slicing a relation **vertically / column-wise**.
- Need to access **different servers** to get complete tuples (since different columns of the same row may live on different servers).

## 4. Horizontal Partitioning
- Slicing a relation **horizontally / row-wise**.
- **Independent chunks of data tuples** are stored in different servers (each server has all columns, but only a subset of the rows).

> **Extra — Vertical vs Horizontal Partitioning comparison:**

| Aspect | Vertical Partitioning | Horizontal Partitioning |
|---|---|---|
| Slicing direction | By columns | By rows |
| Each partition contains | Subset of columns, all rows (for those columns) | Subset of rows, all columns |
| Need to reconstruct a full row | Yes — may require joining across servers | No — a full row lives entirely on one server |
| Common real-world equivalent | Splitting rarely-used columns (like large BLOBs) into a separate table/server | Sharding (see below) |

## 5. When is Partitioning Applied?
1. The dataset has become **much too huge** that managing and dealing with it becomes a **tedious task**.
2. The number of requests is large enough that a single DB server's access is taking huge time, and hence the **system's response time becomes high**.

## 6. Advantages of Partitioning
1. **Parallelism**
2. **Availability**
3. **Performance**
4. **Manageability**
5. **Reduce Cost** — as scaling-up (vertical scaling) might be much costlier.

## 7. Distributed Database
- A **single logical database** that is, in fact, **spread across multiple locations (servers)** and logically interconnected by a network.
- This is the **product** of applying DB optimisation techniques like **Clustering, Partitioning, and Sharding**.
- Why is this needed? — Same reasons as listed under "When is Partitioning Applied?" (point 5 above): huge datasets and huge request volumes.

## 8. Sharding
- A technique to implement **Horizontal Partitioning**.
- The **fundamental idea** of sharding: instead of having all the data sit on one DB instance, we **split it up** and introduce a **Routing layer**, so that we can forward the request to the right instance(s) that actually contain the relevant data.

### Pros
1. **Scalability**
2. **Availability**

### Cons
1. **Complexity** — making partition mapping and the routing layer work correctly needs to be implemented within the system; **non-uniformity** (uneven data distribution) creates the necessity of **Re-Sharding** over time.
2. **Not well suited for Analytical-type queries**, as the data is spread across different DB instances (the **Scatter-Gather problem** — a single analytical query may need to hit many shards and combine results, which is expensive).

> **Extra — Common Sharding strategies (not in original notes but a very standard follow-up interview topic):**
> - **Range-based Sharding:** Data is split based on ranges of a shard key (e.g., user IDs 1-1000 on Shard A, 1001-2000 on Shard B). Simple, but can lead to uneven load if data/access isn't uniform across ranges (hotspotting).
> - **Hash-based Sharding:** A hash function is applied to the shard key to decide which shard a row goes to. Distributes data more evenly, but makes range queries harder (since sequential keys end up scattered across shards).
> - **Directory-based Sharding:** A lookup table (directory service) maps each shard key to its corresponding shard. Flexible, but the directory itself can become a bottleneck or single point of failure if not handled carefully.
> - **Geo-based (Location) Sharding:** Data is partitioned by geographic region, so that data is stored physically close to the users who most often access it (reduces latency) — this connects directly to "Data Centre Wise Partition," Pattern 7 in LEC-19.

---

## Interview Questions & Answers

**Q1. What is Partitioning in DBMS, and why is it used?**
Partitioning is the technique of dividing a large database (or a large table within it) into smaller, more manageable slices called partitions, which can be distributed across separate servers. It's used to improve performance, manageability, and availability when a dataset or request volume grows too large for a single server to handle efficiently.

**Q2. What is the difference between Vertical Partitioning and Horizontal Partitioning?**
Vertical Partitioning slices a table by columns — different columns of the same table are stored on different servers, meaning reconstructing a complete row may require accessing multiple servers. Horizontal Partitioning slices a table by rows — each server holds all columns but only a subset of the rows, so a complete row always lives entirely on one server.

**Q3. What is Sharding, and how does it relate to Horizontal Partitioning?**
Sharding is a specific technique used to implement Horizontal Partitioning — instead of storing all rows of a table on a single database instance, the data is split across multiple instances (shards), and a routing layer directs each request to the shard(s) that actually hold the relevant data.

**Q4. What are the main advantages of Sharding?**
Scalability (since data and load are distributed across multiple servers rather than one, the system can handle much larger datasets and higher request volumes) and Availability (if one shard's server has issues, only the data on that specific shard is affected, not the entire database).

**Q5. What are the main disadvantages/challenges of Sharding?**
Complexity — building and maintaining the routing/partition-mapping layer is non-trivial, and uneven data distribution (non-uniformity) across shards can require Re-Sharding over time to rebalance load. Additionally, Sharding is poorly suited for analytical queries that need to aggregate data across the entire dataset, since such queries must "scatter" the request to many shards and then "gather" and combine the results — an expensive operation known as the Scatter-Gather problem.

**Q6. What is the Scatter-Gather problem in the context of Sharding?**
It's the performance challenge that arises when an analytical query needs data spread across multiple shards — the query must be sent ("scattered") to all relevant shard servers, and then their partial results must be collected and combined ("gathered") back together, which is much more expensive and complex than querying a single, unsharded database.

**Q7. What is a Distributed Database, and how does it relate to Clustering, Partitioning, and Sharding?**
A Distributed Database is a single logical database whose actual data is physically spread across multiple servers/locations, interconnected via a network, while still appearing as one unified database to applications. It's essentially the end-result/product of applying DB optimisation techniques like Clustering (for availability/load balancing via replication), Partitioning (for splitting data by rows/columns), and Sharding (a specific horizontal partitioning implementation) together.

**Q8. What are the advantages of Partitioning in general?**
Parallelism (multiple partitions can be processed/queried simultaneously), Availability (a failure in one partition's server doesn't necessarily bring down the whole system), Performance (queries against a smaller partition are faster than scanning a giant unpartitioned table), Manageability (easier to maintain, back up, and administer smaller chunks of data), and Reduced Cost (horizontal scale-out via partitioning across commodity servers is often cheaper than continuously vertically scaling a single powerful machine).

**Q9. Compare Range-based Sharding and Hash-based Sharding.**
Range-based Sharding assigns data to shards based on ranges of the shard key value (e.g., IDs 1–1000 go to Shard A) — it's simple and preserves natural ordering (good for range queries), but can create "hotspots" if certain ranges are accessed much more frequently than others. Hash-based Sharding applies a hash function to the shard key to decide shard placement, distributing data more evenly across shards and avoiding hotspots, but it scatters naturally sequential/related data across different shards, making range queries much less efficient.

**Q10. Why is sharding described as "generally hard to apply," and what's the practical takeaway from that?**
Sharding is hard because it requires careful upfront design of the shard key/routing strategy, ongoing maintenance to handle data growth and rebalancing (re-sharding) as usage patterns evolve, and it fundamentally complicates certain operations (like cross-shard joins/transactions and analytical queries). The practical takeaway (echoed by the phrase "No Pain, No Gain" in LEC-19) is that sharding should generally be adopted only once simpler scaling techniques (vertical scaling, read replicas, caching, query optimisation) have been exhausted, since the operational complexity it introduces is substantial.

**Q11. Give a real-world example each of when you'd use Vertical Partitioning vs Horizontal Partitioning (Sharding).**
Vertical Partitioning example: separating a rarely-accessed, large `profile_picture_blob` column into its own table/server, so that queries needing only frequently-accessed columns (like name, email) don't have to load/scan that large data unnecessarily. Horizontal Partitioning (Sharding) example: a large e-commerce platform sharding its `Orders` table by `customer_region`, so orders from customers in different geographic regions are stored on different servers, distributing both storage and query load.

**Q12. Why does Partitioning matter more once a system is already horizontally scaled (has multiple servers)?**
Simply having multiple servers doesn't automatically distribute the actual data or workload efficiently — without partitioning, you might still be hitting a single server for all requests, defeating the purpose of having multiple machines. Partitioning is what actually determines HOW the data and requests get spread across those multiple servers, turning raw horizontal scale-out capacity into real, usable performance and availability gains.
