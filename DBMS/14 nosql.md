# LEC-15: NoSQL

## 1. Introduction
- **NoSQL databases** (aka "not only SQL") are **non-tabular databases** and store data differently than relational tables.
- NoSQL databases come in a variety of types based on their data model. The main types are **document, key-value, wide-column, and graph**.
- They provide **flexible schemas** and **scale easily** with large amounts of data and high user loads.
- Key characteristics:
  1. They are **schema-free**.
  2. Data structures used are **not tabular** — more flexible, with the ability to adjust dynamically.
  3. Can handle **huge amounts of data** (big data).
  4. Most NoSQL DBs are **open source** and have the capability of **horizontal scaling**.
  5. It just stores data in some format **other than relational**.

## 2. History Behind NoSQL
1. NoSQL databases emerged in the **late 2000s** as the cost of storage dramatically decreased. Gone were the days of needing to create a complex, difficult-to-manage data model just to avoid data duplication. Developers (rather than storage) were becoming the primary cost of software development, so NoSQL databases optimised for **developer productivity**.
2. Data was becoming more **unstructured**, so structuring (defining schema in advance) was becoming costly.
3. NoSQL databases allow developers to store huge amounts of unstructured data, giving them a lot of **flexibility**.
4. Recognising the need to rapidly adapt to changing requirements, developers needed the ability to **iterate quickly** and make changes throughout the software stack — all the way down to the database. NoSQL gave them this flexibility.
5. **Cloud computing** also rose in popularity, and developers began using public clouds to host applications and data. They wanted the ability to distribute data across multiple servers and regions for resilience, to **scale out** instead of scale up, and to intelligently geo-place data. Some NoSQL DBs like MongoDB provide these capabilities.

## 3. NoSQL Databases — Advantages

### A. Flexible Schema
- RDBMS has a pre-defined schema, which becomes an issue when we don't have all the data with us, or we need to change the schema — it's a huge task to change schema on the go in RDBMS.

### B. Horizontal Scaling
- Horizontal scaling (**scale-out**) means bringing on additional nodes to share the load.
- This is **difficult with relational databases** due to the difficulty in spreading out related data across nodes.
- With non-relational databases, this is made simpler since collections are self-contained and not coupled relationally — allowing them to be distributed across nodes more simply, since queries don't have to "join" them together across nodes.
- Scaling horizontally is achieved through **Sharding** OR **Replica-sets**.

### C. High Availability
- NoSQL databases are highly available due to their **auto-replication** feature — whenever any kind of failure happens, data replicates itself to the preceding consistent state.
- If a server fails, we can access that data from another server as well, since in NoSQL, data is stored at multiple servers.

### D. Easy Insert and Read Operations
- Queries in NoSQL databases can be **faster** than SQL databases. Why? Data in SQL databases is typically normalised, so queries for a single object/entity require joining data from multiple tables — as tables grow, joins become expensive.
- However, data in NoSQL is typically stored in a way that is **optimised for queries**. The rule of thumb when using MongoDB is: **data that is accessed together should be stored together**. Queries typically do not require joins, so they are very fast.
- **But difficult delete or update operations.**

### E. Caching Mechanism
- NoSQL DBs often provide built-in caching mechanisms for performance.

### F. NoSQL use case
- More suited for **Cloud** applications.

## 4. When to Use NoSQL?
1. Fast-paced **Agile development**.
2. Storage of **structured and semi-structured** data.
3. **Huge volumes** of data.
4. Requirements for **scale-out architecture**.
5. Modern application paradigms like **micro-services** and **real-time streaming**.

## 5. NoSQL DB Misconceptions

### a. "Relationship data is best suited for relational databases."
- A common misconception is that NoSQL/non-relational databases don't store relationship data well.
- NoSQL databases **can** store relationship data — they just store it **differently** than relational databases do.
- In fact, many find modelling relationship data in NoSQL databases to be **easier** than in relational databases, because related data doesn't have to be split between tables. NoSQL data models allow related data to be **nested within a single data structure**.

### b. "NoSQL databases don't support ACID transactions."
- Another common misconception. Some NoSQL databases like **MongoDB** do, in fact, support ACID transactions.

## 6. Types of NoSQL Data Models

### 1. Key-Value Stores
- The simplest type of NoSQL database. Every data element is stored as a **key-value pair** consisting of an attribute name (or "key") and a value.
- In a sense, it's like a relational database with only two columns: the key/attribute name (e.g., "state") and the value (e.g., "Alaska").
- **Use cases:** shopping carts, user preferences, user profiles.
- **Examples:** Oracle NoSQL, Amazon DynamoDB, Redis (MongoDB also supports key-value store patterns).
- A key-value database associates a value (which can be anything from a number/string to a complex object) with a key, used to keep track of the object. It's like a dictionary/array/map object as it exists in most programming paradigms, but stored persistently and managed by a DBMS.
- Key-value databases use compact, efficient index structures to quickly and reliably locate a value by its key, making them ideal for systems needing constant-time find/retrieve.
- **Optimal use-cases:**
  a) Real-time random data access — e.g., user session attributes in gaming or finance apps.
  b) Caching mechanism for frequently accessed data or configuration based on keys.
  c) Application designed on simple key-based queries.

### 2. Column-Oriented / Columnar / C-Store / Wide-Column
- Data is stored such that each row of a column is next to other rows from that **same column**.
- While a relational database stores data in rows and reads row by row, a column store organises data as a set of columns — so when running analytics on a small number of columns, you can read those columns directly without consuming memory with unwanted data.
- Columns are often of the same type and benefit from more efficient **compression**, making reads even faster.
- Columnar databases can quickly aggregate the value of a given column (e.g., adding up total sales for the year).
- **Use cases:** analytics.
- **Examples:** Cassandra, RedShift, Snowflake.

### 3. Document-Based Stores
- Stores data in **documents** similar to **JSON** (JavaScript Object Notation) objects.
- Each document contains pairs of fields and values. Values can be of variety of types: strings, numbers, booleans, arrays, or objects.
- **Use cases:** e-commerce platforms, trading platforms, mobile app development across industries.
- **Supports ACID properties**, hence suitable for transactions.
- **Examples:** MongoDB, CouchDB.

### 4. Graph-Based Stores
- Focuses on the **relationship** between data elements.
- Each element is stored as a **node** (such as a person in a social media graph). Connections between elements are called **links or relationships**.
- In a graph database, connections are **first-class elements** of the database, stored directly. In relational databases, links are implied, using data (foreign keys) to express relationships.
- A graph database is optimised to **capture and search connections** between data elements, overcoming the overhead associated with `JOIN`ing multiple tables in SQL.
- Very few real-world business systems can survive solely on graph queries — as a result, graph databases are usually run **alongside** other, more traditional databases.
- **Use cases:** fraud detection, social networks, knowledge graphs.
- **Examples:** Neo4j, Amazon Neptune.

## 7. NoSQL Databases — Disadvantages
1. **Data Redundancy:** Since data models in NoSQL DBs are typically optimised for queries rather than reducing duplication, NoSQL DBs can be larger than SQL DBs. Storage is cheap enough that this is often considered a minor drawback, and some NoSQL DBs support compression to reduce storage footprint.
2. **Update & Delete operations are costly.**
3. **All types of NoSQL data models don't fulfil all application needs:**
   - Depending on the NoSQL database type selected, you may not achieve all your use cases in a single database. E.g., graph databases are excellent for analysing relationships but may not provide range-query capability for everyday retrieval. When selecting a NoSQL database, consider your use cases and whether a general-purpose DB like MongoDB would be better.
4. **Doesn't support ACID properties in general** (though some, like MongoDB, do).
5. **Doesn't support data entry with consistency constraints** in general.

## 8. SQL vs NoSQL

| | SQL Databases | NoSQL Databases |
|---|---|---|
| **Data Storage Model** | Tables with fixed rows and columns | Document: JSON documents; Key-value: key-value pairs; Wide-column: tables with rows and dynamic columns; Graph: nodes and edges |
| **Development History** | Developed in the 1970s, focused on reducing data duplication | Developed in the late 2000s, focused on scaling and rapid application change driven by Agile/DevOps |
| **Examples** | Oracle, MySQL, Microsoft SQL Server, PostgreSQL | Document: MongoDB, CouchDB; Key-value: Redis, DynamoDB; Wide-column: Cassandra, HBase; Graph: Neo4j, Amazon Neptune |
| **Primary Purpose** | General Purpose | Document: general purpose; Key-value: large data, simple lookups; Wide-column: large data, predictable query patterns; Graph: analyzing/traversing connected data |
| **Schemas** | Fixed | Flexible |
| **Scaling** | Vertical (Scale-up) | Horizontal (Scale-out across commodity servers) |
| **ACID Properties** | Supported | Not supported in general, except in DBs like MongoDB |
| **JOINS** | Typically required | Typically not required |
| **Data-to-object mapping** | Requires object-relational mapping (ORM) | Many don't require ORMs; e.g., MongoDB documents map directly to data structures in most popular programming languages |

> **Extra — CAP Theorem preview (fully covered in LEC-20):** A key reason NoSQL databases prioritize availability/partition tolerance over strict consistency (in many cases) traces back to the **CAP theorem** — a distributed system can only guarantee 2 out of 3 of Consistency, Availability, and Partition Tolerance at once. This is why databases like Cassandra favor AP (Availability + Partition tolerance) while MongoDB is more CP (Consistency + Partition tolerance) oriented.

---

## Interview Questions & Answers

**Q1. What is a NoSQL database, and how does it differ from a relational (SQL) database?**
NoSQL ("not only SQL") databases are non-tabular databases that store data in formats other than fixed rows/columns — such as documents, key-value pairs, wide-columns, or graphs. Unlike SQL databases, which have fixed schemas and scale vertically, NoSQL databases typically offer flexible/schema-free structures and scale horizontally across many commodity servers, making them well-suited for large-scale, rapidly changing, or unstructured data.

**Q2. What are the four main types of NoSQL databases? Give an example and use case for each.**
Key-Value stores (e.g., Redis, DynamoDB) — used for caching, session data, simple lookups. Wide-Column stores (e.g., Cassandra, HBase) — used for analytics on large datasets. Document stores (e.g., MongoDB, CouchDB) — used for e-commerce, content management, flexible semi-structured data. Graph databases (e.g., Neo4j, Amazon Neptune) — used for fraud detection, social networks, recommendation engines.

**Q3. Why did NoSQL databases emerge, historically?**
They emerged in the late 2000s as storage costs dropped and developer productivity became the primary cost driver in software — teams needed to iterate quickly without being locked into rigid, pre-defined schemas. Growth in unstructured data, the rise of cloud computing (needing horizontal, geo-distributed scaling), and Agile/DevOps practices all pushed toward more flexible database systems than traditional RDBMSs could offer.

**Q4. Is it true that NoSQL databases can't model relationships well? Explain.**
No, this is a common misconception. NoSQL databases can absolutely store relationship data — they just do it differently, often by nesting related data within a single document/structure rather than splitting it across separate tables and joining them. Many developers actually find this easier to model than the normalized, multi-table relational approach, especially for hierarchical or embedded relationships.

**Q5. Do all NoSQL databases lack ACID transaction support?**
No — this is another common misconception. While many NoSQL databases traditionally favored eventual consistency over strict ACID guarantees (trading consistency for availability/scalability), some, like MongoDB, do support full ACID transactions, especially in more recent versions.

**Q6. What is Horizontal Scaling, and why is it easier with NoSQL databases than relational ones?**
Horizontal scaling (scale-out) means adding more servers/nodes to distribute the load, rather than upgrading a single server's capacity (vertical scaling). It's easier with NoSQL because its data collections are typically self-contained and not tightly coupled through relational joins, so data can be distributed across nodes (via sharding or replica-sets) without the complexity of maintaining cross-node relational integrity that plagues relational databases.

**Q7. Explain the "data accessed together should be stored together" principle in NoSQL (especially MongoDB).**
This principle means that instead of normalizing data across multiple tables (as in SQL) and joining them at query time, NoSQL databases (like MongoDB) encourage embedding/nesting related data within a single document, since that data is usually queried/read together. This avoids the need for expensive joins at query time, making reads significantly faster, at the cost of some data duplication and more complex updates.

**Q8. What are the main disadvantages of NoSQL databases?**
Higher data redundancy (since data models favor query performance over minimizing duplication), costly update and delete operations (especially when data is duplicated across documents), the fact that no single NoSQL type fits every use case (e.g., graph DBs aren't great for simple range queries), and generally weaker default support for ACID properties and consistency constraints compared to relational databases.

**Q9. When would you choose a NoSQL database over a traditional relational database?**
When you need fast-paced Agile development with evolving schemas, are storing large volumes of structured or semi-structured data, need horizontal scale-out architecture to handle huge traffic/data volumes, or are building modern application paradigms like microservices or real-time streaming systems where flexibility and scalability outweigh the need for strict relational consistency.

**Q10. What is a Document Store, and why does it support ACID transactions while many other NoSQL types don't emphasize it?**
A Document Store (like MongoDB) stores data as JSON-like documents containing fields and values, often used for semi-structured application data. Because a single document typically represents one complete logical entity/record, ACID guarantees can be more naturally and efficiently applied at the document level (and in modern MongoDB, even across multi-document transactions), unlike distributed wide-column or key-value stores which are architected primarily for massive horizontal scale and eventual consistency.

**Q11. Explain the difference between Column-Oriented (Wide-Column) stores and traditional row-oriented relational databases.**
Traditional relational databases store and read data row by row, which is efficient when you need most/all columns of a record. Column-oriented stores organize data by column instead, so when you only need to analyze a few specific columns across many records (like aggregating a "sales" column), you can read just that column's data directly — dramatically improving performance for analytical queries, and benefiting from better compression since column values tend to be more uniform in type.

**Q12. Why are Graph databases usually run alongside other databases rather than as the sole database for a system?**
Because most real-world applications need a mix of query patterns — Graph databases excel specifically at traversing and analyzing complex relationships (like "friends of friends" or fraud rings), but they aren't as efficient for other common needs like simple range queries, straightforward CRUD on flat records, or heavy analytical aggregation. So systems typically use a graph database for the relationship-heavy portion of their workload, alongside a more general-purpose database (relational or document) for everything else.
