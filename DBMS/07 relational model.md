# LEC-7: Relational Model

## 1. Introduction
- Relational Model (RM) organises the data in the form of **relations (tables)**.
- A relational DB consists of a **collection of tables**, each of which is assigned a **unique name**.
- A **row** in a table represents a relationship among a set of values, and the table is a collection of such relationships.
- **Tuple:** A single row of the table representing a single data point / a unique record.
- **Columns:** Represent the attributes of the relation. For each attribute, there is a permitted set of values, called the **domain** of the attribute.
- **Relation Schema:** Defines the design and structure of the relation — contains the name of the relation and all the columns/attributes.
- Common RM-based DBMS systems, aka **RDBMS**: Oracle, IBM, **MySQL**, MS Access.
- **Degree of table:** Number of attributes/columns in a given table/relation.
- **Cardinality:** Total number of tuples in a given relation.
- **Relational Key:** A set of attributes which can uniquely identify each tuple.

## 2. Important Properties of a Table in Relational Model
1. The name of a relation is distinct among all other relations.
2. The values have to be **atomic** — can't be broken down further.
3. The name of each attribute/column must be unique.
4. Each tuple must be **unique** in a table.
5. The sequence of rows and columns has **no significance**.
6. Tables must follow **integrity constraints** — this helps maintain data consistency across the tables.

## 3. Relational Model Keys

| Key | Description |
|---|---|
| **Super Key (SK)** | Any set (permutation & combination) of attributes present in a table which can uniquely identify each tuple. |
| **Candidate Key (CK)** | Minimum subset of super keys which can uniquely identify each tuple. Contains no redundant attribute. CK value shouldn't be NULL. |
| **Primary Key (PK)** | Selected out of the CK set, has the least number of attributes. |
| **Alternate Key (AK)** | All Candidate Keys except the one chosen as PK. |
| **Foreign Key (FK)** | Creates a relation between two tables. |
| **Composite Key** | PK formed using **at least 2** attributes. |
| **Compound Key** | PK which is formed using **2 Foreign Keys**. |
| **Surrogate Key** | Synthetic PK, generated automatically by the DB, usually an integer value. May be used as PK. |

### Foreign Key (Detailed)
- A relation, say `r1`, may include among its attributes the PK of another relation, say `r2`. This attribute is called the **FK** from `r1` referencing `r2`.
- `r1` is aka the **Referencing (Child)** relation of the FK dependency, and `r2` is called the **Referenced (Parent)** relation of the FK.
- FK helps to **cross-reference** between two different relations.

> **Extra — Super Key vs Candidate Key (clarified):** Every Candidate Key is a Super Key, but not every Super Key is a Candidate Key — a Super Key can have extra, unnecessary attributes. A Candidate Key is the *minimal* Super Key (no attribute can be removed without losing uniqueness).

> **Extra — Composite vs Compound Key:** A Composite Key just means the PK consists of 2+ attributes (they don't have to be FKs). A Compound Key is a specific type of composite key where the attributes involved are themselves Foreign Keys referencing other tables (common in junction/bridge tables for many-to-many relationships, e.g., `Enrollment(student_id, course_id)`).

## 4. Integrity Constraints
- CRUD operations must be done with some **integrity policy** so that the DB is always consistent.
- Introduced so that we do not accidentally corrupt the DB.

### a. Domain Constraints
- Restricts the value in the attribute of a relation — specifies the **Domain**.
- Restricts the **data types** of every attribute.
- E.g., we want to specify that the enrolment should happen only for candidates with birth year < 2002.

### b. Entity Constraints
- Every relation should have a **PK**. **PK != NULL**.

### c. Referential Constraints
- Specified between two relations & helps maintain consistency among tuples of two relations.
- It requires that the value appearing in specified attributes of any tuple in the **referencing** relation must also appear in the specified attributes of at least one tuple in the **referenced** relation.
- If an FK in the referencing table refers to a PK of the referenced table, then every value of the FK in the referencing table must be **NULL** or **available** in the referenced table.
- FK must have the matching PK for each of its values in the parent table, or it must be NULL.

### d. Key Constraints
The six types of key constraints present in a DBMS:
1. **NOT NULL:** Restricts the user from having a NULL value. Ensures every element in the DB has a value.
2. **UNIQUE:** Ensures all the values in a column are different from each other.
3. **DEFAULT:** Sets the default value for a column when no value is specified.
4. **CHECK:** One of the integrity constraints in DBMS — ensures integrity of data is maintained before and after the completion of CRUD operations.
5. **PRIMARY KEY:** An attribute or set of attributes that can uniquely identify each entity in the entity set. Must contain unique as well as not-null values.
6. **FOREIGN KEY:** Whenever there's a relationship between two entities, there must be some common attribute between them — this common attribute is the PK of one entity set, and becomes the FK of the other entity set. Prevents actions that would result in a loss of connection between tables.

---

## Interview Questions & Answers

**Q1. What is the Relational Model, and what are its basic building blocks?**
The Relational Model organizes data into relations (tables), where each table consists of rows (tuples) and columns (attributes). It's built on the mathematical concept of a "relation" (a set of tuples), and is the foundation of RDBMSs like MySQL, Oracle, and PostgreSQL.

**Q2. What is the difference between degree and cardinality of a relation?**
Degree is the number of attributes (columns) in a relation. Cardinality is the number of tuples (rows) in a relation at a given time.

**Q3. What is the difference between Super Key, Candidate Key, and Primary Key?**
A Super Key is any set of attributes that can uniquely identify a tuple (may include extra, unnecessary attributes). A Candidate Key is a minimal super key — no attribute can be removed while still maintaining uniqueness. A Primary Key is the candidate key chosen by the designer (usually with the fewest attributes) to be the main unique identifier for the table.

**Q4. What is a Foreign Key, and what is its purpose?**
A Foreign Key is an attribute (or set of attributes) in one table that references the Primary Key of another table. Its purpose is to establish and enforce a link/relationship between the two tables, ensuring referential integrity — that related data across tables stays consistent.

**Q5. What is the difference between a Composite Key and a Compound Key?**
A Composite Key is simply a primary key made up of two or more attributes (not necessarily foreign keys). A Compound Key is a specific type of composite key where the attributes involved are themselves foreign keys referencing other tables — commonly seen in junction tables that resolve many-to-many relationships.

**Q6. What is a Surrogate Key? Why is it used?**
A Surrogate Key is a system-generated, artificial identifier (typically an auto-incrementing integer) used as the Primary Key, instead of using a natural/business attribute. It's used because it's simple, stable (doesn't change even if business data changes), and avoids issues with composite natural keys.

**Q7. What are the four main types of Integrity Constraints in the Relational Model?**
Domain Constraints (restrict allowed values/data types per attribute), Entity Constraints (every relation must have a non-null Primary Key), Referential Constraints (foreign key values must match a primary key in the referenced table, or be NULL), and Key Constraints (NOT NULL, UNIQUE, DEFAULT, CHECK, PRIMARY KEY, FOREIGN KEY).

**Q8. What is Referential Integrity? Explain with an example.**
Referential Integrity ensures that a Foreign Key value in the child (referencing) table must either be NULL or match an existing Primary Key value in the parent (referenced) table. Example: an `Orders` table's `customer_id` (FK) must correspond to an actual `id` in the `Customer` table (PK) — you can't have an order pointing to a non-existent customer.

**Q9. Can a Foreign Key contain NULL values?**
Yes, a Foreign Key can contain NULL values (unless explicitly restricted otherwise), which typically indicates that the relationship is optional for that particular row — e.g., an employee row where the `manager_id` FK is NULL because that employee has no manager.

**Q10. Why must every relation have a Primary Key, and why can't it be NULL?**
A Primary Key uniquely identifies each row in a table, which is essential for referencing that row unambiguously (e.g., from other tables via foreign keys, or during updates/deletes). If it were NULL, the row couldn't be reliably identified or distinguished from others, violating the Entity Integrity constraint.

**Q11. What is the difference between UNIQUE and PRIMARY KEY constraints?**
Both ensure uniqueness of values in a column, but a table can have only one PRIMARY KEY (which also cannot be NULL), whereas a table can have multiple UNIQUE constraints/columns, and UNIQUE columns can contain a NULL value (though only one NULL is typically allowed per unique constraint, depending on the DBMS).

**Q12. What does it mean for values in a relational table to be "atomic"?**
It means each value stored in a cell (at the intersection of a row and column) must be indivisible — a single, non-decomposable value — rather than a list or a nested set of values. This is a foundational property of the relational model and directly relates to 1NF (First Normal Form) in normalization.
