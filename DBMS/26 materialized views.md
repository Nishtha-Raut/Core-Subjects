# LEC-27: Materialized Views

*(Not present in original notes — added as a natural extension of LEC-9's Views coverage.)*

## 1. Recap: Regular (Standard) View
- From LEC-9: A regular **View** is a **virtual table** — it has **no data of its own**, and its query is **re-executed every single time** the view is queried, always reflecting the current, live state of the underlying base table(s).

## 2. What is a Materialized View?
- A **Materialized View** is a database object that, unlike a regular view, **physically stores (materializes) the query result** on disk, like a real table.
- It does **not** automatically reflect changes to the underlying base tables in real time — it must be **explicitly refreshed** (either manually or on a schedule) to pick up the latest data.

## 3. Materialized View vs Regular View

| Aspect | Regular View | Materialized View |
|---|---|---|
| Data storage | No data stored — purely virtual | Data is physically stored (materialized) |
| Query performance | Slower — re-executes underlying query every time | Faster — reads pre-computed, stored results |
| Data freshness | Always up-to-date (reflects live base table data) | Can be stale — only as fresh as the last refresh |
| Storage overhead | None (no extra storage) | Requires extra storage for the materialized data |
| Refresh needed? | No | Yes — manual or scheduled refresh required |
| Best for | Simplifying/hiding complex queries where live data is essential | Expensive aggregate queries/reports where slightly stale data is acceptable |

## 4. Why Use a Materialized View?
- For **expensive queries** (heavy joins, aggregations across huge tables) that are run **frequently** but don't need to reflect the absolute latest data every single time (e.g., a daily sales dashboard, a monthly report).
- Instead of re-computing the expensive query every time it's accessed, the result is **computed once and cached** as the materialized view — subsequent reads are much faster, since they just read the pre-computed table.

## 5. Refreshing a Materialized View
- **Manual refresh:** Explicitly triggered by the DBA/application, e.g.:
  ```sql
  -- PostgreSQL example
  REFRESH MATERIALIZED VIEW my_matview;
  ```
- **Scheduled refresh:** Set up to refresh automatically at intervals (e.g., nightly), often via a scheduled job/cron.
- **Refresh types (PostgreSQL-specific, worth knowing conceptually):**
  - **Complete refresh:** Recomputes the entire view from scratch.
  - **Incremental/Fast refresh:** Only applies the changes (deltas) since the last refresh, which is much faster but requires additional bookkeeping (like a log of changes on the base tables).

> **Extra — Note on MySQL:** MySQL does **not** natively support Materialized Views (unlike PostgreSQL and Oracle, which do). In MySQL, materialized-view-like behavior is typically emulated manually — by creating a real table and periodically populating it via a scheduled event or a stored procedure that runs the expensive query and writes results into that table.

## 6. Trade-offs Summary

| Factor | Regular View | Materialized View |
|---|---|---|
| Freshness vs Speed | Prioritizes freshness | Prioritizes speed |
| Use when... | Data must always be current | Slight staleness is acceptable, and read performance matters more |

---

## Interview Questions & Answers

**Q1. What is a Materialized View, and how is it different from a regular View?**
A Materialized View physically stores the result of its defining query on disk, like a real table, whereas a regular View is purely virtual and has no data of its own — it re-executes its underlying query every time it's accessed. This means a Materialized View offers much faster read performance (since it's just reading pre-computed data), but that data can become stale until the view is explicitly refreshed, unlike a regular view which is always up-to-date.

**Q2. Why would you use a Materialized View instead of just running the underlying query directly each time?**
When the underlying query is expensive (e.g., involves heavy joins or aggregations across large tables) and is run frequently, but doesn't need to reflect changes in real-time, a Materialized View lets you compute the expensive result once and cache it — subsequent reads become fast lookups against the pre-computed data instead of re-running the costly computation every single time.

**Q3. How does a Materialized View stay up to date with changes in the underlying base tables?**
It doesn't automatically stay up to date — it must be explicitly refreshed, either manually (via a command like `REFRESH MATERIALIZED VIEW`) or on a scheduled basis (e.g., a nightly cron job), to pull in the latest changes from the base tables and recompute/update the stored result.

**Q4. What is the difference between a Complete Refresh and an Incremental Refresh of a Materialized View?**
A Complete Refresh recomputes the entire materialized view from scratch by re-running its full defining query. An Incremental (Fast) Refresh only applies the specific changes (deltas) that have occurred in the base tables since the last refresh, which is significantly faster but requires additional bookkeeping (such as tracking changes via logs) to determine what has changed.

**Q5. Does MySQL natively support Materialized Views?**
No, MySQL does not natively support Materialized Views the way PostgreSQL or Oracle do. In MySQL, similar behavior is typically emulated manually by creating a regular table and periodically populating/refreshing it using a scheduled event or stored procedure that runs the expensive query and writes its results into that table.

**Q6. When would you prefer a regular View over a Materialized View?**
When the data must always reflect the absolute latest state of the underlying base tables (real-time accuracy is critical), or when the underlying query isn't expensive enough to justify the storage overhead and refresh-management complexity of materializing it — regular Views are simpler and always current, at the cost of needing to re-execute the query on every access.

**Q7. What is a real-world use case for a Materialized View?**
A dashboard showing aggregated sales/revenue metrics (e.g., total sales per product category per month) that gets checked frequently by many users throughout the day. Instead of recalculating these expensive aggregations from the raw transactional data on every single dashboard load, a materialized view can precompute and store these results, refreshing perhaps nightly or hourly, giving fast dashboard load times at the cost of the data being at most a few hours old.

**Q8. What are the storage implications of using Materialized Views?**
Unlike regular Views (which consume no additional storage since they're purely virtual), Materialized Views require additional disk space to physically store their computed result set — this overhead needs to be factored in, especially for large result sets or when maintaining many materialized views.

**Q9. Can a Materialized View become a performance bottleneck if not managed properly?**
Yes — if the refresh process itself is expensive (e.g., a Complete Refresh on a huge underlying dataset) and is scheduled too frequently, or runs during peak load times, it can create significant load on the database server. Poorly managed materialized view refreshes can end up trading one performance problem (slow reads) for another (expensive, poorly-timed refresh operations).

**Q10. How would you decide between using a Materialized View versus simply adding better indexes to speed up a slow query?**
If the query's slowness comes primarily from inefficient access paths (e.g., missing indexes causing full table scans) but the underlying computation itself isn't inherently expensive, adding the right indexes is usually the better, lighter-weight first step. A Materialized View is more appropriate when the query involves genuinely expensive computation (heavy aggregation, multiple large joins) that indexing alone can't sufficiently speed up, and when some data staleness is an acceptable trade-off for consistently fast reads.
