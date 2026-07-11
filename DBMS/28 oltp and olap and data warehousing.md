# LEC-29: OLTP vs OLAP & Data Warehousing Basics

*(Not present in original notes — added as a commonly-asked "good to know" topic that ties together Normalisation, NoSQL, and the general purpose of different database architectures.)*

## 1. OLTP (Online Transaction Processing)
- Systems designed to manage **day-to-day transactional operations** — many small, fast read/write operations.
- **Examples:** e-commerce checkout, banking transactions, booking systems (like the Cab Booking App from LEC-19).
- **Characteristics:**
  - **Highly normalized** schema (to minimize redundancy, support fast, consistent writes).
  - Optimized for **INSERT/UPDATE/DELETE** operations on individual records.
  - Queries typically touch a **small number of rows** (e.g., "get this specific order").
  - Requires **strong consistency/ACID** guarantees.

## 2. OLAP (Online Analytical Processing)
- Systems designed for **complex analytical queries and reporting** — fewer, but much larger and more complex read operations.
- **Examples:** business intelligence dashboards, sales trend analysis, data warehouses.
- **Characteristics:**
  - Often **denormalized** schema (fewer joins needed, optimized for read/aggregation speed).
  - Optimized for **SELECT with heavy aggregation** (`SUM`, `AVG`, `GROUP BY` across millions of rows).
  - Queries typically scan/aggregate **large volumes of historical data**.
  - Can tolerate **slightly stale data** (data is often refreshed periodically via ETL, not real-time).

## 3. OLTP vs OLAP Comparison

| Aspect | OLTP | OLAP |
|---|---|---|
| Purpose | Day-to-day transaction processing | Analysis, reporting, decision-making |
| Data | Current, detailed, operational data | Historical, aggregated, summarized data |
| Schema design | Highly normalized (3NF/BCNF) | Denormalized (Star/Snowflake schema) |
| Query type | Simple, short (touch few rows) | Complex, long-running (touch many rows) |
| Operations | INSERT, UPDATE, DELETE heavy | SELECT/aggregation heavy |
| Users | Many concurrent users (customers, staff) | Fewer users (analysts, executives) |
| Response time expectation | Milliseconds | Seconds to minutes (acceptable) |
| Example systems | MySQL, PostgreSQL (transactional workloads) | Snowflake, Amazon Redshift, Google BigQuery |

> **Extra — Why they're architecturally different:** A single database optimized for OLTP (highly normalized, indexed for point lookups) is usually a poor fit for OLAP workloads (which need to scan/aggregate huge volumes of data, and benefit from denormalization to avoid expensive joins) — this is precisely why organizations typically maintain **separate systems**: an OLTP database for live application operations, and a separate OLAP system/data warehouse for analytics, connected via a periodic data-movement process (ETL).

## 4. ETL (Extract, Transform, Load)
- The process of moving data from OLTP (operational) systems into an OLAP (analytical) system/data warehouse.
1. **Extract:** Pull raw data from one or more OLTP source systems.
2. **Transform:** Clean, reformat, aggregate, and reshape the data to fit the analytical schema (e.g., converting normalized OLTP tables into a denormalized star schema).
3. **Load:** Write the transformed data into the OLAP data warehouse.
- Typically run as a **scheduled batch process** (e.g., nightly), meaning OLAP data is often **not real-time**.

## 5. Data Warehouse Schema Design

### Star Schema
- A **central Fact table** (containing measurable, quantitative data — e.g., `sales_amount`, `quantity`) connected directly to multiple **Dimension tables** (containing descriptive context — e.g., `Product`, `Customer`, `Time`, `Store`).
- Dimension tables are typically **denormalized** (not further broken down), giving the schema a "star" shape when diagrammed — the fact table in the center, with dimension tables radiating outward.

```
              Dim_Time
                 |
Dim_Product -- Fact_Sales -- Dim_Store
                 |
              Dim_Customer
```

### Snowflake Schema
- A variation of the star schema where **dimension tables are further normalized** into sub-dimension tables (e.g., `Dim_Product` splits into `Dim_Product` and `Dim_Category`).
- Reduces some redundancy (compared to Star schema) but requires **more joins**, making queries slightly more complex/slower than a pure Star schema.

> **Extra — Star vs Snowflake comparison:**

| Aspect | Star Schema | Snowflake Schema |
|---|---|---|
| Dimension tables | Denormalized (flat) | Normalized (split into sub-dimensions) |
| Query complexity | Simpler, fewer joins | More complex, more joins |
| Query performance | Generally faster | Generally slower (more joins) |
| Storage | More redundancy | Less redundancy |
| Best for | Simpler reporting needs, performance priority | Very large dimension tables where storage savings matter more |

## 6. Fact Table vs Dimension Table

| Aspect | Fact Table | Dimension Table |
|---|---|---|
| Contains | Quantitative, measurable data (metrics) | Descriptive, contextual attributes |
| Example columns | `sales_amount`, `quantity_sold`, `profit` | `product_name`, `category`, `customer_name`, `region` |
| Size | Very large (grows with every transaction/event) | Comparatively smaller, more static |
| Keys | Foreign keys referencing dimension tables + measures | Primary key + descriptive attributes |

---

## Interview Questions & Answers

**Q1. What is the difference between OLTP and OLAP systems?**
OLTP (Online Transaction Processing) systems handle day-to-day operational transactions — many small, fast reads/writes on current data, typically with a highly normalized schema for consistency. OLAP (Online Analytical Processing) systems handle complex analytical queries and reporting over large volumes of historical data, typically with a denormalized schema optimized for aggregation and read performance rather than transactional consistency.

**Q2. Why is an OLTP database schema usually normalized, while an OLAP schema is usually denormalized?**
OLTP schemas are normalized to minimize redundancy and ensure fast, consistent writes/updates to individual records, since transactional integrity (ACID) is critical. OLAP schemas are denormalized because analytical queries typically need to aggregate large volumes of data across many dimensions — denormalization avoids the overhead of numerous expensive joins during these large-scale read/aggregation operations, prioritizing query speed over write efficiency (since OLAP data is rarely updated directly; it's loaded periodically via ETL).

**Q3. What is ETL, and why is it needed?**
ETL (Extract, Transform, Load) is the process of extracting data from operational (OLTP) source systems, transforming it into a format suitable for analytical use (often reshaping normalized data into a denormalized star/snowflake schema), and loading it into a data warehouse (OLAP system). It's needed because OLTP and OLAP systems are typically architecturally different and separately optimized, so data must be explicitly moved and reshaped between them, rather than running analytics directly against the live transactional database.

**Q4. Why do organizations typically use separate systems for OLTP and OLAP, rather than one unified database?**
Because their workload characteristics and optimal architectures conflict — OLTP needs fast, small, consistent transactional operations on normalized data, while OLAP needs to scan and aggregate massive volumes of data efficiently, which benefits from denormalization. Running heavy analytical queries directly against a live OLTP database can also degrade performance for the actual transactional application, so separating them (connected via ETL) avoids this contention and allows each system to be optimized for its specific purpose.

**Q5. What is a Star Schema, and what are its main components?**
A Star Schema is a data warehouse design consisting of a central Fact table (holding quantitative, measurable data like sales amounts) connected to multiple surrounding Dimension tables (holding descriptive context, like Product, Customer, Time, Store) — visually resembling a star when diagrammed, with the fact table at the center.

**Q6. What is the difference between a Star Schema and a Snowflake Schema?**
In a Star Schema, dimension tables are kept denormalized (flat), resulting in simpler queries with fewer joins but some data redundancy. In a Snowflake Schema, dimension tables are further normalized into sub-dimension tables, reducing redundancy but requiring more joins to reconstruct full dimensional context, generally making queries somewhat more complex and slightly slower.

**Q7. What is the difference between a Fact table and a Dimension table?**
A Fact table contains quantitative, measurable data (metrics/measures, like sales amount or quantity sold) and typically grows very large over time as new transactions/events occur. A Dimension table contains descriptive, contextual attributes (like product name, customer details, region) that provide meaning to the facts, and is generally much smaller and more static in comparison.

**Q8. Why might OLAP systems tolerate "stale" data, while OLTP systems generally cannot?**
OLAP systems are used for analysis, trend-spotting, and decision-making, where having data that's a few hours or a day old (refreshed via scheduled ETL) is usually acceptable, since business decisions rarely depend on second-by-second freshness. OLTP systems, by contrast, directly support live operational processes (like processing a payment or checking real-time inventory), where stale or inconsistent data could lead directly to incorrect transactions, financial errors, or a broken user experience.

**Q9. How does the concept of OLTP vs OLAP relate to the SQL vs NoSQL discussion from earlier lectures?**
Traditional relational databases (SQL) are commonly used for OLTP workloads due to their strong ACID guarantees and normalized schema support for consistent transactional operations. Many specialized OLAP/data warehouse systems (like Snowflake, Amazon Redshift, Google BigQuery) use columnar storage models — conceptually related to the "Wide-Column" NoSQL data model discussed in LEC-15 — since columnar storage is particularly well-suited to the aggregation-heavy query patterns typical of OLAP workloads.

**Q10. If you were designing a system for a large e-commerce company, how would you architect the OLTP and OLAP components?**
The core e-commerce application (product catalog, checkout, order processing) would run on an OLTP database (e.g., MySQL/PostgreSQL) with a normalized schema optimized for fast, consistent transactional operations. Separately, a scheduled ETL pipeline would periodically extract data from this OLTP system, transform it into a denormalized Star (or Snowflake) schema, and load it into a dedicated OLAP data warehouse (e.g., Redshift/BigQuery/Snowflake), which business analysts and dashboards would query for reporting and business intelligence — keeping the two workloads cleanly separated so analytical queries never degrade the live application's performance.
