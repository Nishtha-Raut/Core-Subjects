# LEC-8: Transform - ER Model to Relational Model

## 1. Introduction
- Both ER-Model and Relational Model are abstract logical representations of real-world enterprises. Because the two models imply similar design principles, we can **convert ER design into Relational design**.
- Converting a DB representation from an ER diagram to a table format is how we arrive at a Relational DB design from an ER diagram.

## 2. ER Diagram Notations → Relations

### a. Strong Entity
1. Becomes an **individual table** with the entity name; attributes become columns of the relation.
2. Entity's **Primary Key (PK)** is used as the Relation's PK.
3. **FKs** are added to establish relationships with other relations.

### b. Weak Entity
1. A table is formed with **all** the attributes of the entity.
2. The PK of its corresponding Strong Entity is added as an **FK**.
3. The PK of the relation becomes a **composite PK**: `{FK + Partial discriminator Key}`.

### c. Single-valued Attributes
1. Represented as **columns** directly in the tables/relations.

### d. Composite Attributes
1. Handled by creating a **separate attribute/column** for each sub-part in the original relation.
2. E.g., `Address: {street-name, house-no}` is a composite attribute in the customer relation — we add `address-street-name` & `address-house-no` as new columns, and ignore `address` itself as an attribute.

### e. Multivalued Attributes
1. **New tables** (named as the original attribute name) are created for each multivalued attribute.
2. The PK of the entity is used as a column **FK** in the new table.
3. The multivalued attribute's name is added as a column to hold multiple values (one row per value).
4. The PK of the new table would be `{FK + multivalued attribute name}`.
5. **Example:** For Strong entity `Employee`, `dependent-name` is a multivalued attribute.
   - New table named `dependent-name` is formed with columns `emp-id`, and `dname`.
   - PK: `{emp-id, name}`
   - FK: `{emp-id}`

### f. Derived Attributes
- **Not considered** in the tables — they are computed on the fly, not stored.

## 3. Generalisation → Relations

### Method-1 (Table for every level)
- Create a table for the **higher-level entity set**. For **each** lower-level entity set, create a table that includes a column for each of that entity set's own attributes, **plus** a column for each attribute of the primary key of the higher-level entity set.
- **Example — Banking System generalisation of `Account` into `Savings` & `Current`:**
  1. Table 1: `account (account-number, balance)`
  2. Table 2: `savings-account (account-number, interest-rate, daily-withdrawal-limit)`
  3. Table 3: `current-account (account-number, overdraft-amount, per-transaction-charges)`

### Method-2 (No table for higher level — only for disjoint & complete generalisation)
- An alternative representation is possible **if** the generalisation is **disjoint and complete** — that is:
  - No entity is a member of two lower-level entity sets directly below a higher-level entity set (disjoint), **and**
  - Every entity in the higher-level entity set is also a member of one of the lower-level entity sets (complete).
- Here, we **do not** create a table for the higher-level entity set. Instead, for each lower-level entity set, create a table that includes a column for each of that entity set's own attributes **plus** a column for each attribute of the higher-level entity set.
- **Example — Tables:**
  1. Table 1: `savings-account (account-number, balance, interest-rate, daily-withdrawal-limit)`
  2. Table 2: `current-account (account-number, balance, overdraft-amount, per-transaction-charges)`

### Drawbacks of Method-2
- If Method-2 is used for an **overlapping** generalisation, some values (such as `balance`) would be stored **twice** unnecessarily (redundancy).
- If the generalisation is **not complete** — i.e., some accounts are neither savings nor current — then such accounts could **not** be represented at all using Method-2.

> **Extra — When to use which method:** Use Method-1 (table for higher-level + tables for each subclass) when the generalisation is overlapping and/or partial, since it correctly handles entities that may or may not belong to subclasses and avoids duplicating common attributes. Use Method-2 (no higher-level table) only when the generalisation is strictly disjoint and total/complete, since it avoids an extra join at the cost of only working under those constraints.

## 4. Aggregation → Relations
1. A table of the **relationship set** is made.
2. Attributes include the **primary keys** of the participating entity sets and the aggregation set's entities.
3. Also, add any **descriptive attribute**, if present, on the relationship.

> **Extra — Example:** For the aggregation example from LEC-4 (`Employee works-on Project`, monitored by `Manager`), the resulting table would be something like:
> `Monitors (emp_id, project_id, manager_id, monitor_start_date)` — where `emp_id + project_id` together represent the aggregated "works-on" relationship, `manager_id` is the FK to Manager, and `monitor_start_date` is a descriptive attribute of the `monitors` relationship.

---

## Interview Questions & Answers

**Q1. How does a Strong Entity get converted into a relational table?**
It becomes its own individual table with the entity name; all its attributes become columns, its primary key becomes the table's primary key, and foreign keys are added as needed to reference related tables.

**Q2. How does a Weak Entity get converted into a relational table? Why does it need a composite key?**
A table is created with all the weak entity's attributes, plus the primary key of its owning strong entity is added as a foreign key. Since the weak entity doesn't have a unique identifier of its own, its table's primary key must be a composite key made of the owner's FK plus the weak entity's own partial (discriminator) key, together they form a unique identifier.

**Q3. How are composite attributes handled when converting ER to relational schema?**
Each sub-part of the composite attribute is broken out into its own separate column in the table, and the original composite attribute itself is not stored as a single column. E.g., `Address (street, house-no)` becomes two columns: `address-street` and `address-house-no`.

**Q4. How are multivalued attributes handled in the relational model, and why can't they just be a column?**
Since relational tables require atomic (single) values per cell, a multivalued attribute can't be stored directly as a column. Instead, a new separate table is created, containing the owning entity's primary key (as FK) plus the multivalued attribute itself, with one row per value — the new table's primary key becomes the combination of the FK and the attribute value.

**Q5. Are derived attributes stored in the relational schema? Why or why not?**
No, derived attributes are typically not stored as columns in the relational schema, because their value can always be recomputed from other stored attributes (e.g., Age from Date of Birth) — storing them would introduce redundancy and potential inconsistency if the source attribute changes.

**Q6. Explain the two methods of converting Generalisation/Specialisation hierarchies into relational tables.**
Method-1 creates a table for the higher-level (superclass) entity with its own attributes, and a separate table for each lower-level (subclass) entity containing its own attributes plus the superclass's primary key as FK — this works for any type of generalisation. Method-2 skips the higher-level table entirely and instead includes all the superclass's attributes directly inside each subclass table — but this only works correctly if the generalisation is disjoint (no overlapping membership) and complete (every superclass entity belongs to some subclass).

**Q7. What are the drawbacks of Method-2 for converting generalisation to relational tables?**
If the generalisation is overlapping, common attributes (like `balance`) get duplicated across multiple subclass tables, wasting space and risking inconsistency. If the generalisation is not complete (some entities don't belong to any subclass), those entities simply cannot be represented at all under Method-2, since there's no table for the superclass alone.

**Q8. When should you prefer Method-1 over Method-2 (or vice versa)?**
Prefer Method-1 when the generalisation is overlapping and/or partial (not every superclass entity fits a subclass) — it's more general and always correct, though it needs a join to get the full picture of an entity. Prefer Method-2 only when the generalisation is strictly disjoint and total/complete — it avoids the extra join and extra table, but only works under those specific constraints.

**Q9. How is Aggregation converted into a relational table?**
A table is created for the relationship set involved in the aggregation. Its columns include the primary keys of all participating entity sets (including the entities in the aggregated relationship) plus any descriptive attributes that belong to the relationship itself.

**Q10. Why is it important to be able to convert an ER diagram into a relational schema?**
Because ER modeling is a conceptual design tool meant for humans to visualize and communicate the structure of data, but actual RDBMSs (like MySQL, Oracle) store and query data using tables. The conversion process is the practical bridge that turns a conceptual database design into an implementable relational schema.

**Q11. What determines the primary key of a table generated from a weak entity?**
It's a composite key formed by combining the foreign key (referencing the owning strong entity's primary key) with the weak entity's own partial/discriminator key — together, these two values uniquely identify each row.

**Q12. In the multivalued attribute conversion, why is the new table's primary key a composite of the FK and the attribute value itself?**
Because a single entity (identified by the FK) can have multiple values for that attribute, so the FK alone isn't unique in this new table — it repeats once per value. Combining the FK with the specific attribute value ensures each row (each individual value instance) is uniquely identified.
