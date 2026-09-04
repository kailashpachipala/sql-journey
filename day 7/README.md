 SQL Query Execution Order

## Overview

Understanding the **logical execution order** of SQL clauses is critical — it explains *why* certain clauses can or cannot reference aliases, aggregates, or filtered rows from other clauses.

> **Key Insight for Exams:** The order you *write* SQL is different from the order SQL *executes* it.

### Written Order vs Execution Order

| Step | Written Order | Execution Order |
|------|--------------|-----------------|
| 1 | `SELECT` | `FROM` |
| 2 | `FROM` | `WHERE` |
| 3 | `WHERE` | `GROUP BY` |
| 4 | `GROUP BY` | `HAVING` |
| 5 | `HAVING` | `SELECT` |
| 6 | `ORDER BY` | `ORDER BY` |

---

## Step 1 — FROM

**Definition:** The `FROM` clause identifies the source table(s) from which data is to be retrieved. It is the first clause executed — before any filtering, grouping, or selection.

**What happens at this step:**
- The database engine loads the specified table(s) into a working dataset (conceptually a full table or a joined result).
- If multiple tables are involved, **JOINs** are resolved here.
- All rows and all columns are available at this point — no filtering has occurred yet.

```sql
SELECT *
FROM employees;
```

```sql
-- With JOIN (also resolved in FROM)
SELECT e.name, d.department_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;
```

**Key Rule:** Every column referenced in later clauses must come from a table declared in `FROM`.

---

## Step 2 — WHERE

**Definition:** The `WHERE` clause filters individual rows from the dataset produced by `FROM`, based on a specified condition. It operates **before** grouping or aggregation.

**What happens at this step:**
- Each row is tested against the condition.
- Rows that do **not** satisfy the condition are eliminated.
- Aggregate functions (`SUM`, `COUNT`, `AVG`, etc.) **cannot** be used here because grouping has not happened yet.

```sql
SELECT *
FROM employees
WHERE salary > 50000;
```

### Example Table — `employees`

| emp_id | name    | dept_id | salary |
|--------|---------|---------|--------|
| 1      | Alice   | 10      | 70000  |
| 2      | Bob     | 20      | 45000  |
| 3      | Carol   | 10      | 90000  |
| 4      | David   | 20      | 55000  |
| 5      | Eve     | 10      | 40000  |

**After `WHERE salary > 50000`:**

| emp_id | name  | dept_id | salary |
|--------|-------|---------|--------|
| 1      | Alice | 10      | 70000  |
| 3      | Carol | 10      | 90000  |
| 4      | David | 20      | 55000  |

**Key Rules:**
- Cannot use column aliases defined in `SELECT` (SELECT hasn't run yet).
- Cannot use aggregate functions (`WHERE AVG(salary) > 50000` → ❌ Invalid).
- Use `BETWEEN`, `IN`, `LIKE`, `IS NULL`, `AND`, `OR`, `NOT` for conditions.

```sql
-- Common WHERE operators
WHERE salary BETWEEN 40000 AND 80000
WHERE dept_id IN (10, 20)
WHERE name LIKE 'A%'
WHERE manager_id IS NULL
```

---

## Step 3 — GROUP BY

**Definition:** The `GROUP BY` clause **collects rows sharing the same value(s)** in the specified column(s) into a single group, enabling aggregate functions to be applied per group.

**What happens at this step:**
- Rows surviving the `WHERE` filter are grouped together by the specified column(s).
- Each unique combination of `GROUP BY` column values becomes one group.
- After this step, each group produces **one output row**.

```sql
SELECT dept_id, COUNT(*) AS total_employees, AVG(salary) AS avg_salary
FROM employees
WHERE salary > 50000
GROUP BY dept_id;
```

**After `GROUP BY dept_id`** (on the WHERE-filtered rows):

| dept_id | (grouped rows)         |
|---------|------------------------|
| 10      | Alice (70000), Carol (90000) |
| 20      | David (55000)          |

**Output after aggregation:**

| dept_id | total_employees | avg_salary |
|---------|----------------|------------|
| 10      | 2              | 80000      |
| 20      | 1              | 55000      |

**Key Rules:**
- Every column in `SELECT` that is **not** inside an aggregate function **must** appear in `GROUP BY`.
- You can group by multiple columns: `GROUP BY dept_id, job_title`.
- `NULL` values are grouped together as one group.

```sql
-- Grouping by multiple columns
SELECT dept_id, job_title, COUNT(*) AS count
FROM employees
GROUP BY dept_id, job_title;
```

---

## Step 4 — HAVING

**Definition:** The `HAVING` clause filters **groups** produced by `GROUP BY`, based on aggregate conditions. It is the `WHERE` equivalent for groups.

**What happens at this step:**
- Each group (from `GROUP BY`) is tested against the `HAVING` condition.
- Groups that do not satisfy the condition are eliminated.
- Aggregate functions **can and should** be used here.

```sql
SELECT dept_id, COUNT(*) AS total_employees, AVG(salary) AS avg_salary
FROM employees
WHERE salary > 50000
GROUP BY dept_id
HAVING COUNT(*) >= 2;
```

**After `HAVING COUNT(*) >= 2`** (dept 20 had only 1 employee — eliminated):

| dept_id | total_employees | avg_salary |
|---------|----------------|------------|
| 10      | 2              | 80000      |

### WHERE vs HAVING — Critical Comparison

| Feature | `WHERE` | `HAVING` |
|---|---|---|
| Filters | Individual rows | Groups |
| Executes at | Step 2 (before grouping) | Step 4 (after grouping) |
| Aggregate functions | ❌ Not allowed | ✅ Allowed |
| Works without GROUP BY | ✅ Yes | Rarely useful |
| Example | `WHERE salary > 50000` | `HAVING AVG(salary) > 60000` |

```sql
-- HAVING without GROUP BY (applies to the entire table as one group)
SELECT AVG(salary)
FROM employees
HAVING AVG(salary) > 60000;
```

---

## Step 5 — SELECT

**Definition:** The `SELECT` clause specifies **which columns or expressions** to include in the final result. It is executed after all filtering and grouping are complete.

**What happens at this step:**
- Columns, expressions, aliases, and aggregate results are evaluated.
- Column aliases are defined here (which is why `WHERE` and `HAVING` cannot use them — they execute before `SELECT`).
- `DISTINCT` is applied here to remove duplicate rows.

```sql
SELECT dept_id, 
       COUNT(*) AS total_employees, 
       AVG(salary) AS avg_salary,
       MAX(salary) - MIN(salary) AS salary_range
FROM employees
WHERE salary > 50000
GROUP BY dept_id
HAVING COUNT(*) >= 2;
```

**Key Rules:**
- Aliases created in `SELECT` are **not** available in `WHERE`, `GROUP BY`, or `HAVING`.
- Aliases **are** available in `ORDER BY` (it executes after `SELECT`).
- `SELECT DISTINCT` removes duplicate rows after all other processing.

```sql
-- DISTINCT removes duplicate values
SELECT DISTINCT dept_id
FROM employees;
```

### Common SELECT Expressions

| Expression | Example | Purpose |
|---|---|---|
| Column | `SELECT name` | Retrieve column |
| Alias | `SELECT salary AS pay` | Rename output column |
| Arithmetic | `SELECT salary * 1.1` | Compute expression |
| Aggregate | `SELECT COUNT(*)` | Summarize group |
| Concatenation | `SELECT name \|\| ' ' \|\| dept_id` | Combine strings |
| Conditional | `SELECT CASE WHEN salary > 60000 THEN 'High' ELSE 'Low' END` | Inline logic |

---

## Step 6 — ORDER BY

**Definition:** The `ORDER BY` clause **sorts the final result set** by one or more columns or expressions, in ascending (`ASC`) or descending (`DESC`) order.

**What happens at this step:**
- This is the very last operation — it sorts the rows that survived all previous steps.
- Because `SELECT` has already run, **aliases defined in `SELECT` can be used here**.
- Does not affect which rows are returned — only their order.

```sql
SELECT dept_id, COUNT(*) AS total_employees, AVG(salary) AS avg_salary
FROM employees
WHERE salary > 50000
GROUP BY dept_id
HAVING COUNT(*) >= 2
ORDER BY avg_salary DESC;
```

```sql
-- ORDER BY multiple columns
SELECT name, dept_id, salary
FROM employees
ORDER BY dept_id ASC, salary DESC;
```

**Output:**

| name  | dept_id | salary |
|-------|---------|--------|
| Carol | 10      | 90000  |
| Alice | 10      | 70000  |
| Eve   | 10      | 40000  |
| David | 20      | 55000  |
| Bob   | 20      | 45000  |

**Key Rules:**
- Default sort order is `ASC` (ascending).
- Can sort by column name, alias, or column position number: `ORDER BY 3 DESC`.
- `NULL` values appear first in `ASC` order (in most databases).
- Does **not** eliminate duplicates — use `DISTINCT` in `SELECT` for that.

```sql
-- ORDER BY column position
SELECT name, dept_id, salary
FROM employees
ORDER BY 3 DESC;   -- sorts by salary (3rd column)
```

---

## Complete Query — All Clauses Together

```sql
SELECT dept_id, 
       COUNT(*) AS total_employees, 
       AVG(salary) AS avg_salary
FROM employees
WHERE salary > 40000
GROUP BY dept_id
HAVING COUNT(*) > 1
ORDER BY avg_salary DESC;
```

### Execution Walkthrough

| Step | Clause | Action | Rows Remaining |
|------|--------|--------|---------------|
| 1 | `FROM employees` | Load full table | 5 rows |
| 2 | `WHERE salary > 40000` | Remove Eve (40000) | 4 rows |
| 3 | `GROUP BY dept_id` | Group: dept 10 → {Alice, Carol}, dept 20 → {Bob, David} | 2 groups |
| 4 | `HAVING COUNT(*) > 1` | dept 20 has 2 rows ✅, dept 10 has 2 rows ✅ | 2 groups |
| 5 | `SELECT ...` | Compute COUNT and AVG per group | 2 rows |
| 6 | `ORDER BY avg_salary DESC` | Sort by avg salary, highest first | 2 rows (sorted) |

**Final Output:**

| dept_id | total_employees | avg_salary |
|---------|----------------|------------|
| 10      | 2              | 80000      |
| 20      | 2              | 50000      |

---

## Why Execution Order Matters — Common Exam Errors

| Mistake | Why It Fails | Fix |
|---|---|---|
| `WHERE COUNT(*) > 2` | `WHERE` runs before `GROUP BY`; aggregates don't exist yet | Use `HAVING COUNT(*) > 2` |
| `HAVING salary > 50000` | Technically works but is inefficient; filters rows not groups | Use `WHERE salary > 50000` |
| `WHERE dept_alias = 10` (alias from SELECT) | `SELECT` hasn't run yet when `WHERE` executes | Use the actual column name |
| `ORDER BY` using a non-selected aggregate | Ambiguous in some DBMS | Include aggregate in `SELECT` |
| Listing a non-aggregated, non-grouped column in `SELECT` | Violates `GROUP BY` rules | Add to `GROUP BY` or wrap in aggregate |

---

## Quick Revision Summary

```
FROM      → Load data (join tables)
WHERE     → Filter rows (no aggregates)
GROUP BY  → Collect rows into groups
HAVING    → Filter groups (aggregates allowed)
SELECT    → Choose columns, compute expressions, assign aliases
ORDER BY  → Sort final output (aliases allowed)
```

> **Mnemonic:** **F**red **W**ent **G**rocery **H**unting, **S**elected **O**ranges
> → **F**ROM → **W**HERE → **G**ROUP BY → **H**AVING → **S**ELECT → **O**RDER BY