# LEC-30: SQL Window Functions & Common Interview Queries

*(Not present in original notes — added as one of the most frequently-tested topics in SQL coding interviews/rounds.)*

## 1. What is a Window Function?
- A **Window Function** performs a calculation across a **set of rows related to the current row** (called a "window"), **without collapsing** the result into a single row per group (unlike `GROUP BY` + aggregate functions, which do collapse rows).
- General syntax:
```sql
function_name() OVER (
    [PARTITION BY column(s)]
    [ORDER BY column(s)]
)
```
- `PARTITION BY` divides the result set into groups (windows) — similar in spirit to `GROUP BY`, but rows are **not** collapsed.
- `ORDER BY` (within the window) determines the order in which the window function's logic is applied (important for ranking/running-total type functions).

## 2. Window Functions vs GROUP BY

| Aspect | GROUP BY (with aggregate) | Window Function |
|---|---|---|
| Row count in output | **Reduced** — one row per group | **Same as input** — every original row is preserved |
| Access to individual row detail | Lost (only aggregated values remain) | Preserved (can see both individual row data AND aggregate/rank info together) |
| Use case | Simple summary/aggregate reports | Ranking, running totals, row-by-row comparisons within groups |

## 3. Common Window Functions

### a. ROW_NUMBER()
- Assigns a **unique, sequential number** to each row within a partition, based on the specified order — no ties, always unique.
```sql
SELECT name, department, salary,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS row_num
FROM employees;
```

### b. RANK()
- Assigns a rank to each row within a partition, based on the specified order — **ties get the same rank**, but the next rank **skips** numbers accordingly (e.g., 1, 2, 2, 4).

### c. DENSE_RANK()
- Similar to `RANK()`, but **does not skip** numbers after a tie (e.g., 1, 2, 2, 3).

> **Extra — ROW_NUMBER vs RANK vs DENSE_RANK comparison (very common interview question):**

| Salary | ROW_NUMBER() | RANK() | DENSE_RANK() |
|---|---|---|---|
| 90000 | 1 | 1 | 1 |
| 85000 | 2 | 2 | 2 |
| 85000 | 3 | 2 | 2 |
| 80000 | 4 | 4 | 3 |

### d. Aggregate Functions as Window Functions
- Standard aggregates (`SUM`, `AVG`, `COUNT`, `MIN`, `MAX`) can also be used as window functions, to compute running/cumulative values without collapsing rows.
```sql
-- Running total of sales per day
SELECT sale_date, amount,
    SUM(amount) OVER (ORDER BY sale_date) AS running_total
FROM sales;
```

### e. LAG() and LEAD()
- `LAG()` — accesses data from a **previous** row in the result set (within the window), without needing a self-join.
- `LEAD()` — accesses data from a **subsequent (next)** row.
```sql
SELECT sale_date, amount,
    LAG(amount, 1) OVER (ORDER BY sale_date) AS previous_day_amount,
    LEAD(amount, 1) OVER (ORDER BY sale_date) AS next_day_amount
FROM sales;
```

## 4. Common SQL Interview Coding Questions

### a. Find the Nth Highest Salary
```sql
-- Using DENSE_RANK (handles duplicate salaries correctly)
SELECT salary FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk = 2;  -- for 2nd highest

-- Classic alternative (without window functions), for 2nd highest:
SELECT MAX(salary) FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

### b. Find Duplicate Rows
```sql
SELECT email, COUNT(*) AS cnt
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

### c. Employees Earning More Than Their Manager
```sql
SELECT e.name AS employee_name
FROM employees e
JOIN employees m ON e.manager_id = m.emp_id
WHERE e.salary > m.salary;
```
*(This is a classic Self Join use case — see LEC-9.)*

### d. Delete Duplicate Rows (keeping only one copy)
```sql
DELETE t1 FROM users t1
INNER JOIN users t2
WHERE t1.email = t2.email AND t1.id > t2.id;
```

### e. Second Highest Value Per Group
```sql
SELECT department, salary FROM (
    SELECT department, salary,
        DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk = 2;
```

### f. Consecutive Days / Streaks (Gaps and Islands problem — advanced, often asked)
```sql
-- Find users with 3+ consecutive login days
-- Core trick: (date - ROW_NUMBER() as a "grouping key") stays constant for consecutive dates
SELECT user_id, MIN(login_date) AS streak_start, COUNT(*) AS streak_length
FROM (
    SELECT user_id, login_date,
        DATE_SUB(login_date, INTERVAL ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) DAY) AS grp
    FROM logins
) t
GROUP BY user_id, grp
HAVING COUNT(*) >= 3;
```

---

## Interview Questions & Answers

**Q1. What is a Window Function, and how is it different from GROUP BY?**
A Window Function performs a calculation across a set of related rows (a "window") without collapsing the result — every original row is preserved in the output, alongside the computed window value. GROUP BY with an aggregate function, in contrast, collapses multiple rows into a single summary row per group, losing access to individual row-level detail.

**Q2. What is the difference between ROW_NUMBER(), RANK(), and DENSE_RANK()?**
ROW_NUMBER() assigns a unique sequential number to every row, even if values tie (no duplicate numbers). RANK() assigns the same rank to tied rows, but skips subsequent rank numbers accordingly (e.g., 1, 2, 2, 4). DENSE_RANK() also assigns the same rank to tied rows, but does NOT skip numbers afterward (e.g., 1, 2, 2, 3).

**Q3. How would you find the Nth highest salary using SQL?**
The cleanest modern approach uses DENSE_RANK(): rank all salaries in descending order using `DENSE_RANK() OVER (ORDER BY salary DESC)` in a subquery, then filter the outer query `WHERE rnk = N`. DENSE_RANK is preferred over ROW_NUMBER here because it correctly handles duplicate salary values (so if two people share the highest salary, the "2nd highest" is still a genuinely different value, not just the next row).

**Q4. What do LAG() and LEAD() do, and when would you use them?**
LAG() retrieves a value from a previous row (relative to the current row, within the defined window/order), and LEAD() retrieves a value from a subsequent row — both without requiring a self-join. They're commonly used for comparing a row to the one before/after it, such as calculating day-over-day change in a time series, or finding gaps between consecutive events.

**Q5. How would you find duplicate records in a table?**
Group the table by the column(s) that should be unique (e.g., email), count occurrences of each group using COUNT(*), and filter using HAVING COUNT(*) > 1 — this returns only the values that appear more than once, identifying duplicates.

**Q6. How would you write a query to find employees who earn more than their manager?**
Using a self-join on the employees table: join the table to itself, matching each employee's manager_id to another employee's emp_id (representing that manager), then filter where the employee's salary is greater than the manager's salary.

**Q7. What is PARTITION BY in a window function, and how is it different from GROUP BY?**
PARTITION BY divides the result set into groups (partitions) for the purposes of the window function's calculation — similar in concept to GROUP BY, but critically, it does NOT collapse rows; each row in the partition still appears individually in the output, now annotated with its window-function result (e.g., its rank within that partition).

**Q8. Can you use a window function's result directly in a WHERE clause? Why or why not?**
No — window functions are logically evaluated after the WHERE clause in SQL's query processing order (window functions execute during the same phase as SELECT), so their computed values (e.g., a rank or running total) are not yet available to filter on directly in a WHERE clause. To filter based on a window function's result, you must wrap the query in a subquery/CTE and apply the filter in the OUTER query's WHERE clause instead.

**Q9. What is the "Gaps and Islands" problem in SQL, and what's a common technique to solve it?**
It's a class of problems involving finding consecutive/contiguous groups of rows (e.g., users with 3+ consecutive login days, or continuous stretches of a stock price staying above a value) — "islands" of consecutive values separated by "gaps." A common technique is to compute a "grouping key" by subtracting a ROW_NUMBER() (ordered appropriately) from the actual sequential value (like a date) — this difference stays constant for genuinely consecutive rows, allowing you to GROUP BY this computed key to identify each "island."

**Q10. How would you compute a running total using SQL?**
Using a window function with SUM() and an ORDER BY clause inside the OVER() clause but no PARTITION BY (or with PARTITION BY if you want separate running totals per group): `SUM(amount) OVER (ORDER BY date)` — this computes a cumulative sum up to and including each row, in the specified order, without collapsing the individual rows.

**Q11. Why is DENSE_RANK() generally preferred over ROW_NUMBER() when finding the "Nth highest distinct value"?**
Because ROW_NUMBER() assigns a unique number to every single row regardless of ties — so if two rows have the exact same (highest) value, ROW_NUMBER() would label them 1 and 2, meaning your "2nd highest" result would actually just be a duplicate of the highest value, not a genuinely different one. DENSE_RANK() correctly assigns the same rank to tied values and only increments the rank for a genuinely different value, ensuring "Nth highest" correctly refers to the Nth distinct value.

**Q12. What's the difference between deleting duplicate rows using a self-join versus using a window function approach (e.g., with ROW_NUMBER)?**
A self-join approach (like comparing `t1.id > t2.id` for matching duplicate keys) directly deletes rows matching the join condition, and is straightforward but can be less readable for complex duplicate criteria. A window-function approach typically uses ROW_NUMBER() OVER (PARTITION BY duplicate_key ORDER BY some_tiebreaker) in a CTE/subquery to number each duplicate group, then deletes all rows where the row number is greater than 1 — often considered cleaner and more flexible for more complex duplicate-detection logic involving multiple columns or specific "keep this one" rules.
