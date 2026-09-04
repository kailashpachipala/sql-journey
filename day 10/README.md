# SQL SELECT — Complete Exam & Practice Notes

> A structured guide to retrieving, filtering, grouping, sorting, and summarizing data using SQL.

---

## 1. What is `SELECT`?

`SELECT` is used to **retrieve data** from one or more tables in a database.

### Basic Syntax

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition
GROUP BY column
HAVING condition
ORDER BY column [ASC | DESC];
```

Not every query requires all clauses.

### Logical Query Processing Order

SQL is written starting with `SELECT`, but logically the database processes the main clauses roughly in this order:

| Order | Clause     | Purpose                   |
| ----: | ---------- | ------------------------- |
|     1 | `FROM`     | Identify the source table |
|     2 | `WHERE`    | Filter individual rows    |
|     3 | `GROUP BY` | Create groups             |
|     4 | `HAVING`   | Filter groups             |
|     5 | `SELECT`   | Choose the output columns |
|     6 | `ORDER BY` | Sort the result           |

---

# 2. Sample Database

The examples in this document use the following table.

## `students`

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(50),
    branch VARCHAR(30),
    age INT,
    marks DECIMAL(5,2),
    city VARCHAR(30)
);
```

### Sample Data

| student_id | name    | branch | age | marks | city    |
| ---------: | ------- | ------ | --: | ----: | ------- |
|          1 | Alice   | CSE    |  20 | 88.50 | Delhi   |
|          2 | Bob     | ECE    |  21 | 72.00 | Mumbai  |
|          3 | Charlie | CSE    |  22 | 91.00 | Delhi   |
|          4 | Diana   | ME     |  20 | 65.00 | Chennai |
|          5 | Eve     | CSE    |  21 | 88.50 | Mumbai  |
|          6 | Frank   | ECE    |  23 | 55.00 | Delhi   |
|          7 | Grace   | ME     |  22 | 78.00 | Kolkata |
|          8 | Hank    | CSE    |  20 | 91.00 | Chennai |

---

# 3. Select All Columns

### Question

Retrieve all records from `students`.

```sql
SELECT *
FROM students;
```

`*` means **all columns**.

### Exam Tip

`SELECT *` is convenient for learning and exploration, but explicitly naming the required columns is generally better for production queries.

---

# 4. Select Specific Columns

### Question

Retrieve the name, branch, and marks of every student.

```sql
SELECT name, branch, marks
FROM students;
```

### Result

| name    | branch | marks |
| ------- | ------ | ----: |
| Alice   | CSE    | 88.50 |
| Bob     | ECE    | 72.00 |
| Charlie | CSE    | 91.00 |
| Diana   | ME     | 65.00 |
| Eve     | CSE    | 88.50 |
| Frank   | ECE    | 55.00 |
| Grace   | ME     | 78.00 |
| Hank    | CSE    | 91.00 |

---

# 5. `WHERE` — Filtering Rows

The `WHERE` clause filters **individual rows** based on a condition.

### Question

Retrieve students from the CSE branch.

```sql
SELECT *
FROM students
WHERE branch = 'CSE';
```

### Result

| student_id | name    | branch | age | marks | city    |
| ---------: | ------- | ------ | --: | ----: | ------- |
|          1 | Alice   | CSE    |  20 | 88.50 | Delhi   |
|          3 | Charlie | CSE    |  22 | 91.00 | Delhi   |
|          5 | Eve     | CSE    |  21 | 88.50 | Mumbai  |
|          8 | Hank    | CSE    |  20 | 91.00 | Chennai |

---

# 6. Comparison Operators

| Operator | Meaning                  |
| -------- | ------------------------ |
| `=`      | Equal to                 |
| `!=`     | Not equal to             |
| `<>`     | Not equal to             |
| `>`      | Greater than             |
| `<`      | Less than                |
| `>=`     | Greater than or equal to |
| `<=`     | Less than or equal to    |

### Example

Find students who scored more than 80.

```sql
SELECT name, marks
FROM students
WHERE marks > 80;
```

### Result

| name    | marks |
| ------- | ----: |
| Alice   | 88.50 |
| Charlie | 91.00 |
| Eve     | 88.50 |
| Hank    | 91.00 |

---

# 7. `AND`, `OR`, and `NOT`

## `AND`

Both conditions must be true.

### Question

Find CSE students who scored more than 85.

```sql
SELECT name, branch, marks
FROM students
WHERE branch = 'CSE'
  AND marks > 85;
```

---

## `OR`

At least one condition must be true.

### Question

Find students from CSE or ECE.

```sql
SELECT name, branch
FROM students
WHERE branch = 'CSE'
   OR branch = 'ECE';
```

---

## `NOT`

Negates a condition.

### Question

Find students who are not from CSE.

```sql
SELECT name, branch
FROM students
WHERE NOT branch = 'CSE';
```

### Exam Tip

Parentheses are useful when combining `AND` and `OR` to make the intended logic explicit.

```sql
WHERE (branch = 'CSE' OR branch = 'ECE')
  AND marks > 70;
```

---

# 8. `BETWEEN`

`BETWEEN` checks whether a value falls within a range.

**Both endpoints are included.**

### Question

Find students with marks from 70 to 90.

```sql
SELECT name, marks
FROM students
WHERE marks BETWEEN 70 AND 90;
```

This is equivalent to:

```sql
WHERE marks >= 70
  AND marks <= 90;
```

### Exam Trap

```sql
BETWEEN 70 AND 90
```

includes both `70` and `90`.

---

# 9. `IN`

`IN` checks whether a value matches **any value in a list**.

### Question

Find students from Delhi, Mumbai, or Kolkata.

```sql
SELECT name, city
FROM students
WHERE city IN ('Delhi', 'Mumbai', 'Kolkata');
```

This is shorter than:

```sql
WHERE city = 'Delhi'
   OR city = 'Mumbai'
   OR city = 'Kolkata';
```

You can also use:

```sql
WHERE city NOT IN ('Delhi', 'Mumbai');
```

---

# 10. `LIKE` — Pattern Matching

`LIKE` is used for pattern matching, usually with string columns.

| Wildcard | Meaning                 |
| -------- | ----------------------- |
| `%`      | Zero or more characters |
| `_`      | Exactly one character   |

## Starts with a character

Find names starting with `A`.

```sql
SELECT name
FROM students
WHERE name LIKE 'A%';
```

Result:

```text
Alice
```

---

## Ends with a character

Find names ending with `e`.

```sql
SELECT name
FROM students
WHERE name LIKE '%e';
```

Result:

```text
Alice
Charlie
Eve
Grace
```

---

## Exactly three characters

```sql
SELECT name
FROM students
WHERE name LIKE '___';
```

Result:

```text
Bob
Eve
```

---

## Character position

Find names whose **second character is `a`**.

```sql
SELECT name
FROM students
WHERE name LIKE '_a%';
```

Result:

```text
Charlie
Diana
Hank
```

> `_` represents exactly one character, so `_a%` means: one character, followed by `a`, followed by zero or more characters.

---

# 11. `NULL`

`NULL` represents a **missing or unknown value**.

You cannot reliably test for `NULL` using `=`.

### Correct

```sql
SELECT *
FROM students
WHERE city IS NULL;
```

### Incorrect

```sql
SELECT *
FROM students
WHERE city = NULL;
```

Use:

```sql
IS NULL
```

or:

```sql
IS NOT NULL
```

### Exam Trap

Remember:

```sql
column = NULL       -- Wrong
column IS NULL      -- Correct
column IS NOT NULL  -- Correct
```

---

# 12. `DISTINCT`

`DISTINCT` removes duplicate combinations from the result.

### Question

Find all unique branches.

```sql
SELECT DISTINCT branch
FROM students;
```

Result:

| branch |
| ------ |
| CSE    |
| ECE    |
| ME     |

### Count Unique Values

```sql
SELECT COUNT(DISTINCT branch) AS total_branches
FROM students;
```

Result:

```text
3
```

### Important

With multiple columns:

```sql
SELECT DISTINCT branch, city
FROM students;
```

`DISTINCT` applies to the **combination of `branch` and `city`**, not each column independently.

---

# 13. `ORDER BY`

`ORDER BY` sorts query results.

Default ordering is:

```sql
ASC
```

### Ascending

```sql
SELECT name, marks
FROM students
ORDER BY marks ASC;
```

### Descending

```sql
SELECT name, marks
FROM students
ORDER BY marks DESC;
```

### Multiple Sort Columns

Sort by branch alphabetically and marks from highest to lowest within each branch.

```sql
SELECT name, branch, marks
FROM students
ORDER BY branch ASC, marks DESC;
```

### Exam Tip

You can often use a selected column's alias in `ORDER BY`.

```sql
SELECT name, marks AS score
FROM students
ORDER BY score DESC;
```

---

# 14. Limiting Rows

The syntax depends on the database system.

| Database   | Common Syntax             |
| ---------- | ------------------------- |
| MySQL      | `LIMIT n`                 |
| PostgreSQL | `LIMIT n`                 |
| SQL Server | `TOP n`                   |
| Oracle     | `FETCH FIRST n ROWS ONLY` |

### MySQL / PostgreSQL

Find the top 3 students by marks.

```sql
SELECT name, marks
FROM students
ORDER BY marks DESC
LIMIT 3;
```

### Important

Always use `ORDER BY` when "top N" has a ranking meaning.

---

# 15. Aggregate Functions

Aggregate functions calculate a value from multiple rows.

| Function  | Purpose           |
| --------- | ----------------- |
| `COUNT()` | Count rows/values |
| `SUM()`   | Calculate total   |
| `AVG()`   | Calculate average |
| `MAX()`   | Find maximum      |
| `MIN()`   | Find minimum      |

### Example

```sql
SELECT
    COUNT(*) AS total_students,
    AVG(marks) AS average_marks,
    MAX(marks) AS highest_marks,
    MIN(marks) AS lowest_marks,
    SUM(marks) AS total_marks
FROM students;
```

### Result

| total_students | average_marks | highest_marks | lowest_marks | total_marks |
| -------------: | ------------: | ------------: | -----------: | ----------: |
|              8 |        78.625 |         91.00 |        55.00 |      629.00 |

---

# 16. `GROUP BY`

`GROUP BY` combines rows with the same value into groups.

It is commonly used with aggregate functions.

### Question

Count students in each branch.

```sql
SELECT
    branch,
    COUNT(*) AS student_count
FROM students
GROUP BY branch;
```

### Result

| branch | student_count |
| ------ | ------------: |
| CSE    |             4 |
| ECE    |             2 |
| ME     |             2 |

---

## Average Marks by Branch

```sql
SELECT
    branch,
    AVG(marks) AS avg_marks
FROM students
GROUP BY branch;
```

### Result

| branch | avg_marks |
| ------ | --------: |
| CSE    |     89.50 |
| ECE    |     63.50 |
| ME     |     71.50 |

> Note: CSE average is `(88.50 + 91.00 + 88.50 + 91.00) / 4 = 89.50`.

---

# 17. `HAVING`

`HAVING` filters **groups after `GROUP BY`**.

### `WHERE` vs `HAVING`

| Clause   | Filters           | Used            |
| -------- | ----------------- | --------------- |
| `WHERE`  | Individual rows   | Before grouping |
| `HAVING` | Groups/aggregates | After grouping  |

### Question

Find branches whose average marks are greater than 75.

```sql
SELECT
    branch,
    AVG(marks) AS avg_marks
FROM students
GROUP BY branch
HAVING AVG(marks) > 75;
```

### Result

| branch | avg_marks |
| ------ | --------: |
| CSE    |     89.50 |

---

## Multiple Conditions in `HAVING`

Find branches with more than 2 students and an average above 80.

```sql
SELECT
    branch,
    COUNT(*) AS student_count,
    AVG(marks) AS avg_marks
FROM students
GROUP BY branch
HAVING COUNT(*) > 2
   AND AVG(marks) > 80;
```

### Result

| branch | student_count | avg_marks |
| ------ | ------------: | --------: |
| CSE    |             4 |     89.50 |

---

# 18. Column Aliases — `AS`

An alias gives a temporary name to a column or calculated expression in the result.

```sql
SELECT
    name AS student_name,
    marks AS score,
    marks * 0.9 AS scaled_score
FROM students
WHERE branch = 'CSE';
```

Aliases make query results easier to understand.

---

# 19. Combining Multiple Clauses

SQL becomes powerful when multiple clauses are combined.

### Question

Find students from CSE or ECE who scored above 70, sort them by marks descending, and return the top 3.

```sql
SELECT
    name,
    branch,
    marks
FROM students
WHERE branch IN ('CSE', 'ECE')
  AND marks > 70
ORDER BY marks DESC
LIMIT 3;
```

### Logical Breakdown

1. `FROM` → read `students`
2. `WHERE` → keep CSE/ECE students with marks > 70
3. `SELECT` → return name, branch, and marks
4. `ORDER BY` → highest marks first
5. `LIMIT` → return only 3 rows

---

# 20. Quick Revision

| Feature    | Purpose                       | Example                  |
| ---------- | ----------------------------- | ------------------------ |
| `SELECT`   | Choose columns                | `SELECT name`            |
| `FROM`     | Choose table                  | `FROM students`          |
| `WHERE`    | Filter rows                   | `WHERE marks > 80`       |
| `DISTINCT` | Remove duplicate combinations | `SELECT DISTINCT branch` |
| `GROUP BY` | Create groups                 | `GROUP BY branch`        |
| `HAVING`   | Filter groups                 | `HAVING AVG(marks) > 75` |
| `ORDER BY` | Sort results                  | `ORDER BY marks DESC`    |
| `LIMIT`    | Restrict rows                 | `LIMIT 5`                |
| `AS`       | Create an alias               | `marks AS score`         |

---

# 21. Common Exam Traps

### 1. `WHERE` vs `HAVING`

```sql
WHERE marks > 80
```

filters individual rows.

```sql
HAVING AVG(marks) > 80
```

filters groups.

---

### 2. Never use `= NULL`

Wrong:

```sql
WHERE city = NULL;
```

Correct:

```sql
WHERE city IS NULL;
```

---

### 3. `BETWEEN` is inclusive

```sql
marks BETWEEN 70 AND 90
```

means:

```sql
marks >= 70 AND marks <= 90
```

---

### 4. `DISTINCT` applies to the selected combination

```sql
SELECT DISTINCT branch, city
FROM students;
```

removes duplicate **branch-city combinations**.

---

### 5. `ORDER BY` controls ranking

If you ask for the "top 3", use a meaningful `ORDER BY`.

```sql
ORDER BY marks DESC
LIMIT 3;
```

---

### 6. `LIKE` wildcards

```text
%  → zero or more characters
_  → exactly one character
```

---

# 22. Practice Questions

Try these without looking at the answers.

### Q1

Retrieve only the names and cities of all students.

### Q2

Find students whose marks are greater than or equal to 80.

### Q3

Find students from CSE or ME.

### Q4

Find students whose marks are between 60 and 85.

### Q5

Find students from Delhi or Mumbai using `IN`.

### Q6

Find students whose names start with `C`.

### Q7

List all unique cities.

### Q8

Sort students by age ascending and marks descending.

### Q9

Find the highest marks.

### Q10

Find the number of students in each branch.

### Q11

Find branches having more than 2 students.

### Q12

Find the average marks of each branch.

### Q13

Find branches whose average marks are greater than 70.

### Q14

Return the top 3 students by marks.

### Q15

Find CSE students whose marks are greater than 85 and sort them by marks descending.

---

# 23. Key Takeaways

Remember these relationships:

```text
SELECT  → What do I want to see?
FROM    → Where is the data?
WHERE   → Which rows do I want?
GROUP BY → How should I group the rows?
HAVING  → Which groups do I want?
ORDER BY → How should the result be sorted?
LIMIT   → How many rows do I want?
```

### Core Query Pattern

```sql
SELECT columns
FROM table
WHERE row_condition
GROUP BY columns
HAVING group_condition
ORDER BY columns
LIMIT n;
```

Not every query needs every clause.

---

## Recommended Learning Order

```text
SELECT
  ↓
WHERE
  ↓
Comparison Operators
  ↓
AND / OR / NOT
  ↓
BETWEEN / IN / LIKE
  ↓
NULL
  ↓
DISTINCT
  ↓
ORDER BY
  ↓
LIMIT
  ↓
Aggregate Functions
  ↓
GROUP BY
  ↓
HAVING
  ↓
Combining Clauses
```
