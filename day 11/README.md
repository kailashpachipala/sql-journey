 Data Manipulation Language (DML)

**Data Manipulation Language (DML)** is a subset of SQL used to retrieve, insert, modify, and delete data within database tables. Unlike DDL, DML does not alter the structure of tables — it operates on the data stored inside them.

> **Core DML Commands:** `SELECT` · `INSERT` · `UPDATE` · `DELETE`

---

# `SELECT` — Querying the Database

## Definition

**`SELECT`** is used to retrieve data from one or more tables. It is the most frequently used DML command and forms the basis of all database queries.

## General Syntax

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition
GROUP BY column
HAVING condition
ORDER BY column [ASC | DESC]
LIMIT n;
```

> **Clause Execution Order (Important for Exams):**
> `FROM` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `ORDER BY` → `LIMIT`

---

## Sample Table

```sql
-- Table: students
-- +--------+----------+--------+---------+--------+
-- | stu_id | name     | dept   | city    | marks  |
-- +--------+----------+--------+---------+--------+
-- | 101    | Alice    | CS     | Delhi   | 88     |
-- | 102    | Bob      | ECE    | Mumbai  | 74     |
-- | 103    | Carol    | CS     | Delhi   | 91     |
-- | 104    | David    | ME     | Chennai | 60     |
-- | 105    | Eve      | ECE    | Mumbai  | 85     |
-- | 106    | Frank    | CS     | Delhi   | 74     |
-- +--------+----------+--------+---------+--------+
```

---

## `SELECT` Variants

### 1. Select All Columns

```sql
SELECT * FROM students;
```

Returns every column and row from the table.

---

### 2. Select Specific Columns

```sql
SELECT name, dept, marks
FROM students;
```

| name  | dept | marks |
|-------|------|-------|
| Alice | CS   | 88    |
| Bob   | ECE  | 74    |
| Carol | CS   | 91    |
| David | ME   | 60    |
| Eve   | ECE  | 85    |
| Frank | CS   | 74    |

---

### 3. `WHERE` Clause — Filtering Rows

**`WHERE`** filters rows based on a specified condition. Only rows satisfying the condition are returned.

```sql
SELECT name, marks
FROM students
WHERE dept = 'CS';
```

| name  | marks |
|-------|-------|
| Alice | 88    |
| Carol | 91    |
| Frank | 74    |

---

### 4. Logical Operators — `AND`, `OR`, `NOT`

```sql
-- AND: both conditions must be true
SELECT name FROM students
WHERE dept = 'CS' AND marks > 80;
```

| name  |
|-------|
| Alice |
| Carol |

```sql
-- OR: either condition must be true
SELECT name FROM students
WHERE city = 'Delhi' OR marks > 85;
```

```sql
-- NOT: negates the condition
SELECT name FROM students
WHERE NOT dept = 'ME';
```

---

### 5. `BETWEEN` Operator

**`BETWEEN`** selects values within an inclusive range.

```sql
SELECT name, marks
FROM students
WHERE marks BETWEEN 70 AND 90;
```

| name  | marks |
|-------|-------|
| Alice | 88    |
| Bob   | 74    |
| Eve   | 85    |
| Frank | 74    |

---

### 6. `IN` Operator

**`IN`** checks whether a value matches any value in a specified list.

```sql
SELECT name, dept
FROM students
WHERE dept IN ('CS', 'ECE');
```

| name  | dept |
|-------|------|
| Alice | CS   |
| Bob   | ECE  |
| Carol | CS   |
| Eve   | ECE  |
| Frank | CS   |

---

### 7. `LIKE` Operator — Pattern Matching

**`LIKE`** is used for pattern matching using wildcards.

| Wildcard | Meaning                        |
|----------|-------------------------------|
| `%`      | Zero or more characters        |
| `_`      | Exactly one character          |

```sql
-- Names starting with 'A'
SELECT name FROM students WHERE name LIKE 'A%';

-- Names with exactly 3 characters
SELECT name FROM students WHERE name LIKE '___';

-- Names containing 'al'
SELECT name FROM students WHERE name LIKE '%al%';
```

---

### 8. `ORDER BY` — Sorting Results

**`ORDER BY`** sorts query results in ascending (`ASC`) or descending (`DESC`) order. Default is `ASC`.

```sql
SELECT name, marks
FROM students
ORDER BY marks DESC;
```

| name  | marks |
|-------|-------|
| Carol | 91    |
| Alice | 88    |
| Eve   | 85    |
| Bob   | 74    |
| Frank | 74    |
| David | 60    |

---

### 9. `DISTINCT` — Remove Duplicates

**`DISTINCT`** eliminates duplicate values from the result set.

```sql
SELECT DISTINCT dept FROM students;
```

| dept |
|------|
| CS   |
| ECE  |
| ME   |

---

### 10. `LIMIT` — Restrict Rows Returned

```sql
-- Top 3 scorers
SELECT name, marks
FROM students
ORDER BY marks DESC
LIMIT 3;
```

| name  | marks |
|-------|-------|
| Carol | 91    |
| Alice | 88    |
| Eve   | 85    |

---

### 11. Column Aliases — `AS`

**`AS`** assigns a temporary display name to a column or expression in the result.

```sql
SELECT name AS Student_Name, marks AS Score
FROM students;
```

---

## Aggregate Functions with `GROUP BY` and `HAVING`

**Aggregate functions** perform a calculation on a set of rows and return a single summarized value.

| Function    | Description                    |
|-------------|-------------------------------|
| `COUNT()`   | Number of rows                 |
| `SUM()`     | Total of numeric column        |
| `AVG()`     | Average of numeric column      |
| `MAX()`     | Maximum value                  |
| `MIN()`     | Minimum value                  |

### `GROUP BY`

**`GROUP BY`** groups rows sharing the same value in a specified column so aggregate functions can be applied per group.

```sql
SELECT dept, COUNT(*) AS total_students, AVG(marks) AS avg_marks
FROM students
GROUP BY dept;
```

| dept | total_students | avg_marks |
|------|---------------|-----------|
| CS   | 3             | 84.33     |
| ECE  | 2             | 79.50     |
| ME   | 1             | 60.00     |

### `HAVING`

**`HAVING`** filters groups after `GROUP BY` is applied. It is like `WHERE` but operates on aggregated results.

> **Key Difference:** `WHERE` filters individual rows (before grouping); `HAVING` filters groups (after grouping).

```sql
SELECT dept, AVG(marks) AS avg_marks
FROM students
GROUP BY dept
HAVING AVG(marks) > 75;
```

| dept | avg_marks |
|------|-----------|
| CS   | 84.33     |
| ECE  | 79.50     |

---

## `WHERE` vs `HAVING` — Comparison Table

| Feature          | `WHERE`                          | `HAVING`                          |
|-----------------|----------------------------------|-----------------------------------|
| **Operates on** | Individual rows                  | Grouped rows / aggregates         |
| **Used with**   | Any `SELECT` query               | `GROUP BY` only                   |
| **Aggregate functions** | ❌ Not allowed            | ✅ Allowed                        |
| **Execution order** | Before `GROUP BY`            | After `GROUP BY`                  |
| **Example**     | `WHERE marks > 80`               | `HAVING AVG(marks) > 80`          |

---

## Subqueries in `SELECT`

A **subquery** (nested query) is a `SELECT` statement embedded within another SQL statement.

```sql
-- Students who scored above the overall average
SELECT name, marks
FROM students
WHERE marks > (SELECT AVG(marks) FROM students);
```

| name  | marks |
|-------|-------|
| Alice | 88    |
| Carol | 91    |
| Eve   | 85    |

---

---

# `INSERT` — Adding Data to the Database

## Definition

**`INSERT INTO`** adds new rows of data into a table.

## Syntax Variants

### 1. Insert a Single Row (Specifying Columns)

```sql
INSERT INTO students (stu_id, name, dept, city, marks)
VALUES (107, 'Grace', 'CS', 'Pune', 79);
```

> Always specify column names explicitly — it protects against schema changes and prevents accidental mismatches.

---

### 2. Insert a Single Row (Without Column Names)

```sql
INSERT INTO students
VALUES (107, 'Grace', 'CS', 'Pune', 79);
```

> Values must be in the exact order of columns as defined in the table schema.

---

### 3. Insert Multiple Rows

```sql
INSERT INTO students (stu_id, name, dept, city, marks)
VALUES
    (108, 'Hank', 'ME', 'Kolkata', 55),
    (109, 'Iris', 'CS', 'Delhi', 93),
    (110, 'Jay',  'ECE', 'Chennai', 68);
```

---

### 4. Insert Data from Another Table

```sql
-- Copy CS students into a new table: cs_students
INSERT INTO cs_students (stu_id, name, marks)
SELECT stu_id, name, marks
FROM students
WHERE dept = 'CS';
```

---

## Important Rules for `INSERT`

- **NOT NULL** columns must be given a value, unless a `DEFAULT` is defined.
- **Primary Key** values must be unique and non-null.
- **Data types** of values must match column definitions.
- **Foreign Key** values must already exist in the referenced (parent) table.

---

---

# `UPDATE` — Modifying Existing Data

## Definition

**`UPDATE`** modifies the values of one or more columns in existing rows of a table, based on a condition.

## General Syntax

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

> ⚠️ **Critical:** Always use a `WHERE` clause. Omitting it updates **every row** in the table.

---

## Examples

### 1. Update a Single Column for One Row

```sql
UPDATE students
SET marks = 95
WHERE stu_id = 103;
```

Carol's marks are updated from 91 to 95.

---

### 2. Update Multiple Columns

```sql
UPDATE students
SET city = 'Hyderabad', marks = 80
WHERE stu_id = 104;
```

---

### 3. Update Using an Expression

```sql
-- Add 5 bonus marks to all ECE students
UPDATE students
SET marks = marks + 5
WHERE dept = 'ECE';
```

---

### 4. Update All Rows (No `WHERE` — Use with Caution)

```sql
-- Deduct 2 marks from every student
UPDATE students
SET marks = marks - 2;
```

> This affects every row. Always double-check before running without `WHERE`.

---

### 5. Update Using a Subquery

```sql
-- Set marks of the lowest scorer to 50
UPDATE students
SET marks = 50
WHERE marks = (SELECT MIN(marks) FROM students);
```

> Note: Some databases (like MySQL) do not allow referencing the same table in a subquery for `UPDATE`. A derived table workaround may be needed.

---

---

# `DELETE` — Removing Data from the Database

## Definition

**`DELETE FROM`** removes one or more rows from a table based on a condition.

## General Syntax

```sql
DELETE FROM table_name
WHERE condition;
```

> ⚠️ **Critical:** Always use a `WHERE` clause. Omitting it deletes **every row** in the table.

---

## Examples

### 1. Delete a Specific Row

```sql
DELETE FROM students
WHERE stu_id = 104;
```

David's record is removed from the table.

---

### 2. Delete Rows Matching a Condition

```sql
DELETE FROM students
WHERE marks < 65;
```

Removes all students who scored less than 65.

---

### 3. Delete Using `IN`

```sql
DELETE FROM students
WHERE dept IN ('ME', 'ECE');
```

---

### 4. Delete All Rows (No `WHERE` — Dangerous)

```sql
DELETE FROM students;
```

> Deletes all data but **preserves the table structure** (schema remains intact). This is the key difference from `DROP TABLE`.

---

### 5. Delete Using a Subquery

```sql
-- Remove students from the city with the lowest average marks
DELETE FROM students
WHERE city = (
    SELECT city FROM students
    GROUP BY city
    ORDER BY AVG(marks) ASC
    LIMIT 1
);
```

---

---

# DML Command Comparison Table

| Feature               | `SELECT`              | `INSERT`              | `UPDATE`                    | `DELETE`                    |
|-----------------------|-----------------------|-----------------------|-----------------------------|-----------------------------|
| **Purpose**           | Retrieve data         | Add new rows          | Modify existing data        | Remove rows                 |
| **Affects structure** | ❌ No                 | ❌ No                 | ❌ No                       | ❌ No                       |
| **Returns rows**      | ✅ Yes                | ❌ No                 | ❌ No                       | ❌ No                       |
| **`WHERE` clause**    | Optional (filter)     | ❌ Not applicable     | ⚠️ Critical (else all rows) | ⚠️ Critical (else all rows) |
| **Rollback possible** | ✅ Yes (no change)    | ✅ Yes                | ✅ Yes                      | ✅ Yes                      |
| **Typical risk**      | None                  | Duplicate / type mismatch | Updates all rows if no `WHERE` | Deletes all rows if no `WHERE` |

---

# `DELETE` vs `TRUNCATE` vs `DROP` — Critical Exam Comparison

| Feature                  | `DELETE`                    | `TRUNCATE`                   | `DROP`                        |
|--------------------------|-----------------------------|------------------------------|-------------------------------|
| **Command type**         | DML                         | DDL                          | DDL                           |
| **Removes rows**         | ✅ Yes (selective or all)   | ✅ Yes (all rows)            | ✅ Yes (entire table)         |
| **Removes structure**    | ❌ No                       | ❌ No                        | ✅ Yes                        |
| **`WHERE` clause**       | ✅ Supported                | ❌ Not supported             | ❌ Not supported              |
| **Rollback possible**    | ✅ Yes                      | ❌ Usually No                | ❌ No                         |
| **Resets auto-increment**| ❌ No                       | ✅ Yes                       | ✅ Yes (table is gone)        |
| **Speed**                | Slower (row-by-row logging) | Faster (minimal logging)     | Fastest                       |
| **Triggers fired**       | ✅ Yes                      | ❌ No                        | ❌ No                         |

---

# Quick Revision — Key Exam Points

- **DML commands** operate on data, not on table structure; changes can be rolled back within a transaction.
- **`SELECT`** clause execution order: `FROM` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `ORDER BY` → `LIMIT`.
- **`WHERE`** filters individual rows; **`HAVING`** filters groups after aggregation — both can appear in the same query.
- **`DISTINCT`** removes duplicate rows from output; it applies to the entire row, not just one column.
- **`GROUP BY`** must include every non-aggregated column in the `SELECT` list.
- **`INSERT INTO ... SELECT`** copies data from one table into another without loading it externally.
- **`UPDATE` without `WHERE`** modifies every row — always verify your condition first.
- **`DELETE` without `WHERE`** removes every row but preserves the table; **`DROP`** removes the table entirely.
- **`TRUNCATE`** is faster than `DELETE` for clearing all rows but cannot be rolled back in most databases.
- **Subqueries** can be used inside `SELECT`, `INSERT`, `UPDATE`, and `DELETE` to make conditions dynamic.