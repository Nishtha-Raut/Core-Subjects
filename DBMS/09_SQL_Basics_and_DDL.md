# LEC-9 (Part 1): SQL Basics, Data Types & DDL

## 1. What is SQL?
- **SQL:** Structured Query Language — used to access and manipulate data.
- SQL uses **CRUD** operations to communicate with the DB:
  1. **CREATE** — execute INSERT statements to insert a new tuple into the relation.
  2. **READ** — read data already in the relations.
  3. **UPDATE** — modify already-inserted data in the relation.
  4. **DELETE** — delete specific data point/tuple/row(s).
- **SQL is not a DB — it is a query language.**

## 2. What is RDBMS?
- **RDBMS** = Relational Database Management System.
- Software that enables us to implement a designed relational model.
- E.g., MySQL, MS SQL, Oracle, IBM, etc.
- **Table/Relation** is the simplest form of a data storage object in an R-DB.
- **MySQL** is an open-source RDBMS, and it uses SQL for all CRUD operations.
- MySQL uses a **client-server model**, where the client is a CLI or frontend that uses services provided by the MySQL server.

### Difference between SQL and MySQL
- **SQL** is a Structured Query Language used to perform CRUD operations in an R-DB.
- **MySQL** is an RDBMS used to store, manage, and administrate a DB (provided by itself), using SQL.

> **Extra:** This is a very common interview trick question — SQL is a *language* (a standard, ANSI/ISO defined), while MySQL is a *specific software product* (a database engine) that implements/uses SQL. Other RDBMS products (Oracle, PostgreSQL, SQL Server) also use SQL but with their own dialect variations.

## 3. SQL Data Types
*(Reference: w3schools.com/sql/sql_datatypes.asp)*

- In an SQL DB, data is stored in the form of tables.
- Data can be of different types, like INT, CHAR, etc.

| Datatype | Description |
|---|---|
| CHAR | string(0-255), fixed size = (0, 255], e.g., `CHAR(251)` |
| VARCHAR | string(0-255), variable length |
| TINYTEXT | String(0-255) |
| TEXT | string(0-65535) |
| BLOB | string(0-65535) — binary large object |
| MEDIUMTEXT | string(0-16,777,215) |
| MEDIUMBLOB | string(0-16,777,215) |
| LONGTEXT | string(0-4,294,967,295) |
| LONGBLOB | string(0-4,294,967,295) |
| TINYINT | integer(-128 to 127) |
| SMALLINT | integer(-32,768 to 32,767) |
| MEDIUMINT | integer(-8,388,608 to 8,388,607) |
| INT | integer(-2,147,483,648 to 2,147,483,647) |
| BIGINT | integer(-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807) |
| FLOAT | decimal with precision to 23 digits |
| DOUBLE | decimal with 24 to 53 digits |
| DECIMAL | double stored as a string (exact precision) |
| DATE | `YYYY-MM-DD` |
| DATETIME | `YYYY-MM-DD HH:MM:SS` |
| TIMESTAMP | `YYYYMMDDHHMMSS` |
| TIME | `HH:MM:SS` |
| ENUM | one of a set of preset values |
| SET | one or many of a set of preset values |
| BOOLEAN | 0/1 |
| BIT | e.g., `BIT(n)`, n up to 64 — stores values in bits |

### Notes on Data Types
- **Size ordering:** `TINY < SMALL < MEDIUM < INT < BIGINT`.
- **Variable-length data types** (e.g., VARCHAR) are better to use as they occupy space equal to the actual data size, rather than a fixed allocation.
- Numeric values can also be **unsigned**, e.g., `INT UNSIGNED` (doubles the positive range by removing the negative range).

> **Extra — CHAR vs VARCHAR (frequently asked):** `CHAR(n)` always uses exactly `n` bytes of storage (padded with spaces if the actual string is shorter), making it faster for fixed-length data (like a 2-letter state code). `VARCHAR(n)` only uses as much storage as the actual string length (plus 1-2 bytes overhead), making it more space-efficient for variable-length data (like names or emails), but with a tiny performance cost for length tracking.

> **Extra — DECIMAL vs FLOAT/DOUBLE:** Use `DECIMAL` for exact-precision values like currency/money (no rounding errors), since FLOAT/DOUBLE use approximate binary floating-point representation and can introduce small rounding errors — never use FLOAT/DOUBLE for financial calculations.

## 4. Types of SQL Commands

### a. DDL (Data Definition Language) — defining relation schema
1. **CREATE** — create table, DB, view.
2. **ALTER TABLE** — modify table structure, e.g., change column datatype or add/remove columns.
3. **DROP** — delete table, DB, view.
4. **TRUNCATE** — remove all the tuples from the table (structure remains).
5. **RENAME** — rename DB name, table name, column name, etc.

### b. DRL/DQL (Data Retrieval / Data Query Language) — retrieve data from tables
1. **SELECT**

### c. DML (Data Modification Language) — perform modifications in the DB
1. **INSERT** — insert data into a relation.
2. **UPDATE** — update relation data.
3. **DELETE** — delete row(s) from the relation.

### d. DCL (Data Control Language) — grant or revoke authorities from a user
1. **GRANT** — grant access privileges to the DB.
2. **REVOKE** — revoke user access privileges.

### e. TCL (Transaction Control Language) — manage transactions done in the DB
1. **START TRANSACTION** — begin a transaction.
2. **COMMIT** — apply all changes and end the transaction.
3. **ROLLBACK** — discard changes and end the transaction.
4. **SAVEPOINT** — checkpoint within a group of transactions, to which we can roll back.

> **Extra — Quick summary table:**

| Category | Full Form | Commands |
|---|---|---|
| DDL | Data Definition Language | CREATE, ALTER, DROP, TRUNCATE, RENAME |
| DQL/DRL | Data Query/Retrieval Language | SELECT |
| DML | Data Manipulation Language | INSERT, UPDATE, DELETE |
| DCL | Data Control Language | GRANT, REVOKE |
| TCL | Transaction Control Language | COMMIT, ROLLBACK, SAVEPOINT, START TRANSACTION |

## 5. Managing DB (DDL) — Common Commands

```sql
-- Creation of DB
CREATE DATABASE IF NOT EXISTS db_name;

-- Need to execute to choose which DB the subsequent commands (CREATE TABLE etc.) run on.
-- Makes switching between DBs possible.
USE db_name;

-- Dropping a database
DROP DATABASE IF EXISTS db_name;

-- List all the DBs in the server
SHOW DATABASES;

-- List tables in the selected DB
SHOW TABLES;
```

---

## Interview Questions & Answers

**Q1. Is SQL a database? Explain the difference between SQL and MySQL.**
No, SQL is not a database — it's a query language used to interact with relational databases. MySQL, on the other hand, is an actual RDBMS software product that implements SQL and provides the engine to store, manage, and administer databases using SQL commands.

**Q2. What are the four CRUD operations, and which SQL commands correspond to each?**
Create → INSERT, Read → SELECT, Update → UPDATE, Delete → DELETE. Together these form the core set of operations any database application performs on data.

**Q3. What is the difference between CHAR and VARCHAR?**
CHAR is a fixed-length string type — it always reserves the declared number of characters, padding with spaces if needed. VARCHAR is a variable-length string type — it only uses as much space as the actual data requires (plus small overhead), making it more storage-efficient for data of varying length.

**Q4. Why should you use DECIMAL instead of FLOAT/DOUBLE for storing currency values?**
DECIMAL stores exact numeric values without rounding errors, which is critical for financial calculations. FLOAT and DOUBLE use binary floating-point approximation, which can introduce small precision/rounding errors — unacceptable when dealing with money.

**Q5. What are the five main categories of SQL commands? Give one example of each.**
DDL (CREATE, ALTER, DROP) — defines schema. DQL/DRL (SELECT) — retrieves data. DML (INSERT, UPDATE, DELETE) — modifies data. DCL (GRANT, REVOKE) — controls access/permissions. TCL (COMMIT, ROLLBACK, SAVEPOINT) — manages transactions.

**Q6. What is the difference between DELETE, TRUNCATE, and DROP?**
DELETE (DML) removes specific rows based on a condition and can be rolled back; it's slower since it logs each row deletion. TRUNCATE (DDL) removes all rows from a table at once but keeps the table structure; it's faster and typically cannot be rolled back in most engines. DROP (DDL) removes the entire table (or database) structure along with its data completely.

**Q7. What does the USE statement do in MySQL?**
It selects a specific database as the "current" database in the session, so that subsequent SQL commands (like CREATE TABLE, SELECT, etc.) are executed against that database without needing to fully qualify table names every time.

**Q8. What is the purpose of the DDL command ALTER TABLE?**
It's used to modify the structure of an existing table — such as adding or dropping columns, changing a column's data type, renaming columns, or renaming the table itself — without needing to drop and recreate the table.

**Q9. What is an unsigned integer in SQL, and why would you use it?**
An unsigned integer (e.g., `INT UNSIGNED`) only stores non-negative values (0 and above), effectively doubling the maximum positive range compared to a signed integer of the same storage size. It's useful for columns that logically can never be negative, like an auto-increment ID or a count/quantity field.

**Q10. What are DCL and TCL commands used for, and how do they differ?**
DCL (Data Control Language) commands like GRANT and REVOKE manage user permissions/access rights on database objects. TCL (Transaction Control Language) commands like COMMIT, ROLLBACK, and SAVEPOINT manage the lifecycle of transactions — controlling when changes become permanent or are undone. DCL is about *who* can do what; TCL is about *how and when* changes take effect.

**Q11. What is the difference between a Query Language and a Data Manipulation Language in the context of SQL?**
In SQL, the Query Language (DQL/DRL) specifically refers to the SELECT statement used for retrieving data, while Data Manipulation Language (DML) refers to statements that modify data — INSERT, UPDATE, DELETE. Some classifications group SELECT under DML broadly, but the notes here treat DQL as its own distinct category since retrieval doesn't change the underlying data.

**Q12. Why is VARCHAR generally preferred over CHAR for storing names or emails?**
Because names and emails vary significantly in length between records, VARCHAR avoids wasting storage on padding for shorter values (unlike CHAR, which pads every value to a fixed length), making it more space-efficient for such variable-length text data.
