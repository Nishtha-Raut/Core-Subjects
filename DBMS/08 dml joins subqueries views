# LEC-9 (Part 2): DRL, Constraints, DML, Joins, Set Operations, Subqueries & Views

## 1. Data Retrieval Language (DRL)

- **Syntax:** `SELECT <set of column names> FROM <table_name>;`
- **Order of execution:** from **RIGHT to LEFT** (conceptually — FROM/WHERE evaluated before SELECT).

### Can we use SELECT without FROM?
- **Yes**, using **DUAL tables**.
- Dual tables are dummy tables created by MySQL, which help users perform certain obvious actions without referring to user-defined tables.
```sql
SELECT 55 + 11;
SELECT now();
SELECT ucase('hello');
```

### WHERE
- Reduces rows based on given conditions.
```sql
SELECT * FROM customer WHERE age > 18;
```

### BETWEEN
```sql
SELECT * FROM customer WHERE age BETWEEN 0 AND 100;
```
- `0` and `100` are **inclusive**.

### IN
- Reduces **OR** conditions.
```sql
SELECT * FROM officers WHERE officer_name IN ('Lakshay', 'Maharana Pratap', 'Deepika');
```

### AND / OR / NOT
```sql
WHERE cond1 AND cond2
WHERE cond1 OR cond2
WHERE col_name NOT IN (1,2,3,4);
```

### IS NULL
```sql
SELECT * FROM customer WHERE prime_status IS NULL;
```

### Pattern Searching / Wildcards (`%`, `_`)
- `%` — any number of characters, from 0 to n. Similar to `*` in regex.
- `_` — exactly one character.
```sql
SELECT * FROM customer WHERE name LIKE '%p_';
```

### ORDER BY
- Sorting the data retrieved.
```sql
SELECT * FROM customer ORDER BY name DESC;
```
- `DESC` = Descending, `ASC` = Ascending.

### GROUP BY
- Collects data from multiple records and groups the result by one or more columns.
```sql
SELECT c1, c2, c3 FROM sample_table WHERE cond GROUP BY c1, c2, c3;
```
- All column names mentioned after SELECT must be repeated in GROUP BY, for the query to execute successfully.
- Used with **aggregation functions**:
  - `COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`

### DISTINCT
- Finds distinct values in the table.
```sql
SELECT DISTINCT(col_name) FROM table_name;
-- GROUP BY can also achieve the same effect:
SELECT col_name FROM table GROUP BY col_name;
```
> SQL is smart enough to realise that if you use GROUP BY without any aggregation function, you effectively mean "DISTINCT".

### GROUP BY ... HAVING
- Out of the categories made by GROUP BY, filters to keep only the particular groups matching a condition.
- Similar to WHERE, but operates on **groups**.
```sql
SELECT COUNT(cust_id), country FROM customer GROUP BY country HAVING COUNT(cust_id) > 50;
```

### WHERE vs HAVING

| Aspect | WHERE | HAVING |
|---|---|---|
| Function | Filters rows from the table | Filters rows from the groups |
| Applied | Before GROUP BY | After GROUP BY |
| Requires GROUP BY? | No | Yes (if using HAVING, GROUP BY is necessary) |
| Usable with | SELECT, UPDATE, DELETE | Only with SELECT (used with GROUP BY) |

## 2. Constraints (DDL)

### a. Primary Key
- PK is **not null**, **unique**, and only **one per table**.
```sql
CREATE TABLE Customer (
    id INT PRIMARY KEY,
    branch_id INT,
    first_name CHAR(50),
    last_name CHAR(50),
    DOB DATE,
    gender CHAR(6)
);
-- OR, choose one of these ways:
CREATE TABLE Customer (
    id INT,
    ...
    PRIMARY KEY (id)
);
```

### b. Foreign Key
- FK refers to the PK of another table.
- Each relation can have any number of FKs.
```sql
CREATE TABLE ORDER (
    id INT PRIMARY KEY,
    delivery_date DATE,
    order_placed_date DATE,
    cust_id INT,
    FOREIGN KEY (cust_id) REFERENCES customer(id)
);
```

### c. UNIQUE
- Values must be unique, but **can be NULL**. A table can have multiple unique attributes.
```sql
CREATE TABLE customer (
    ...
    email VARCHAR(1024) UNIQUE,
    ...
);
```

### d. CHECK
```sql
CREATE TABLE customer (
    ...
    CONSTRAINT age_check CHECK (age > 12),
    ...
);
```
- Naming the constraint (`age_check`) is optional — MySQL auto-generates a name if omitted.

### e. DEFAULT
- Sets a default value for the column.
```sql
CREATE TABLE account (
    ...
    saving_rate DOUBLE NOT NULL DEFAULT 4.25,
    ...
);
```

> **Extra:** An attribute **can be both PK and FK** in the same table simultaneously — this is common in weak-entity tables and 1:1 relationship implementations.

### f. ALTER Operations

| Operation | Purpose | Syntax Example |
|---|---|---|
| **ADD** | Add new column | `ALTER TABLE customer ADD age INT NOT NULL;` |
| **MODIFY** | Change datatype of an attribute | `ALTER TABLE customer MODIFY name CHAR(1024);` |
| **CHANGE COLUMN** | Rename column name (and optionally type) | `ALTER TABLE customer CHANGE COLUMN name customer_name VARCHAR(1024);` |
| **DROP COLUMN** | Drop a column completely | `ALTER TABLE customer DROP COLUMN middle_name;` |
| **RENAME** | Rename table itself | `ALTER TABLE customer RENAME TO customer_details;` |

## 3. Data Manipulation Language (DML)

### INSERT
```sql
INSERT INTO table_name(col1, col2, col3) VALUES (v1, v2, v3), (val1, val2, val3);
```

### UPDATE
```sql
UPDATE table_name SET col1 = 1, col2 = 'abc' WHERE id = 1;

-- Update multiple rows, e.g.:
UPDATE student SET standard = standard + 1;
```

#### ON UPDATE CASCADE
- Can be added while creating a constraint. If the PK of one table (parent) is the FK of another table (child), and the parent's PK is updated, `ON UPDATE CASCADE` automatically updates the FK value in the child table too.

### DELETE
```sql
DELETE FROM table_name WHERE id = 1;
DELETE FROM table_name; -- all rows deleted
```

#### DELETE CASCADE (overcomes DELETE constraint of Referential constraints)
- Decides what happens to child rows when a parent row is deleted.
```sql
CREATE TABLE ORDER (
    order_id INT PRIMARY KEY,
    delivery_date DATE,
    cust_id INT,
    FOREIGN KEY(cust_id) REFERENCES customer(id) ON DELETE CASCADE
);
```

#### ON DELETE SET NULL (can FK have NULL values?)
```sql
CREATE TABLE ORDER (
    order_id INT PRIMARY KEY,
    delivery_date DATE,
    cust_id INT,
    FOREIGN KEY(cust_id) REFERENCES customer(id) ON DELETE SET NULL
);
```

### REPLACE
- Primarily used when a tuple may already be present in a table.
- Behaves like **UPDATE**: using REPLACE with a WHERE-equivalent match on PK replaces that row.
- Behaves like **INSERT**: if there's no duplicate data, a new tuple is inserted.
```sql
REPLACE INTO student (id, class) VALUES(4, 3);
REPLACE INTO table_name SET col1 = val1, col2 = val2;
```

> **Extra — REPLACE internal behavior:** REPLACE actually works by first attempting an INSERT; if it violates a UNIQUE or PRIMARY KEY constraint, MySQL deletes the conflicting old row and then inserts the new row. This means `AUTO_INCREMENT` values and any `ON DELETE` triggers/cascades on that row **will fire**, which can have side effects — an important nuance for interviews.

## 4. Joining Tables
- All RDBMS are relational in nature — we refer to other tables to get meaningful outcomes.
- FKs are used to reference other tables.

### INNER JOIN
- Returns a resultant table with matching values from both (or all) the tables.
```sql
SELECT column_list FROM table1
INNER JOIN table2 ON condition1
INNER JOIN table3 ON condition2
...;
```

### Alias in MySQL (AS)
- Gives a temporary name to a table or column, for the purpose of a particular query — like a nickname. Makes the query short and neat.
```sql
SELECT col_name AS alias_name FROM table_name;
SELECT col_name1, col_name2, ... FROM table_name AS alias_name;
```

### OUTER JOIN

**LEFT JOIN**
- Returns all data from the left table, plus matched data from the right table.
```sql
SELECT columns FROM table1 LEFT JOIN table2 ON join_condition;
```

**RIGHT JOIN**
- Returns all data from the right table, plus matched data from the left table.
```sql
SELECT columns FROM table1 RIGHT JOIN table2 ON join_cond;
```

**FULL JOIN**
- Returns all data when there's a match on either left or right table data.
- **Emulated** in MySQL (no native FULL JOIN) using LEFT JOIN UNION RIGHT JOIN.
```sql
SELECT columns FROM table1 AS t1 LEFT JOIN table2 AS t2 ON t1.id = t2.id
UNION
SELECT columns FROM table1 AS t1 RIGHT JOIN table2 AS t2 ON t1.id = t2.id;
```
- `UNION ALL` can also be used — this will duplicate values as well, whereas `UNION` gives only unique values.

### CROSS JOIN
- Returns all the **Cartesian products** of the data present in both tables. All possible variations are reflected in the output.
- Used **rarely** in practical purposes.
- E.g., Table-1 has 10 rows, Table-2 has 5 rows → resultant would have 50 rows.
```sql
SELECT column_lists FROM table1 CROSS JOIN table2;
```

### SELF JOIN
- Used to get output from a particular table when it is joined to itself. Used **very rarely**.
- Emulated using INNER JOIN.
```sql
SELECT columns FROM table AS t1 INNER JOIN table AS t2 ON t1.id = t2.id;
```

### Join Without Using JOIN Keywords
```sql
SELECT * FROM table1, table2 WHERE condition;
-- E.g.
SELECT artist_name, album_name, year_recorded
FROM artist, album
WHERE artist.id = album.artist_id;
```

## 5. Set Operations
- Used to combine multiple SELECT statements.
- **Always gives distinct rows** (except UNION ALL).

### JOIN vs SET Operations

| Aspect | JOIN | SET Operations |
|---|---|---|
| Combination type | Column-wise combination | Row-wise combination |
| Result | Resulting set from matching condition | Resulting set from two or more SELECT statements |
| Data types | Can be different across tables | Corresponding columns must have the same datatype |
| Row uniqueness | Can generate both distinct or duplicate rows | Generates distinct rows |
| Column count | May or may not be the same from each table | Must be the same from each table |
| Direction | Combines results horizontally | Combines results vertically |

### UNION
- Combines two or more SELECT statements.
```sql
SELECT * FROM table1
UNION
SELECT * FROM table2;
```
- Number of columns and their order must be the same for table1 and table2.

### INTERSECT
- Returns common values of the tables.
- **Emulated** in MySQL (no native INTERSECT).
```sql
SELECT DISTINCT column_list FROM table1 INNER JOIN table2 USING(join_cond);
```

### MINUS
- Returns the distinct rows from the first table that do **not** occur in the second table.
- **Emulated** in MySQL.
```sql
SELECT column_list FROM table1 LEFT JOIN table2 ON condition WHERE table2.column_name IS NULL;
-- E.g.
SELECT id FROM table1 LEFT JOIN table2 USING(id) WHERE table2.id IS NULL;
```

## 6. Subqueries
- The **outer query depends on the inner query**.
- An **alternative to joins**.
- Also called **nested queries**.
```sql
SELECT column_list(s) FROM table_name
WHERE column_name OPERATOR (SELECT column_list(s) FROM table_name [WHERE]);

-- E.g.
SELECT * FROM table1 WHERE col1 IN (SELECT col1 FROM table1);
```

### Subqueries exist mainly in 3 clauses:
1. Inside a **WHERE** clause.
2. Inside a **FROM** clause.
3. Inside a **SELECT** clause.

#### Subquery using FROM clause
```sql
SELECT MAX(rating) FROM (SELECT * FROM movie WHERE country = 'India') AS temp;
```

#### Subquery using SELECT
```sql
SELECT (SELECT column_list(s) FROM T_name WHERE condition), columnList(s)
FROM T2_name WHERE condition;
```

#### Derived Subquery
```sql
SELECT columnList(s) FROM (SELECT columnList(s) FROM table_name WHERE [condition]) AS new_table_name;
```

#### Co-related (Correlated) Sub-queries
- With a **normal nested subquery**, the inner SELECT runs first and executes **once**, returning values used by the main (outer) query.
- A **correlated subquery**, however, executes **once for each candidate row** considered by the outer query — the inner query is driven by the outer query.
```sql
SELECT column1, column2, ...
FROM table1 AS outer
WHERE column1 operator
    (SELECT column1, column2
     FROM table2
     WHERE expr1 = outer.expr2);
```

### JOIN vs SUBQUERY

| Aspect | JOINS | SUBQUERIES |
|---|---|---|
| Speed | Faster | Slower |
| Calculation burden | Maximises calculation burden on the DBMS | Keeps responsibility of calculation on the user |
| Complexity | Complex, difficult to understand and implement | Comparatively easy to understand and implement |
| Optimal use case selection | Difficult | Easy |

## 7. MySQL Views
- A **view** is a database object that has no values of its own. Its contents are based on the base table. It contains rows and columns similar to the real table.
- In MySQL, a View is a **virtual table** created by a query joining one or more tables. Operates similarly to the base table but doesn't hold any data of its own.
- The main difference: views are **definitions built on top of other tables** (or views). If the underlying table changes, the same changes are reflected in the View also.

```sql
CREATE VIEW view_name AS SELECT columns FROM tables [WHERE conditions];
ALTER VIEW view_name AS SELECT columns FROM table WHERE conditions;
DROP VIEW IF EXISTS view_name;

-- View using a JOIN clause
CREATE VIEW Trainer AS
SELECT c.course_name, c.trainer, t.email
FROM courses c, contact t
WHERE c.id = t.id;
```

> **Note:** We can also import/export table schema from files (.csv or .json).

---

## Interview Questions & Answers

**Q1. What is the difference between WHERE and HAVING clauses?**
WHERE filters individual rows *before* grouping occurs and cannot use aggregate functions. HAVING filters *groups* of rows created by GROUP BY, and is typically used with aggregate functions like COUNT() or SUM(). If you use HAVING, a GROUP BY clause is required.

**Q2. What is the difference between DELETE and DELETE CASCADE?**
Plain DELETE removes rows from a table; if that row is referenced by a foreign key in a child table, the operation will fail (or leave orphaned FK values) unless handled. DELETE CASCADE is a foreign-key constraint option that automatically deletes the corresponding child-table rows whenever the parent row they reference is deleted, preserving referential integrity automatically.

**Q3. Can a Foreign Key be NULL? What happens with ON DELETE SET NULL?**
Yes, a Foreign Key can be NULL by default (unless restricted with NOT NULL), meaning the relationship is optional. With `ON DELETE SET NULL`, if the referenced parent row is deleted, the FK column in the child table's row is automatically set to NULL instead of deleting the row or throwing an error.

**Q4. What is the difference between INNER JOIN and OUTER JOIN (LEFT/RIGHT)?**
INNER JOIN returns only the rows that have matching values in both tables. OUTER JOIN types return all rows from one or both tables regardless of a match: LEFT JOIN keeps all rows from the left table (NULLs for unmatched right-table columns), RIGHT JOIN keeps all rows from the right table, and FULL JOIN keeps all rows from both.

**Q5. How is FULL JOIN achieved in MySQL, since it has no native FULL JOIN keyword?**
It's emulated by combining a LEFT JOIN and a RIGHT JOIN between the same two tables using UNION: `SELECT ... FROM t1 LEFT JOIN t2 ... UNION SELECT ... FROM t1 RIGHT JOIN t2 ...`. UNION automatically removes duplicate rows that appear in both join results.

**Q6. What is a CROSS JOIN, and why is it used rarely in practice?**
A CROSS JOIN returns the Cartesian product of two tables — every row from table1 combined with every row from table2. It's used rarely because the result size grows multiplicatively (rows_t1 × rows_t2), which is usually not meaningful for typical business queries and can be extremely expensive for large tables.

**Q7. What is the difference between JOIN and Subquery? When would you use one over the other?**
JOINs combine data column-wise from multiple tables based on a matching condition and are generally faster since the DBMS optimizer handles them efficiently, but syntax can get complex for nested logic. Subqueries let you nest one query inside another, offering easier-to-read logic for certain problems (like filtering based on an aggregate from another table) but are typically slower since they may execute repeatedly (especially correlated subqueries).

**Q8. What is a correlated subquery? How is it different from a regular (nested) subquery?**
A regular subquery executes once, independently, and its result is used by the outer query. A correlated subquery references a column from the outer query, so it must be re-executed once for every row considered by the outer query — making it generally slower but able to express row-by-row comparisons that a simple nested subquery cannot.

**Q9. What is the difference between UNION and UNION ALL?**
UNION combines the results of two or more SELECT statements and removes duplicate rows, returning only distinct rows. UNION ALL combines the results without removing duplicates, which is faster since it skips the deduplication step.

**Q10. Why doesn't MySQL have native INTERSECT and MINUS operators, and how are they emulated?**
Older versions of MySQL did not implement these standard SQL set operators natively (though newer MySQL 8.0.31+ versions do support INTERSECT and EXCEPT). Traditionally, INTERSECT is emulated using an INNER JOIN with DISTINCT, and MINUS (also called EXCEPT) is emulated using a LEFT JOIN combined with a WHERE clause checking for NULL in the second table's matching column.

**Q11. What is a View in SQL, and what are its benefits?**
A View is a virtual table defined by a stored SELECT query — it doesn't store data itself but dynamically reflects the underlying base table(s) whenever queried. Benefits include simplifying complex queries (hiding joins/logic behind a simple SELECT), providing a security layer (exposing only specific columns/rows to certain users), and presenting a consistent interface even if the underlying schema changes.

**Q12. What is the difference between a View and a Table?**
A Table physically stores data on disk. A View does not store data itself — it's a saved query definition that dynamically generates its result set from the underlying table(s) each time it's queried, always reflecting the current state of that data.

**Q13. Explain the REPLACE statement and how it differs from a plain UPDATE.**
REPLACE attempts to INSERT a new row; if a row with the same PRIMARY KEY or UNIQUE key already exists, MySQL first DELETEs that conflicting row and then INSERTs the new one. Unlike UPDATE (which modifies specific columns of an existing row in place), REPLACE effectively removes and recreates the entire row — meaning AUTO_INCREMENT values can change and any ON DELETE triggers/cascades will fire.

**Q14. What is the ON UPDATE CASCADE constraint used for?**
It ensures that if the primary key value in a parent table is updated, all foreign key references to that value in child tables are automatically updated too, keeping referential integrity intact without requiring manual updates in every related table.

**Q15. Explain the three clauses where a subquery can be used, with a brief description of each.**
(1) In the WHERE clause — to filter rows based on a computed set of values (e.g., `WHERE col1 IN (SELECT ...)`). (2) In the FROM clause — to treat the subquery's result as a temporary derived table that the outer query then operates on. (3) In the SELECT clause — to compute a single scalar value per outer row, often used to pull in a related value from another table without a full join.
