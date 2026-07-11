# LEC-10: Triggers & Stored Procedures

*(Not present in original notes — added as a critical interview topic, fits topically after LEC-9's DML/Views coverage.)*

## 1. What is a Trigger?
- A **Trigger** is a special kind of stored procedure that **automatically executes (fires)** in response to certain events (`INSERT`, `UPDATE`, `DELETE`) on a specified table.
- Triggers are defined via **DDL** (`CREATE TRIGGER` / `DROP TRIGGER`), but their body typically contains **DML-like logic**.
- Used to enforce complex business rules, auditing/logging, auto-updating related data, and maintaining data integrity beyond what standard constraints can do.

## 2. Types of Triggers

### By Timing
- **BEFORE trigger** — fires **before** the triggering DML statement executes. Often used for validation or auto-populating a column (e.g., setting a `created_at` timestamp).
- **AFTER trigger** — fires **after** the triggering DML statement executes. Often used for auditing, logging, or cascading updates to other tables.

### By Event
- `INSERT` trigger, `UPDATE` trigger, `DELETE` trigger.

### By Granularity
- **Row-level trigger** — fires **once per affected row**. Uses `OLD` and `NEW` pseudo-records to access the row's before/after values.
- **Statement-level trigger** — fires **once per SQL statement**, regardless of how many rows were affected. (Not supported directly in MySQL — MySQL only supports row-level triggers.)

## 3. Syntax (MySQL)

```sql
CREATE TRIGGER trigger_name
{BEFORE | AFTER} {INSERT | UPDATE | DELETE}
ON table_name
FOR EACH ROW
BEGIN
    -- trigger logic here
    -- use NEW.column_name for the new value (INSERT/UPDATE)
    -- use OLD.column_name for the old value (UPDATE/DELETE)
END;
```

**Example — Audit log trigger:**
```sql
CREATE TRIGGER before_salary_update
BEFORE UPDATE ON employee
FOR EACH ROW
BEGIN
    INSERT INTO salary_audit(emp_id, old_salary, new_salary, changed_at)
    VALUES (OLD.emp_id, OLD.salary, NEW.salary, NOW());
END;
```

```sql
DROP TRIGGER IF EXISTS trigger_name;
```

## 4. Use Cases
1. **Auditing/logging** changes to sensitive tables (who changed what, when).
2. **Enforcing complex constraints** that a simple CHECK constraint can't express (e.g., cross-table validation).
3. **Auto-updating derived/denormalized data** (e.g., updating a `total_orders` count column whenever a new order is inserted).
4. **Cascading custom business logic** beyond what `ON DELETE CASCADE` / `ON UPDATE CASCADE` provide.

## 5. Advantages & Disadvantages

| Advantages | Disadvantages |
|---|---|
| Enforces integrity/business rules automatically, no reliance on app code | Hidden logic — hard to debug, "invisible" side effects |
| Centralizes logic in the DB (consistent regardless of which app/client writes to it) | Can hurt performance if overused (fires on every row) |
| Useful for auditing without modifying application code | Can create complex chains if triggers fire other triggers |

## 6. What is a Stored Procedure?
- A **Stored Procedure** is a precompiled collection of one or more SQL statements, stored in the database, that can be **explicitly called/executed** by name (unlike a trigger, which fires automatically).
- Can accept **input parameters (IN)**, **output parameters (OUT)**, or both (**INOUT**).

```sql
DELIMITER //
CREATE PROCEDURE GetEmployeesByDept(IN dept_id INT)
BEGIN
    SELECT * FROM employee WHERE department_id = dept_id;
END //
DELIMITER ;

CALL GetEmployeesByDept(5);
```

## 7. Stored Function vs Stored Procedure vs Trigger

| Aspect | Stored Function | Stored Procedure | Trigger |
|---|---|---|---|
| Invocation | Called within a SQL expression (e.g., `SELECT myFunc(x)`) | Explicitly called via `CALL` | Fires automatically on a DML event |
| Return value | Must return a single value | May or may not return values (via OUT params) | Does not return a value |
| Can call from SELECT? | Yes | No | N/A (not manually invoked) |
| Use case | Reusable computation/transformation | Reusable multi-step business logic | Automatic enforcement/side-effects on data change |

---

## Interview Questions & Answers

**Q1. What is a Trigger, and how is it different from a Stored Procedure?**
A Trigger is a database object that automatically executes in response to a specific event (INSERT/UPDATE/DELETE) on a table, without being explicitly called. A Stored Procedure, by contrast, must be explicitly invoked (via `CALL`) — it doesn't run automatically in response to data changes.

**Q2. What is the difference between a BEFORE trigger and an AFTER trigger?**
A BEFORE trigger fires before the triggering DML statement is executed — commonly used for validating or modifying data before it's written (e.g., auto-setting a timestamp). An AFTER trigger fires after the statement completes — commonly used for auditing, logging, or cascading changes to other tables, since the data change has already happened.

**Q3. What are OLD and NEW in a trigger, and when are they available?**
OLD and NEW are pseudo-records that let you access column values within a row-level trigger. OLD refers to the row's values before the change (available in UPDATE and DELETE triggers), and NEW refers to the row's values after the change (available in INSERT and UPDATE triggers).

**Q4. What is the difference between row-level and statement-level triggers?**
A row-level trigger fires once for every row affected by the triggering statement (so a bulk UPDATE affecting 100 rows fires the trigger 100 times). A statement-level trigger fires only once per SQL statement, regardless of how many rows it affects. MySQL only supports row-level triggers.

**Q5. What are common use cases for triggers?**
Auditing/logging changes to sensitive data, enforcing complex business rules/constraints that can't be expressed via simple CHECK constraints, automatically updating derived or denormalized data (like maintaining a running total), and implementing custom cascading logic beyond what FK constraints provide.

**Q6. What are the downsides of using triggers heavily?**
Triggers introduce "hidden" logic that isn't visible in application code, making systems harder to debug and reason about; they can hurt performance if they run expensive logic on every row; and if triggers cause further data changes that fire other triggers, it can lead to complex, hard-to-trace chains of execution.

**Q7. What is a Stored Function, and how is it different from a Stored Procedure?**
A Stored Function is a reusable block of SQL logic that must return exactly one value and can be called directly within a SQL expression (like inside a SELECT statement). A Stored Procedure doesn't have to return a value (though it can use OUT parameters), can't be called from within a SELECT, and is invoked explicitly via the CALL statement.

**Q8. Why might you use a stored procedure instead of writing the equivalent logic in application code?**
Stored procedures centralize business logic in the database itself, reducing network round-trips (since multiple SQL statements execute together on the server), ensuring consistent behavior regardless of which application/client calls it, and can improve performance since they're precompiled by the database engine.

**Q9. Can a trigger call a stored procedure? Can a trigger cause another trigger to fire?**
Yes to both, in most RDBMSs (with some restrictions in MySQL) — a trigger can contain logic that calls a stored procedure, and if a trigger's action modifies another table that itself has a trigger defined, that second trigger can also fire, creating a chain. This chaining behavior is exactly why triggers can become hard to debug if overused.

**Q10. Give a practical example of when a trigger is a better choice than application-level validation.**
When you need to guarantee that a business rule is enforced regardless of HOW the data is modified — e.g., through the main application, a bulk import script, an admin tool, or even direct SQL access — a trigger enforces the rule at the database level itself, ensuring consistency no matter which path is used to write the data, whereas application-level validation could be bypassed if data is modified through a different channel.
