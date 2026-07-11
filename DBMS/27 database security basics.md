# LEC-28: Database Security Basics

*(Not present in original notes — added as a commonly-asked topic, connecting to LEC-2's DCL commands and general DBA responsibilities.)*

## 1. Authentication vs Authorization
- **Authentication:** The process of **verifying identity** — confirming that a user is who they claim to be (e.g., via username/password, tokens, certificates).
- **Authorization:** The process of **determining what an authenticated user is allowed to do** — i.e., what data/operations they can access, once their identity is confirmed.

> **Extra — Simple mnemonic:** Authentication = "Who are you?" Authorization = "What are you allowed to do?" A user can be successfully authenticated (logged in) but still be denied specific actions due to insufficient authorization.

## 2. Access Control via DCL (Recap + Expansion)
- As covered in LEC-2/LEC-9, **DCL (Data Control Language)** commands manage database-level access control:
  - `GRANT` — gives specific privileges (SELECT, INSERT, UPDATE, DELETE, etc.) to a user/role on specific database objects.
  - `REVOKE` — removes previously granted privileges.

```sql
GRANT SELECT, INSERT ON employees TO 'analyst_user'@'localhost';
REVOKE INSERT ON employees FROM 'analyst_user'@'localhost';
```

### Role-Based Access Control (RBAC)
- Instead of granting privileges directly to each individual user, privileges are grouped into **roles** (e.g., `read_only`, `admin`, `data_analyst`), and roles are assigned to users.
- Makes managing permissions at scale much easier — updating a role's privileges automatically applies to all users who have that role.

```sql
CREATE ROLE read_only;
GRANT SELECT ON database_name.* TO read_only;
GRANT read_only TO 'analyst_user'@'localhost';
```

## 3. Principle of Least Privilege
- A core security principle: users/applications should be granted **only the minimum privileges necessary** to perform their required tasks — nothing more.
- Reduces the **attack surface** and limits potential damage from compromised accounts or accidental mistakes (e.g., an application user account that only needs SELECT/INSERT shouldn't also have DROP TABLE privileges).

## 4. SQL Injection
- A common and serious **security vulnerability** where an attacker manipulates user input to inject malicious SQL code into a query, exploiting improperly sanitized/concatenated input.

### Example (Vulnerable Code)
```sql
-- Application builds a query like this by string concatenation:
"SELECT * FROM users WHERE username = '" + userInput + "' AND password = '" + passInput + "'"

-- If userInput = "admin' -- ", the resulting query becomes:
SELECT * FROM users WHERE username = 'admin' -- ' AND password = '...'
-- The "--" comments out the password check entirely, bypassing authentication!
```

### Prevention
1. **Parameterized Queries / Prepared Statements** — the gold-standard defense. User input is passed as a **bound parameter**, never directly concatenated into the SQL string, so it can never be interpreted as executable SQL code.
   ```sql
   -- Example using a prepared statement (pseudocode)
   PREPARE stmt FROM 'SELECT * FROM users WHERE username = ? AND password = ?';
   EXECUTE stmt USING @userInput, @passInput;
   ```
2. **Input Validation/Sanitization** — validate and sanitize all user input as a defense-in-depth measure (not a replacement for parameterized queries).
3. **Principle of Least Privilege** — even if an injection succeeds, a properly restricted DB user account limits the damage an attacker can do.
4. **ORM (Object-Relational Mapping) frameworks** — most modern ORMs (like Hibernate, Sequelize, SQLAlchemy) use parameterized queries internally by default, providing built-in protection when used correctly.

## 5. Encryption
- **Encryption at Rest:** Data stored on disk is encrypted, so that even if physical storage/backups are stolen, the data remains unreadable without the decryption key.
- **Encryption in Transit:** Data is encrypted while being transmitted between the client and the database server (e.g., via SSL/TLS), preventing eavesdropping/man-in-the-middle attacks.

## 6. Auditing
- Keeping logs of **who accessed/modified what data, and when** — critical for compliance (e.g., financial or healthcare regulations), forensic investigation after a security incident, and general accountability.
- This directly connects back to **Triggers** (LEC-10) — audit triggers are a common way to implement change-tracking at the database level.

---

## Interview Questions & Answers

**Q1. What is the difference between Authentication and Authorization?**
Authentication verifies a user's identity — confirming they are who they claim to be (e.g., via login credentials). Authorization determines what an already-authenticated user is permitted to do — which data and operations they can access. A user can be authenticated successfully but still lack authorization for specific actions.

**Q2. What is the Principle of Least Privilege, and why is it important?**
It states that users and applications should be granted only the minimum set of privileges necessary to perform their required tasks, nothing more. It's important because it minimizes the potential damage from compromised accounts, insider threats, or accidental mistakes — an account with limited privileges can only cause limited harm, even if misused or exploited.

**Q3. What is SQL Injection, and how does it work?**
SQL Injection is a security vulnerability where an attacker manipulates user-supplied input to inject malicious SQL code into a query, exploiting applications that build SQL queries via unsafe string concatenation of user input. This can allow attackers to bypass authentication, extract unauthorized data, or even modify/delete data, by crafting input that alters the intended structure/logic of the SQL query.

**Q4. What is the most effective way to prevent SQL Injection?**
Using Parameterized Queries (also called Prepared Statements), where user input is passed as a separate, bound parameter rather than being directly concatenated into the SQL query string — this ensures the database treats the input strictly as data, never as executable SQL code, regardless of what characters or SQL keywords it contains.

**Q5. Besides parameterized queries, what other measures help defend against SQL Injection?**
Input validation/sanitization (as a defense-in-depth layer, though not a substitute for parameterized queries), applying the Principle of Least Privilege (so even a successful injection has limited access/damage potential), and using ORM frameworks that internally use parameterized queries by default when used correctly.

**Q6. What is Role-Based Access Control (RBAC), and why is it useful?**
RBAC is an access control approach where privileges are grouped into named roles (e.g., `read_only`, `admin`), and users are assigned to these roles rather than having privileges granted individually to each user. It's useful because it simplifies permission management at scale — updating a role's privileges automatically propagates to all users assigned that role, rather than needing to update each user's permissions individually.

**Q7. What is the difference between Encryption at Rest and Encryption in Transit?**
Encryption at Rest protects data stored on disk (including backups), ensuring it remains unreadable without the decryption key even if the physical storage is stolen or accessed without authorization. Encryption in Transit protects data as it moves between the client and the database server (typically via SSL/TLS), preventing it from being intercepted or read by attackers during transmission over the network.

**Q8. How do GRANT and REVOKE relate to database security?**
GRANT and REVOKE are DCL (Data Control Language) commands used to implement authorization at the database level — GRANT assigns specific privileges (like SELECT, INSERT, UPDATE) to a user or role on specific database objects, while REVOKE removes previously granted privileges, forming the core mechanism by which a DBA enforces access control policies.

**Q9. Why is database auditing important, and how might it be implemented?**
Auditing provides a record of who accessed or modified specific data, and when — critical for regulatory compliance (e.g., financial or healthcare industries), investigating security incidents, and maintaining general accountability. It's commonly implemented using database triggers that automatically log changes (INSERT/UPDATE/DELETE) to an audit table whenever sensitive data is modified, without relying on application-level logging that could be bypassed.

**Q10. If an attacker successfully performs a SQL Injection attack, why does the Principle of Least Privilege still matter as a mitigation?**
Even though the injection itself succeeded, if the compromised database account only has minimal privileges (e.g., SELECT-only access to specific tables, rather than full admin rights), the attacker's ability to exfiltrate, modify, or delete data is significantly constrained. This "defense in depth" approach means a single vulnerability doesn't automatically translate into full database compromise, buying time for detection and limiting overall damage.
