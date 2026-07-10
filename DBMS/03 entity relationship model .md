# LEC-3: Entity-Relationship Model

## 1. Data Model
- A collection of conceptual tools for describing data, data relationships, data semantics, and consistency constraints.

## 2. ER Model
- It is a **high-level data model** based on a perception of a real world that consists of a collection of basic objects, called **entities**, and of **relationships** among these objects.
- The graphical representation of the ER Model is the **ER diagram**, which acts as a **blueprint** of the DB.

## 3. Entity
- An Entity is a "thing" or "object" in the real world that is **distinguishable** from all other objects.
- It has **physical existence**.
- E.g., each student in a college is an entity.
- An entity can be **uniquely** identified (by a primary attribute, aka Primary Key).

### Strong vs Weak Entity
- **Strong Entity:** Can be uniquely identified (has its own primary key).
- **Weak Entity:** Can't be uniquely identified on its own; depends on some other strong entity.
  - It doesn't have sufficient attributes to select a uniquely identifiable attribute.
  - E.g., `Loan` -> Strong Entity, `Payment` -> Weak Entity, as instalment numbers are a sequential counter generated separately for each loan.
  - **Weak entity depends on strong entity for existence.**

> **Extra:** A weak entity is identified using a combination of its own **partial key** (discriminator) and the primary key of its owning strong entity. In ER diagrams, weak entities are shown with a **double-bordered rectangle**, and their identifying relationship with a **double-bordered diamond**.

## 4. Entity Set
- A set of entities of the **same type** that share the **same properties**, or attributes.
- E.g., `Student` is an entity set.
- E.g., `Customer` of a bank.

## 5. Attributes
- An entity is represented by a set of attributes.
- Each entity has a value for each of its attributes.
- For each attribute, there is a set of **permitted values**, called the **domain**, or **value set**, of that attribute.
- E.g., a `Student` entity has the following attributes: Student_ID, Name, Standard, Course, Batch, Contact number, Address.

### Types of Attributes

**a. Simple**
- Attributes which can't be divided further.
- E.g., Customer's account number in a bank, Student's Roll number, etc.

**b. Composite**
- Can be divided into subparts (that is, other attributes).
- E.g., `Name` of a person can be divided into first-name, middle-name, last-name.
- The user may want to refer to the entire attribute or to only a component of it.
- `Address` can also be divided into street, city, state, PIN code.

**c. Single-valued**
- Only one value for the attribute.
- E.g., Student ID, loan-number for a loan.

**d. Multi-valued**
- Attribute having more than one value.
- E.g., phone-number, nominee-name on some insurance, dependent-name, etc.
- A limit constraint may be applied — upper or lower limits.

**e. Derived**
- Value of this type of attribute can be **derived** from the value of other related attributes.
- E.g., Age, loan-age, membership-period, etc.

> **Extra:** Derived attributes are usually **not physically stored** in the database — they're computed on the fly (e.g., `Age` derived from `Date_of_Birth`), which also avoids data-inconsistency issues that would arise from storing a value that could go stale.

### NULL Value
- An attribute takes a NULL value when an entity does not have a value for it.
- It may indicate **"not applicable"** — the value doesn't exist. E.g., a person having no middle-name.
- It may indicate **"unknown"**:
  1. **Missing:** e.g., the name value of a customer is NULL, meaning it is missing, since name must have some value.
  2. **Not known:** e.g., the salary attribute value of an employee is NULL, meaning it is simply not known yet.

## 6. Relationships
- **Association** among two or more entities.
- E.g., Person *has* Vehicle, Parent *has* Child, Customer *borrows* Loan, etc.
- **Strong Relationship** — between two independent (strong) entities.
- **Weak Relationship** — between a weak entity and its owner/strong entity.
  - E.g., `Loan <instalment-payments> Payment`.

### Degree of Relationship
- Number of entities participating in a relationship.
- **Unary** — Only one entity participates. E.g., Employee manages Employee.
- **Binary** — Two entities participate. E.g., Student takes Course.
- **Ternary** — Three entities participate. E.g., Employee works-on Branch, Employee works-on Job.
- **Binary relationships are the most common.**

## 7. Relationship Constraints

### a. Mapping Cardinality / Cardinality Ratio
- The number of entities to which another entity can be associated via a relationship.

| Cardinality | Description | Example |
|---|---|---|
| **One to one** | Entity in A associates with at most one entity in B, and an entity of B is associated with at most one entity of A. | Citizen has Aadhar Card. |
| **One to many** | Entity in A is associated with N entities in B; entity in B is associated with at most one entity in A. | Citizen has Vehicle. |
| **Many to one** | Entity in A is associated with at most one entity in B; entity in B can be associated with N entities in A. | Course taken by Professor. |
| **Many to many** | Entity in A associated with N entities in B, and entity in B also associated with N entities in A. | Customer buys Product; Student attends Course. |

### b. Participation Constraints
- Also known as **Minimum cardinality constraint**.
- **Types:** Partial & Total Participation.
  - **Partial Participation:** Not all entities are involved in the relationship instance.
  - **Total Participation:** Each entity must be involved in at least one relationship instance.
- E.g., Customer borrows Loan — Loan has **total participation** (a loan can't exist without a customer entity), while Customer has **partial participation** (not every customer necessarily has a loan).
- **Weak entities always have total participation constraint**, but strong entities may not have total participation.

## 8. ER Notations

| Symbol | Meaning |
|---|---|
| Rectangle | Entity |
| Double-bordered Rectangle | Weak Entity |
| Ellipse | Attribute |
| Double-bordered Ellipse | Multi-valued Attribute |
| Underlined Ellipse | Primary Key Attribute |
| Dashed-underlined Ellipse | Weak Key (Partial/Discriminator) Attribute |
| Dotted/Dashed Ellipse | Derived Attribute |
| Diamond | Relationship |
| Double-bordered Diamond | Weak Relationship |
| Double line between Relationship & Entity | Total Participation |

---

## Interview Questions & Answers

**Q1. What is the ER Model, and why is it used?**
The Entity-Relationship (ER) Model is a high-level conceptual data model that represents real-world objects as entities and their associations as relationships. It's used as a blueprint for designing a database before converting it into a relational schema, making it easier to visualize and communicate the structure of data.

**Q2. What is the difference between a strong entity and a weak entity?**
A strong entity has its own primary key and can be uniquely identified independently. A weak entity does not have enough attributes to form a unique primary key on its own and depends on a strong (owner) entity for its existence and identification — it's identified using a partial key combined with the owner entity's primary key.

**Q3. Give an example of a weak entity and explain why it's weak.**
`Payment` for a `Loan` is a classic weak entity — a payment (installment) doesn't have a globally unique identifier of its own; installment numbers are sequential but reset per loan. So Payment is identified only in combination with the Loan it belongs to (Loan's primary key + installment number).

**Q4. What are the different types of attributes in the ER model? Give an example of each.**
Simple (Roll number), Composite (Name → first, middle, last), Single-valued (Student ID), Multi-valued (Phone numbers), Derived (Age, computed from Date of Birth).

**Q5. What does a NULL value represent in a database, and what are its two interpretations?**
A NULL represents the absence of a value for an attribute. It can mean "not applicable" (e.g., no middle name exists) or "unknown" — which itself splits into "missing" (a value must exist but wasn't recorded, like a mandatory Name field) and "not known yet" (a value may or may not exist yet, like Salary before it's finalized).

**Q6. What is the degree of a relationship? Explain unary, binary, and ternary relationships.**
Degree refers to the number of entity types participating in a relationship. Unary involves one entity type relating to itself (Employee manages Employee), Binary involves two entity types (Student takes Course) — the most common type — and Ternary involves three entity types simultaneously (Employee works-on Branch and Job together).

**Q7. Explain the four types of mapping cardinality with examples.**
One-to-one: Citizen ↔ Aadhar Card (each citizen has exactly one Aadhar card and vice versa). One-to-many: Citizen → Vehicles (one citizen can own many vehicles, but each vehicle belongs to one citizen). Many-to-one: Courses → Professor (many courses taught by one professor). Many-to-many: Students ↔ Courses (many students attend many courses).

**Q8. What is the difference between total participation and partial participation?**
Total participation means every entity instance in the entity set must participate in at least one relationship instance (e.g., every Loan must have a Customer). Partial participation means only some entity instances participate in the relationship (e.g., not every Customer has taken a Loan).

**Q9. Why does a weak entity always have total participation?**
Because a weak entity's existence is entirely dependent on its owning strong entity — without the identifying relationship to the strong entity, the weak entity cannot exist or be identified at all, so it must always participate in that relationship.

**Q10. What is the difference between an entity and an entity set?**
An entity is a single, specific real-world object (e.g., "Ravi", a particular student). An entity set is a collection of entities of the same type sharing the same attributes (e.g., the `Student` entity set containing all student entities).

**Q11. How is a weak entity represented in an ER diagram?**
With a double-bordered (or double-outlined) rectangle for the entity itself, a double-bordered diamond for its identifying relationship with the owner entity, and a dashed/underlined ellipse for its partial (discriminator) key attribute.

**Q12. What is a derived attribute, and why is it usually not stored physically?**
A derived attribute is one whose value can be calculated from other attributes (e.g., Age from Date of Birth). It's usually not stored directly to avoid redundancy and potential inconsistency — since if the source attribute (DOB) changes, a stored derived value (Age) could become outdated unless it's re-computed dynamically whenever needed.
