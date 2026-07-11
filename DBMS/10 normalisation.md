# LEC-11: Normalisation

## 1. Introduction
- **Normalisation** is a step towards DB **optimisation**.

## 2. Functional Dependency (FD)
- It's a relationship between the primary key attribute (usually) of the relation to that of the other attributes of the relation.
- **X -> Y**: the left side of the FD is known as the **Determinant**, the right side is known as the **Dependent**.

### Types of FD

**Trivial FD**
- A → B has a trivial functional dependency if B is a **subset** of A. `A->A`, `B->B` are also trivial FDs.

**Non-trivial FD**
- A → B has a **non-trivial** functional dependency if B is **not** a subset of A. [A ∩ B is NULL].

### Rules of FD (Armstrong's Axioms)

**a. Reflexive**
- If 'A' is a set of attributes and 'B' is a subset of 'A', then A → B holds.
- If A ⊇ B, then A → B.

**b. Augmentation**
- If B can be determined from A, then adding an attribute to this functional dependency won't change anything.
- If A → B holds, then AX → BX holds too. 'X' being a set of attributes.

**c. Transitivity**
- If A determines B and B determines C, we can say that A determines C.
- If A → B and B → C, then A → C.

> **Extra — Additional derived rules (often asked in interviews, derived from Armstrong's Axioms):**
> - **Union:** If A → B and A → C, then A → BC.
> - **Decomposition:** If A → BC, then A → B and A → C.
> - **Pseudotransitivity:** If A → B and BC → D, then AC → D.

## 3. Why Normalisation?
- To **avoid redundancy** in the DB — not to store redundant data.

## 4. What happens if we have redundant data?
- **Insertion, deletion, and updation anomalies** arise.

## 5. Anomalies
- Anomalies mean abnormalities — there are **three types** of anomalies introduced by data redundancy.

### a. Insertion Anomaly
- When certain data (attribute) **cannot be inserted** into the DB without the presence of other data.

### b. Deletion Anomaly
- The delete anomaly refers to the situation where the deletion of data results in the **unintended loss** of some other important data.

### c. Updation Anomaly (or Modification Anomaly)
- The update anomaly is when an update of a single data value requires **multiple rows** of data to be updated.
- Due to updation happening in many places, **data inconsistency** may arise if one forgets to update the data at all the intended places.

- Due to these anomalies, **DB size increases** and **DB performance becomes very slow**.
- To rectify these anomalies and their effect on the DB, we use a database optimisation technique called **NORMALISATION**.

> **Extra — Concrete example of anomalies:** Consider an unnormalized table `StudentCourse(student_id, student_name, course_id, course_name, instructor)`:
> - **Insertion anomaly:** We can't add a new course to the DB until at least one student enrolls in it, since `course_id`/`course_name` only exist alongside a student row.
> - **Deletion anomaly:** If the last student enrolled in a course drops it and we delete their row, we accidentally lose all information about that course (course_name, instructor) too.
> - **Update anomaly:** If the instructor for a course changes, we must update the `instructor` value in *every row* where that course appears — miss even one, and the data becomes inconsistent.

## 6. What is Normalisation?
- Normalisation is used to **minimise the redundancy** from relations. It is also used to eliminate undesirable characteristics like **Insertion, Update, and Deletion Anomalies**.
- Normalisation divides composite attributes into individual attributes, OR divides a larger table into smaller ones and links them using relationships.
- The normal form is used to **reduce redundancy** from the database table.

## 7. Types of Normal Forms

### 1NF (First Normal Form)
1. Every relation cell must have an **atomic value**.
2. Relation must **not** have multi-valued attributes.

### 2NF (Second Normal Form)
1. Relation must be in **1NF**.
2. There should **not be any partial dependency**.
   - All non-prime attributes must be **fully dependent** on the PK.
   - A non-prime attribute cannot depend on only **part** of the (composite) PK.

### 3NF (Third Normal Form)
1. Relation must be in **2NF**.
2. **No transitivity dependency** exists.
   - A non-prime attribute should not determine another non-prime attribute.

### BCNF (Boyce-Codd Normal Form)
1. Relation must be in **3NF**.
2. For every FD `A -> B`, **A must be a super key**.
   - We must not derive a prime attribute from any prime or non-prime attribute (i.e., even determinants involving prime attributes must be super keys).

> **Extra — Worked example across normal forms:**
>
> **Unnormalized table:** `Student(student_id, student_name, subjects)` where `subjects` = "Math, Physics" (multi-valued) → violates 1NF.
>
> **1NF fix:** Split multi-valued subjects into atomic rows:
> `Student(student_id, student_name, subject)` — one row per subject.
>
> **2NF example:** Consider `Enrollment(student_id, subject_id, student_name, subject_name, marks)` with composite PK `{student_id, subject_id}`. Here `student_name` depends only on `student_id` (partial dependency) — violates 2NF. Fix: split into `Student(student_id, student_name)` and `Enrollment(student_id, subject_id, marks)` and `Subject(subject_id, subject_name)`.
>
> **3NF example:** Consider `Employee(emp_id, dept_id, dept_name)` where `emp_id -> dept_id -> dept_name` (transitive dependency: dept_name depends on dept_id, not directly on emp_id) — violates 3NF. Fix: split into `Employee(emp_id, dept_id)` and `Department(dept_id, dept_name)`.
>
> **BCNF example:** Consider `Course(course_id, instructor, room)` where each instructor teaches only one course but a room can host multiple courses; suppose FD `instructor -> room` exists, but `instructor` is not a super key (course_id is). This is 3NF but not BCNF since a non-super-key (`instructor`) determines another attribute (`room`). Fix: split into `CourseInstructor(course_id, instructor)` and `InstructorRoom(instructor, room)`.

> **Extra — Higher Normal Forms (beyond original notes, sometimes asked in advanced interviews):**
> - **4NF:** Relation must be in BCNF, and should have no **multi-valued dependency** (an attribute shouldn't have independent multiple values that create redundant combinations with another independent multi-valued attribute).
> - **5NF (Project-Join Normal Form):** Relation must be in 4NF, and it should not be possible to decompose the table further into smaller tables without loss of information (no join dependency remains).

## 8. Advantages of Normalisation
1. Normalisation helps to **minimise data redundancy**.
2. **Greater overall database organisation.**
3. **Data consistency** is maintained in the DB.

> **Extra — Denormalization (commonly paired interview topic):** Sometimes, for performance reasons (fewer joins, faster reads in read-heavy systems like analytics/reporting), a database is deliberately **denormalized** — reintroducing some redundancy on purpose to trade off write-efficiency and storage for read-speed. This is a common follow-up question after Normalisation topics.

---

## Interview Questions & Answers

**Q1. What is Normalisation, and why is it needed?**
Normalisation is the process of organizing a database's tables and columns to minimize data redundancy and eliminate undesirable anomalies (insertion, update, deletion). It's needed because unnormalized data leads to wasted storage, data inconsistency, and difficulty maintaining data integrity as the database grows.

**Q2. What is a Functional Dependency? Give an example.**
A functional dependency (X → Y) means that the value of attribute set X uniquely determines the value of attribute Y — for any two rows with the same X value, Y must also be the same. Example: `student_id → student_name` — knowing the student_id uniquely determines the student's name.

**Q3. What is the difference between trivial and non-trivial functional dependency?**
A trivial FD (A → B) occurs when B is a subset of A (e.g., `{student_id, name} → student_id`), which is always true and doesn't provide new information. A non-trivial FD occurs when B is not a subset of A, meaning it represents a real, meaningful dependency in the data (e.g., `student_id → student_name`).

**Q4. Explain the three types of anomalies with examples.**
Insertion anomaly: can't add a new course to a database until a student enrolls in it, since course details are stored redundantly with student rows. Deletion anomaly: deleting the only student enrolled in a course also deletes all information about that course. Update anomaly: changing an instructor's name requires updating it in every row that references them — missing one row causes inconsistency.

**Q5. What is the difference between 1NF, 2NF, and 3NF?**
1NF requires all attribute values to be atomic (no multi-valued or repeating groups). 2NF requires the relation to be in 1NF and additionally have no partial dependency — every non-prime attribute must depend on the *entire* primary key, not just part of a composite key. 3NF requires 2NF plus no transitive dependency — a non-prime attribute should not depend on another non-prime attribute.

**Q6. What is a partial dependency? Give an example.**
A partial dependency occurs when a non-prime attribute depends on only part of a composite primary key, rather than the whole key. Example: in `Enrollment(student_id, subject_id, student_name)` with composite PK `{student_id, subject_id}`, `student_name` depends only on `student_id`, not on the full composite key — this is a partial dependency, violating 2NF.

**Q7. What is a transitive dependency? Give an example.**
A transitive dependency occurs when a non-prime attribute depends on another non-prime attribute rather than directly on the primary key. Example: in `Employee(emp_id, dept_id, dept_name)`, if `emp_id → dept_id` and `dept_id → dept_name`, then `dept_name` transitively depends on `emp_id` through `dept_id` — violating 3NF.

**Q8. What is BCNF, and how does it differ from 3NF?**
BCNF (Boyce-Codd Normal Form) requires that for every functional dependency A → B in the relation, A must be a super key. It's a stricter version of 3NF — while 3NF allows some FDs where the determinant is a prime attribute but not a full super key, BCNF eliminates all such cases, resolving certain anomalies that can still exist in 3NF relations (typically involving overlapping composite candidate keys).

**Q9. Can a relation be in 3NF but not in BCNF? Give an example.**
Yes. Example: `Course(course_id, instructor, room)` where each instructor teaches exactly one course (so `instructor → course_id`... wait, more precisely: if `instructor → room` holds but `instructor` alone is not a super key of the relation (only `course_id` is), the relation satisfies 3NF (since `room` and `instructor` may both be prime or the check passes under 3NF's relaxed rule) but violates BCNF, because a non-super-key attribute (`instructor`) determines another attribute (`room`).

**Q10. What are Armstrong's Axioms? Name them.**
Armstrong's Axioms are a set of inference rules used to determine all valid functional dependencies from a given set: Reflexivity (if B ⊆ A, then A → B), Augmentation (if A → B, then AX → BX for any X), and Transitivity (if A → B and B → C, then A → C). These are sound and complete for deriving all functional dependencies.

**Q11. What are the derived rules besides the three basic Armstrong's Axioms?**
Union (if A → B and A → C, then A → BC), Decomposition (if A → BC, then A → B and A → C), and Pseudotransitivity (if A → B and BC → D, then AC → D). These are derived from the three basic axioms and are useful shortcuts when reasoning about functional dependencies.

**Q12. What are the advantages of Normalisation?**
It minimizes data redundancy (saving storage and avoiding duplicate updates), leads to greater overall database organization (clearer table structure and relationships), and maintains data consistency (since each fact is stored in exactly one place, reducing the risk of contradictory data).

**Q13. What is denormalization, and why would you use it despite normalisation's benefits?**
Denormalization is the deliberate process of introducing some redundancy back into a normalized database, typically to improve read performance by reducing the number of joins needed for common queries. It's used in read-heavy systems like analytics/reporting/data warehouses, where query speed is prioritized over write-efficiency and strict consistency.

**Q14. What is 4NF, and how does it differ from BCNF?**
4NF requires a relation to be in BCNF and additionally have no multi-valued dependency — meaning there should be no case where one attribute has multiple independent values that combine redundantly with another independent multi-valued attribute in the same relation. It addresses redundancy issues from independent multi-valued facts that BCNF alone doesn't catch.

**Q15. Why is normalisation described as "a step towards DB optimisation"?**
Because by systematically removing redundancy and structural anomalies, normalisation improves storage efficiency, enforces stronger data integrity through well-defined dependencies, and generally makes update/insert/delete operations simpler and less error-prone — all of which contribute to a more optimized, maintainable database design.
