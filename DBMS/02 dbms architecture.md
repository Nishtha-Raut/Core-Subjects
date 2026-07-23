# LEC-2: DBMS Architecture

## 1. View of Data (Three Schema Architecture)
- The major purpose of a DBMS is to provide users with an **abstract view** of the data. That is, the system hides certain details of how the data is stored and maintained.
- To simplify user interaction with the system, abstraction is applied through **several levels of abstraction**.
- The **main objective** of the three-level architecture is to enable multiple users to access the same data with a personalized view, while storing the underlying data only once.

### a. Physical Level / Internal Level
- The **lowest level** of abstraction — describes **how** the data are actually stored.
- Uses low-level data structures.
- Has a **Physical schema** which describes the physical storage structure of the DB.
- Talks about: storage allocation (e.g., N-ary tree etc.), data compression & encryption, etc.
- **Goal:** define algorithms that allow efficient access to data.

### b. Logical Level / Conceptual Level
- The **conceptual schema** describes the design of a database at the conceptual level — describes **what** data is stored in the DB, and what **relationships** exist among that data.
- A user at the logical level does **not** need to be aware of physical-level structures.
- The **DBA**, who must decide what information to keep in the DB, uses the logical level of abstraction.
- **Goal:** ease of use.

### c. View Level / External Level
- **Highest level** of abstraction — aims to simplify users' interaction with the system by providing a different view to different end-users.
- Each **view schema** describes the part of the database that a particular user group is interested in, and hides the rest of the database from that group.
- At the external level, a database contains several schemas, sometimes called **subschemas**. A subschema describes a different view of the database.
- Views also provide a **security** mechanism to prevent users from accessing certain parts of the DB.

```
        External Level        External Level
         (External Schema)    (External Schema)
                \                  /
                 \                /
              External / Conceptual Mapping
                        |
                 Conceptual Level
                (Conceptual Schema)
                        |
              Conceptual / Internal Mapping
                        |
                  Internal Level
                 (Internal Schema)
                        |
                     Database
```

> **Extra — Why 3-schema architecture matters:** It's the backbone of **data independence**. Because each level is decoupled via mappings, changes at one level (e.g., changing storage engine) don't force changes at the levels above it — this is exactly what "physical/logical data independence" (below) is built on.

## 2. Instances and Schemas
- The collection of information stored in the DB at a particular moment is called an **instance** of the DB.
- The overall design of the DB is called the DB **schema**.
- Schema is a **structural** description of data. Schema **doesn't change frequently**; data **may** change frequently.
- DB schema corresponds to the **variable declarations** (along with type) in a program; an instance corresponds to the actual values held by those variables at runtime.
- We have 3 types of schemas: **Physical**, **Logical**, and several **view schemas** (subschemas).
- The **Logical schema** is the most important in terms of its effect on application programs, since programmers build apps using the logical schema.
- **Physical data independence:** a change in the physical schema should not affect the logical schema / application programs.

> **Extra — Logical Data Independence:** The counterpart concept — a change in the logical schema (e.g., adding a new attribute/entity) should ideally not affect the external schemas/application programs that don't use that part of the data. Logical data independence is generally *harder* to achieve than physical data independence, because application code is more tightly coupled to the logical structure.

## 3. Data Models
- Provides a way to describe the **design** of a DB at the logical level.
- Underlying the structure of the DB is the **Data Model** — a collection of conceptual tools for describing data, data relationships, data semantics & consistency constraints.
- Examples: **ER model**, **Relational Model**, **Object-oriented model**, **Object-relational data model**, etc.

## 4. Database Languages
- **Database Language** — a specialized programming language used to read, define, update, and secure data within a Database Management System (DBMS).
- **Data Definition Language (DDL)** — used to specify the database schema.
- **Data Manipulation Language (DML)** — used to express database updates, additions, and removals.
- **Data Query Language (DQL)** — used to specify a statement requesting the retrieval of information.
- **Data Control Language (DCL)** — used to manage security, user roles, and system privileges.
- **Transaction Control Language (TCL)** — used to manage and control logical units of database work.
- Practically, all these language features are present in a single DB language, e.g., **SQL**.

### DDL
- **Definition**: A language used to build, alter, and define the structural skeleton (schema) of database objects.
- We specify **consistency constraints**, which must be checked every time the DB is updated.
- Core commands involve:
  1. `CREATE` — generates a brand new database object like a table or index.
  2. `ALTER` — modifies structural attributes of an existing table or schema.
  3. `DROP` — deletes a table, database, or view entirely from storage.
  4. `TRUNCATE` — wipes out all data rows but leaves the table structure intact.

### DML
- **Definition**: A language used to access, modify, and manage the actual data records stored inside existing database tables.
- Data manipulation involves:
  1. **Insertion** of new information into the DB using the `INSERT` command.
  2. **Deletion** of information from the DB using the `DELETE` command.
  3. **Updating** existing information stored in the DB using the `UPDATE` command.

### DQL
- **Definition**: A dedicated component of the database language focused exclusively on reading and fetching data from tables.
- **Query language** — a part of the language used to specify a statement requesting the retrieval of information.
- Core commands involve:
  1. `SELECT` — retrieves and filters targeted rows or columns from tables.

### DCL
- **Definition**: A language used by administrators to control data access security, user identities, and operational permissions.
- We specify **authorization and access controls** to protect data resources.
- Core commands involve:
  1. `GRANT` — gives specific users permission to execute operations like reading or writing.
  2. `REVOKE` — pulls back previously assigned user access permissions instantly.

### TCL
- **Definition**: A language used to track, run, and isolate changes made by DML statements to maintain data accuracy during workflows.
- We ensure **state preservation** by grouping operations into safe transactional blocks.
- Core commands involve:
  1. `COMMIT` — saves all active transactional modifications permanently to physical storage.
  2. `ROLLBACK` — restores the database to its previous stable state if an error occurs.
  3. `SAVEPOINT` — sets temporary checkpoints within a long transaction for partial rollbacks.


## 5. How is a Database Accessed from Application Programs?
- Applications (written in host languages like C/C++, Java) interact with the DB.
- E.g., a banking system's payroll-generation module accesses the DB by executing DML statements from the host language.
- An **API** is provided to send DML/DDL statements to the DB and retrieve the results:
  - **ODBC** (Open Database Connectivity) — Microsoft "C".
  - **JDBC** (Java Database Connectivity) — Java.

## 6. Database Administrator (DBA)
- A person who has **central control** of both the data and the programs that access that data.
- **Functions of a DBA:**
  1. Schema Definition
  2. Storage structure and access methods
  3. Schema and physical organization modifications
  4. Authorization control
  5. Routine maintenance:
     - Periodic backups
     - Security patches
     - Any upgrades

## 7. DBMS Application Architectures
Client machines (on which remote DB users work) and server machines (on which the DB system runs).

### a. T1 (Single-Tier) Architecture
- The client, server & DB all present on the **same machine**.

### b. T2 (Two-Tier) Architecture
- App is partitioned into **2 components**.
- The **client machine**, which invokes DB system functionality at the server end through query language statements.
- API standards like **ODBC & JDBC** are used to interact between client and server.

### c. T3 (Three-Tier) Architecture
- App is partitioned into **3 logical components**.
- The **client machine** is just a frontend and doesn't contain any direct DB calls.
- The client communicates with the **App server**, and the App server communicates with the **DB system** to access data.
- **Business logic** — what action to take under a given condition — lives in the App server itself.
- T3 architecture is **best for WWW applications**.
- **Advantages:**
  1. **Scalability** — due to distributed application servers.
  2. **Data integrity** — the App server acts as a middle layer between client and DB, minimizing chances of data corruption.
  3. **Security** — client can't directly access the DB, hence it is more secure.

```
Two-Tier Architecture              Three-Tier Architecture
   user                                user
    |                                   |
 application                    application client   <- client
    |            network               |
    | (network)                     network
    |                                   |
database system                application server    <- server
                                        |
                                database system
```

---

## Interview Questions & Answers

**Q1. What is the three-schema architecture, and why is it used?**
It's a way of organizing a DBMS into three levels — Physical (internal), Logical (conceptual), and View (external) — so that users get a simplified, personalized view of data while the DBMS manages one single copy of the underlying data. It exists mainly to achieve data independence, separating how data is *used* from how it is *stored*.

**Q2. What is the difference between the physical level, logical level, and view level in DBMS?**
Physical level describes how data is actually stored on disk (low-level structures, storage allocation, compression). Logical level describes what data is stored and the relationships among it, without concern for physical storage. View level provides a customized, partial view of the database to different user groups, hiding irrelevant/sensitive data.

**Q3. What is the difference between schema and instance?**
Schema is the overall structural design of the database (like a blueprint) — it rarely changes. Instance is the actual data stored in the database at a particular point in time — it changes frequently as data is inserted, updated, or deleted.

**Q4. What is data independence? Explain physical vs logical data independence.**
Data independence is the capacity to change a schema at one level without affecting the schema at a higher level. Physical data independence means changing the physical storage structure doesn't affect the logical schema or applications. Logical data independence means changing the logical schema (e.g., adding a new entity) doesn't affect the external schemas/application programs, provided they don't depend on the changed part.

**Q5. Why is logical data independence harder to achieve than physical data independence?**
Because application programs are typically written against the logical schema directly (e.g., referencing specific tables/columns), so any structural change at the logical level is more likely to break existing queries or code, compared to a physical-level change (like changing indexing or file organization) which is usually invisible to applications.

**Q6. What is the difference between DDL and DML?**
DDL (Data Definition Language) is used to define/modify the database schema — e.g., CREATE, ALTER, DROP. DML (Data Manipulation Language) is used to query and modify the actual data — e.g., SELECT, INSERT, UPDATE, DELETE.

**Q7. What are ODBC and JDBC used for?**
They are standard APIs that allow application programs (written in C/C++ or Java respectively) to connect to and interact with a database by sending DML/DDL statements and retrieving results, without needing to know the database's internal implementation details.

**Q8. What are the functions of a Database Administrator (DBA)?**
Schema definition, deciding storage structure and access methods, modifying schema/physical organization as needed, controlling authorization/access to data, and performing routine maintenance like backups, security patches, and upgrades.

**Q9. Explain the difference between two-tier and three-tier architecture.**
In two-tier architecture, the client directly communicates with the database server using API calls (like ODBC/JDBC) — the client contains both UI and some business logic. In three-tier architecture, an application server sits between the client and the database; the client only handles presentation, the app server handles business logic, and only the app server talks to the database — making the system more scalable and secure.

**Q10. Why is three-tier architecture considered best for web applications?**
Because it separates presentation, business logic, and data layers, allowing each to scale independently (scalability), keeps the database hidden from direct client access (security), and centralizes business rules in the app server, reducing the risk of data corruption from inconsistent client-side logic (data integrity).

**Q11. What is a subschema?**
A subschema is another name for a view schema at the external level — it describes a specific, often partial, view of the database that is relevant to a particular category of users, while hiding the rest of the database from them.

**Q12. Who uses the logical level of abstraction and why?**
The DBA primarily uses the logical level, since they need to decide the overall structure, relationships, and constraints of the data to be stored — without needing to worry about low-level storage details.
