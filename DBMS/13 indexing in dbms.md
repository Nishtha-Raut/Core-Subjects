# LEC-14: Indexing in DBMS

## 1. Introduction
- **Indexing** is used to **optimise the performance** of a database by minimising the number of disk accesses required when a query is processed.
- The index is a type of **data structure**. It is used to **locate and access** the data in a database table quickly.
- Speeds up operations with **read** operations like `SELECT` queries, `WHERE` clauses, etc.
- **Search Key:** Contains a copy of the primary key or candidate key of the table, or something else.
- **Data Reference:** A pointer holding the address of the disk block where the value of the corresponding key is stored.
- Indexing is **optional**, but it increases access speed. It is **not the primary means** to access a tuple — it is a **secondary means**.
- **Index file is always sorted.**

```
Search Key | Data Reference
   (Key)   |     (Value)
```

## 2. Indexing Methods

### A. Primary Index (Clustering Index)
- A file may have several indices, on different search keys. If the data file containing the records is **sequentially ordered**, a **Primary index** is an index whose search key also defines the sequential order of the file.
- **NOTE:** The term "primary index" is sometimes used to mean an index on a primary key — however, such usage is **nonstandard and should be avoided**. (A primary/clustering index just means the data file is physically sorted by that search key — it may or may not be the primary key.)
- All files are ordered sequentially on some search key — it could be a Primary Key or a non-primary key.

#### Dense and Sparse Indices

**Dense Index**
- Contains an index record for **every** search key value in the data file.
- The index record contains the search-key value and a pointer to the **first** data record with that search-key value. The rest of the records with the same search-key value are stored sequentially after the first record.
- Needs **more space** to store the index record itself. Index records have the search key and a pointer to the actual record on disk.

**Sparse Index**
- An index record appears for **only some** of the search-key values.
- Helps resolve the storage-overhead issues of dense indexing. In this technique, a **range** of index columns stores the same data block address, and when data needs to be retrieved, the block address is fetched, then a scan happens within that block.

- **Primary Indexing** can be based on the data file being sorted w.r.t a **Primary Key attribute** or a **non-key attribute**.

#### Based on Key Attribute
- Data file is sorted w.r.t. the primary key attribute.
- PK is used as the search-key in the index.
- **Sparse Index** is formed — i.e., number of entries in the index file = number of **blocks** in the data file.

#### Based on Non-Key Attribute
- Data file is sorted w.r.t. a non-key attribute.
- Number of entries in the index = number of **unique** non-key attribute values in the data file.
- This is a **dense index**, as all the unique values have an entry in the index file.
- **Example:** A company recruited many employees across various departments. In this case, clustering indexing should be created for all employees who belong to the same department (i.e., index on `dept`, with data file physically sorted by department).

### B. Multi-level Index
- Index with **two or more levels**.
- If a single-level index becomes large enough that a binary search over it would itself take much time, we break down the indexing into **multiple levels** (an index over the index).

```
outer index --> index block 0 --> data block 0
            --> index block 1 --> data block 1
                (inner index)  --> ...
```

### C. Secondary Index (Non-Clustering Index)
- The **data file is unsorted** (with respect to the index's search key). Hence, **Primary Indexing is not possible** for this attribute.
- Can be done on a **key or non-key** attribute.
- Called "secondary" indexing because normally one (primary/clustering) indexing is already applied elsewhere.
- Number of entries in the index file = number of **records** in the data file.
- It's an example of a **Dense index** (must have an entry for every record, since the data isn't physically sorted by this key, so you can't infer ranges/blocks).

> **Extra — Primary/Clustering Index vs Secondary/Non-Clustering Index (comparison table, common interview ask):**

| Aspect | Primary (Clustering) Index | Secondary (Non-Clustering) Index |
|---|---|---|
| Data file order | Physically sorted by the index's search key | Data file is NOT sorted by this key |
| Density | Usually Sparse (can be dense too) | Always Dense |
| Number of indexes per table | Only **one** (since data can be physically sorted only one way) | Can have **multiple** |
| Speed | Generally faster for range queries | Slower for range queries (random access) |

## 3. Advantages of Indexing
1. **Faster access and retrieval** of data.
2. **IO is less.**

## 4. Limitations of Indexing
1. **Additional space** required to store the index table.
2. Indexing **decreases performance** in `INSERT`, `DELETE`, and `UPDATE` queries (since the index itself must also be updated on every write).

> **Extra — Why indexing hurts write performance:** Every INSERT/UPDATE/DELETE on an indexed column requires the DBMS to also update the index structure (commonly a B-Tree or B+Tree) to keep it consistent with the data — this adds overhead on every write operation. This is exactly why indexes should be added thoughtfully (typically on columns frequently used in WHERE/JOIN/ORDER BY), not on every column.

> **Extra — Common index data structures (not explicitly named in original notes but frequently asked):**
> - **B-Tree / B+Tree:** The most common index structure used by RDBMS (MySQL InnoDB, PostgreSQL, Oracle) — a balanced tree structure that keeps data sorted and allows O(log n) search, insert, delete, plus efficient range queries. B+Tree specifically stores all actual data/pointers only at leaf nodes (linked together), making range scans very efficient.
> - **Hash Index:** Uses a hash function to map search keys to bucket locations — extremely fast for exact-match (`=`) lookups but **not suitable for range queries** (`<`, `>`, `BETWEEN`), since hashing destroys ordering.

---

## Interview Questions & Answers

**Q1. What is Indexing in DBMS, and why is it used?**
Indexing is a data structure technique used to speed up data retrieval operations on a database table by minimizing the number of disk accesses needed to locate specific rows, especially for SELECT queries with WHERE clauses, joins, and sorting.

**Q2. What is the difference between a Dense Index and a Sparse Index?**
A Dense Index contains an index entry for every single search-key value in the data file, pointing to the first record with that value (and subsequent duplicates are stored sequentially after). A Sparse Index only contains index entries for some search-key values (typically one per data block), reducing index storage overhead at the cost of needing to scan within a block after locating the entry.

**Q3. What is the difference between Primary (Clustering) Index and Secondary (Non-Clustering) Index?**
A Primary/Clustering Index exists on a search key by which the actual data file is physically sorted — there can be only one such index per table since data can only be physically ordered one way. A Secondary/Non-Clustering Index exists on a search key that does NOT determine the physical order of the data file — a table can have multiple secondary indexes, but they must always be dense (an entry for every record), since there's no way to infer ranges from an unsorted data file.

**Q4. Why is a Sparse Index only possible with a Primary/Clustering Index and not with a Secondary Index?**
Because a Sparse Index relies on the data file being physically sorted by the search key — this way, one index entry per block (or range) is enough, since all records with nearby key values are guaranteed to be stored together in that block. Secondary indexes work on unsorted data (with respect to that key), so records with a given key value could be scattered anywhere in the file, making sparse indexing impossible — every record needs its own explicit index entry (dense).

**Q5. Is it accurate to say "primary index" always means "an index on the primary key"? Explain.**
No — this is a common misconception. "Primary index" (or clustering index) technically refers to an index whose search key determines the physical sorted order of the data file, which could be any attribute, not necessarily the primary key. Using "primary index" to specifically mean "index on the primary key" is considered non-standard usage and should be avoided in precise technical discussions.

**Q6. What is a Multi-level Index, and why is it needed?**
A Multi-level Index is an index built on top of another index (essentially an index of the index), used when a single-level index becomes so large that searching through it directly (even via binary search) becomes slow. By adding additional index levels (outer index, inner index, etc.), the search space at each level stays small, keeping lookup operations efficient even for very large tables.

**Q7. What are the main advantages and limitations of indexing?**
Advantages: faster data access/retrieval and reduced disk I/O for read operations. Limitations: indexes require additional storage space, and they slow down write operations (INSERT, UPDATE, DELETE), since the index structure itself must also be updated whenever the underlying data changes.

**Q8. Why does indexing decrease performance on INSERT, UPDATE, and DELETE operations?**
Because every time a row is inserted, updated, or deleted, the DBMS must also update the corresponding index structure(s) to keep them synchronized with the actual data — this adds extra work beyond just modifying the table itself, and the overhead increases with the number of indexes defined on that table.

**Q9. When would you choose NOT to add an index on a column?**
When the column is rarely used in WHERE clauses, JOINs, or ORDER BY (so the read benefit is minimal), when the table experiences very frequent writes (INSERT/UPDATE/DELETE) where index-maintenance overhead would outweigh read benefits, or when the column has very low cardinality (few distinct values, like a boolean flag), since the index wouldn't meaningfully narrow down search results.

**Q10. What is the difference between a Search Key and a Primary Key in the context of indexing?**
A Primary Key is a specific attribute (or set of attributes) chosen to uniquely identify each row in a relational table. A Search Key, in the context of indexing, is simply the attribute (or attributes) on which an index is built — it can be a copy of the primary key, a candidate key, or any other attribute entirely, depending on what queries need to be optimized.

**Q11. What data structures are typically used to implement indexes internally, and why?**
Most RDBMSs use B-Tree or B+Tree structures for indexes, because they keep data sorted and support efficient logarithmic-time search, insertion, and deletion, while also being excellent for range queries (since B+Tree leaf nodes are linked sequentially). Hash indexes are also used in some systems for very fast exact-match lookups, but they don't support range queries efficiently since hashing doesn't preserve key ordering.

**Q12. Why can a table have only one Primary/Clustering Index but multiple Secondary Indexes?**
Because the physical storage order of a data file can only be arranged in one way at a time — you can't sort the same physical file by two different columns simultaneously. A Primary/Clustering Index is tied directly to this physical order, so there can be only one. Secondary Indexes, however, are separate structures pointing back to the (unsorted, relative to them) data file, so you can create as many as needed on different columns.
