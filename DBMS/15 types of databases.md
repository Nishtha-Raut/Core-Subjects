# LEC-16: Types of Databases

## 1. Relational Databases
- Based on the **Relational Model**.
- Relational databases are quite **popular**, even though the system was designed in the **1970s**. Also known as **Relational Database Management Systems (RDBMS)**.
- Commonly use **SQL** for operations such as creating, reading, updating, and deleting data.
- Store information in **discrete tables**, which can be **JOIN**ed together by fields known as **foreign keys**.
- Example: a `User` table containing info about all users, joined to a `Purchases` table containing info about all their purchases.
- **Examples:** MySQL, Microsoft SQL Server, Oracle.

### Characteristics
- Ubiquitous — steady user base since the 1970s.
- Highly **optimised for working with structured data**.
- Provide a **stronger guarantee of data normalisation**.
- Use a well-known querying language through **SQL**.
- **Scalability issues** (Horizontal Scaling is hard).
- As data becomes huge, the system becomes **more complex**.

## 2. Object-Oriented Databases
- Based on the **object-oriented-programming (OOP) paradigm**, which is now in wide use.
- **Inheritance**, **object-identity**, and **encapsulation** (information hiding) — with methods to provide an interface to objects — are among the key OOP concepts that have found applications in data modelling.
- Also supports a **rich type system**, including structured and collection types.
- While inheritance and (to some extent) complex types are also present in the E-R model, **encapsulation and object-identity** distinguish the object-oriented data model from the E-R model.
- Sometimes the DB can be very complex, having multiple relations — maintaining relationships between them can be **tedious**.
  - In Object-oriented databases, data is treated as an **object**.
  - All bits of information come in **one instantly available object package**, instead of multiple tables.

### Advantages
1. Data storage and retrieval is **easy and quick**.
2. Can handle **complex data relations** and a greater variety of data types than standard relational databases.
3. Relatively friendly to model **advanced real-world problems**.
4. Works with the functionality of **OOPs and Object-Oriented languages**.

### Disadvantages
1. High complexity causes **performance issues** — read, write, update, and delete operations are slowed down.
2. **Not much community support**, as it isn't as widely adopted as relational databases.
3. Does **not support views** like relational databases.

- **Examples:** ObjectDB, GemStone.

## 3. NoSQL Databases
- NoSQL databases (aka "not only SQL") are **non-tabular databases** and store data differently than relational tables.
- Main types: **document, key-value, wide-column, and graph**.
- Provide **flexible schemas** and scale easily with large amounts of data and high user loads.
- Key traits: schema-free, flexible non-tabular structures, can handle big data, mostly open-source, capable of horizontal scaling, stores data in some format other than relational.
- *(Refer to LEC-15 notes for full details.)*

## 4. Hierarchical Databases
- Most appropriate for use cases where the main focus of information gathering is based on a **concrete hierarchy**, such as several individual employees reporting to a single department at a company.
- Schema is defined by a **tree-like organisation**, with typically a **root "parent"** directory of data stored as records that links to various other subdirectory branches; each subdirectory branch (or **child record**) may link to further subdirectory branches.
- **Structure rule:** while a parent record can have **several child records**, each child record can only have **one parent record**.
- Data within records is stored in the form of **fields**, and each field can only contain **one value**.
- Retrieving hierarchical data requires **traversing the entire tree**, starting at the root node.
- Since disk storage is also inherently hierarchical, these models can also be used as **physical models**.

### Advantage
- **Ease of use** — the one-to-many organisation of data makes traversing the database **simple and fast**, ideal for use cases such as website drop-down menus or computer folders (like Microsoft Windows OS folders). Due to the separation of tables from physical storage structures, info can easily be added or deleted without affecting the entirety of the database. Most major programming languages offer functionality for reading tree-structure databases.

### Disadvantage
- **Inflexible nature** — the one-to-many structure is not ideal for complex structures, as it cannot describe relationships in which each child node has **multiple parent nodes**. Also, the tree-like organisation requires **top-to-bottom sequential searching**, which is time-consuming, and requires **repetitive storage** of data in multiple different entities, which can be redundant.

- **Example:** IBM IMS.

## 5. Network Databases
- **Extension** of Hierarchical databases.
- Child records are given the **freedom to associate with multiple parent records** (unlike hierarchical, which restricts to one parent).
- Organised in a **Graph structure**.
- Can handle **complex relations**.
- **Maintenance is tedious.**
- **M:N links** may cause **slow retrieval**.
- **Not much web community support.**

- **Examples:** Integrated Data Store (IDS), IDMS (Integrated Database Management System), Raima Database Manager, TurboIMAGE.

```
              A1
            /    \
          B1      B2
        / |  \   /| \ \
      C1 C2  C3 C4 C5 C6
         |    |
         D1   D2  D3
              (etc. — nodes can have multiple parents,
               unlike a strict tree in Hierarchical DB)
```

> **Extra — Hierarchical vs Network Databases (comparison, common interview ask):**

| Aspect | Hierarchical | Network |
|---|---|---|
| Structure | Tree (strict) | Graph |
| Parent-child relation | Each child has exactly **1** parent | Each child can have **multiple** parents |
| Complexity of relations | Limited (1:N only) | Can model M:N relations |
| Flexibility | Rigid | More flexible than hierarchical |
| Example | IBM IMS | IDMS, IDS |

> **Extra — Overall evolution of database types (useful narrative for interviews):**
> `Hierarchical (1960s) → Network (late 1960s/70s, extension of Hierarchical) → Relational (1970s, solved rigidity issues of both) → Object-Oriented (1980s-90s, for complex data types) → NoSQL (late 2000s, for scale & flexibility with big data/cloud)`

---

## Interview Questions & Answers

**Q1. What are the main types of databases, and how do they differ in structure?**
The main types are Relational (data in tables linked by foreign keys), Object-Oriented (data as objects with inheritance/encapsulation), NoSQL (non-tabular — document/key-value/wide-column/graph), Hierarchical (tree structure, one parent per child), and Network (graph structure, multiple parents per child allowed).

**Q2. What is the key difference between Hierarchical and Network databases?**
In a Hierarchical database, each child record can have only ONE parent record, forming a strict tree structure. In a Network database (an extension of Hierarchical), a child record can have MULTIPLE parent records, forming a more flexible graph structure — allowing it to model many-to-many relationships that Hierarchical databases cannot.

**Q3. What is the main limitation of Hierarchical databases, and how does the Network model address it?**
Hierarchical databases can't represent relationships where a child entity needs to relate to multiple parent entities (many-to-many relationships) — since each child can have only one parent. The Network model addresses this by organizing data as a graph rather than a strict tree, allowing child records to associate with multiple parents, enabling more complex relationship modeling.

**Q4. Why did Relational Databases become so dominant over Hierarchical and Network models?**
Relational databases solved the rigidity problems of both Hierarchical (strict one-parent tree) and Network (still requires navigating explicit links/pointers) models by using a mathematically grounded, flexible table-based structure with declarative querying (SQL) — data could be related dynamically via foreign keys/joins at query time, rather than requiring pre-defined physical navigation paths, making them far easier to use, query, and maintain.

**Q5. What distinguishes an Object-Oriented database from a Relational database?**
Object-Oriented databases store data as objects (following OOP principles like inheritance, encapsulation, and object-identity), keeping all related information bundled together in one object rather than split across multiple normalized tables. Relational databases, by contrast, decompose data into normalized tables linked by keys, requiring joins to reconstruct a complete "object's" data — but relational databases benefit from decades of tooling, community support, and simpler standardized querying (SQL).

**Q6. What are the main disadvantages of Object-Oriented Databases?**
High complexity leading to performance issues in CRUD operations, much smaller community/tooling support compared to relational databases (limiting adoption), and lack of support for views (a common convenience feature in relational databases).

**Q7. Why are Hierarchical databases still considered good for use cases like file systems or drop-down menus?**
Because those use cases naturally fit a strict one-to-many parent-child structure — e.g., a folder can contain many files/subfolders, but each file/subfolder belongs to exactly one parent folder — making the simple, fast tree-traversal nature of hierarchical databases a natural and efficient fit.

**Q8. What does it mean that "disk storage is also inherently a hierarchical structure," and why is this relevant?**
Physical disk storage systems are often organized in tree-like directory structures (folders containing subfolders and files), which mirrors the hierarchical database model directly. This relevance means hierarchical database concepts can double as physical storage models, not just logical/conceptual ones — the model naturally maps onto how data is actually laid out on disk.

**Q9. Give a real-world scenario where you would prefer a Network database over a Hierarchical one.**
A scenario involving many-to-many relationships, such as a university course-registration system where a single student can enroll in multiple courses AND a single course can have multiple students — a Hierarchical model (one parent per child) can't cleanly represent this bidirectional many-to-many link, whereas a Network model's graph structure can.

**Q10. In the evolution of database systems, what problem did each new model type primarily aim to solve?**
Hierarchical databases first solved organized, fast data storage/retrieval for simple one-to-many relationships (like org charts). Network databases extended this to handle more complex many-to-many relationships. Relational databases then solved the rigidity/navigational complexity of both by introducing a flexible, mathematically-grounded table model with declarative SQL querying. Object-Oriented databases aimed to better handle complex, real-world data types by aligning the database model with OOP application code. NoSQL databases emerged to solve massive-scale, flexible-schema, and cloud-distributed data needs that traditional relational databases struggled with.

**Q11. Why do Object-Oriented databases claim "quick and easy data retrieval" as an advantage, despite performance issues being listed as a disadvantage?**
The "quick and easy" advantage refers to retrieving a *complete, complex object* in one go (since all related data is bundled together as a single object, avoiding the need for multiple joins) — this is fast for accessing a full object's data at once. However, the performance issue disadvantage refers to the overhead introduced by the complexity of managing rich type systems, inheritance hierarchies, and encapsulation internally, which can slow down individual read/write/update/delete operations, especially at scale — these are two different aspects of performance, not a direct contradiction.

**Q12. Name one real-world example database system for each of the five database types discussed.**
Relational: MySQL. Object-Oriented: ObjectDB. NoSQL: MongoDB (document type). Hierarchical: IBM IMS. Network: IDMS (Integrated Database Management System).
