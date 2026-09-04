# SQL Data Operations & Basic Querying

---

## DQL vs DML — Overview & Comparison

**DQL (Data Query Language)** — SQL commands used to *retrieve/fetch* data from the database. These commands do **not** modify the database.

**DML (Data Manipulation Language)** — SQL commands used to *insert, update, or delete* data in the database. These commands **modify** the database content.

| Feature | DQL | DML |
|---|---|---|
| Full Form | Data Query Language | Data Manipulation Language |
| Purpose | Retrieve data | Manipulate (modify) data |
| Commands | `SELECT` | `INSERT`, `UPDATE`, `DELETE` |
| Modifies DB? | No | Yes |
| Rollback possible? | N/A | Yes (in transactions) |
| Auto-commit | N/A | Depends on DBMS |
| Example | `SELECT * FROM Student` | `INSERT INTO Student VALUES (...)` |

---

## Sample Table Used Throughout These Notes

```sql
CREATE TABLE Student (
    RollNo    INT PRIMARY KEY,
    Name      VARCHAR(50),
    Branch    VARCHAR(20),
    Marks     INT,
    City      VARCHAR(30)
);
```

**Data in `Student` table:**

| RollNo | Name    | Branch | Marks | City      |
|--------|---------|--------|-------|-----------|
| 101    | Alice   | CSE    | 88    | Delhi     |
| 102    | Bob     | ECE    | 72    | Mumbai    |
| 103    | Charlie | CSE    | 91    | Delhi     |
| 104    | Diana   | ME     | 65    | Chennai   |
| 105    | Evan    | ECE    | 58    | Mumbai    |
| 106    | Fiona   | CSE    | 45    | Delhi     |

---

# DML Operations

## INSERT

**Definition:** The `INSERT` statement is used to add new rows (records) into a table.

### Syntax

```sql
-- Insert with all columns (order must match table definition)
INSERT INTO table_name VALUES (val1, val2, ...);

-- Insert with specific columns (recommended practice)
INSERT INTO table_name (col1, col2, ...) VALUES (val1, val2, ...);
```

### Example — Single Row Insert

```sql
INSERT INTO Student (RollNo, Name, Branch, Marks, City)
VALUES (107, 'George', 'CSE', 79, 'Kolkata');
```

### Example — Multiple Row Insert

```sql
INSERT INTO Student VALUES
(108, 'Hannah', 'ME',  83, 'Pune'),
(109, 'Ivan',   'ECE', 67, 'Jaipur');
```

### Key Points
- Column count and data types must match exactly
- `PRIMARY KEY` column must be unique and `NOT NULL`
- String values must be enclosed in **single quotes** `'...'`
- Columns with `DEFAULT` or `NULL` allowed can be omitted when specifying column names

---

## UPDATE

**Definition:** The `UPDATE` statement is used to modify existing records in a table.

### Syntax

```sql
UPDATE table_name
SET col1 = val1, col2 = val2, ...
WHERE condition;
```

> **Exam Warning:** Without a `WHERE` clause, ALL rows are updated.

### Example — Update Single Row

```sql
UPDATE Student
SET Marks = 95
WHERE RollNo = 106;
```

**Effect:** Only Fiona's marks change from 45 → 95.

### Example — Update Multiple Columns

```sql
UPDATE Student
SET Marks = 70, City = 'Hyderabad'
WHERE Name = 'Bob';
```

### Example — Update Multiple Rows (by condition)

```sql
UPDATE Student
SET Marks = Marks + 5
WHERE Branch = 'ECE';
```

**Effect:** All ECE students get 5 bonus marks.

### Key Points
- `WHERE` clause is critical — always specify unless updating all rows intentionally
- Can update multiple columns in one `SET` clause using commas
- Arithmetic expressions are valid in `SET` (e.g., `Marks = Marks + 5`)

---

## DELETE

**Definition:** The `DELETE` statement is used to remove existing rows from a table based on a condition.

### Syntax

```sql
DELETE FROM table_name
WHERE condition;
```

> **Exam Warning:** Without `WHERE`, ALL rows are deleted (table remains, data is gone).

### Example — Delete Specific Row

```sql
DELETE FROM Student
WHERE RollNo = 105;
```

**Effect:** Evan's record is removed.

### Example — Delete by Condition

```sql
DELETE FROM Student
WHERE Marks < 50;
```

**Effect:** All students with marks below 50 are deleted.

### Example — Delete All Rows

```sql
DELETE FROM Student;   -- Deletes all data; table structure remains
```

### DELETE vs TRUNCATE vs DROP

| Feature | DELETE | TRUNCATE | DROP |
|---|---|---|---|
| Type | DML | DDL | DDL |
| Removes | Selected / all rows | All rows | Entire table |
| WHERE clause | Yes | No | No |
| Rollback | Yes | No (in most DBMS) | No |
| Table structure | Preserved | Preserved | Removed |
| Triggers fired? | Yes | No | No |

---

# DQL — SELECT Statement

## Basic SELECT (Projection & Selection)

**Definition:** The `SELECT` statement retrieves data from one or more tables. It implements two fundamental relational algebra operations:

- **Projection (π)** — choosing *which columns* to display
- **Selection (σ)** — choosing *which rows* to display (via `WHERE`)

### Syntax

```sql
SELECT column1, column2, ...   -- Projection
FROM table_name
WHERE condition;               -- Selection
```

---

## SELECT All Columns

```sql
SELECT * FROM Student;
```

**Output:**

| RollNo | Name    | Branch | Marks | City    |
|--------|---------|--------|-------|---------|
| 101    | Alice   | CSE    | 88    | Delhi   |
| 102    | Bob     | ECE    | 72    | Mumbai  |
| 103    | Charlie | CSE    | 91    | Delhi   |
| 104    | Diana   | ME     | 65    | Chennai |
| 105    | Evan    | ECE    | 58    | Mumbai  |
| 106    | Fiona   | CSE    | 45    | Delhi   |

> `*` is a wildcard that selects all columns. Avoid in production; fine in exams for brevity.

---

## Projection — Selecting Specific Columns

```sql
SELECT Name, Branch, Marks FROM Student;
```

**Output:**

| Name    | Branch | Marks |
|---------|--------|-------|
| Alice   | CSE    | 88    |
| Bob     | ECE    | 72    |
| Charlie | CSE    | 91    |
| Diana   | ME     | 65    |
| Evan    | ECE    | 58    |
| Fiona   | CSE    | 45    |

Only the specified columns are displayed — this is **projection**.

---

## DISTINCT — Remove Duplicates

```sql
SELECT DISTINCT Branch FROM Student;
```

**Output:**

| Branch |
|--------|
| CSE    |
| ECE    |
| ME     |

**`DISTINCT`** eliminates duplicate values in the result.

---

## Column Aliases — AS

```sql
SELECT Name AS StudentName, Marks AS TotalMarks FROM Student;
```

**Output:**

| StudentName | TotalMarks |
|-------------|------------|
| Alice       | 88         |
| Bob         | 72         |
| ...         | ...        |

`AS` renames a column in the output — does **not** change the table.

---

# WHERE Clause — Selection

**Definition:** The `WHERE` clause filters rows based on a specified condition. Only rows satisfying the condition are included in the result.

### Syntax

```sql
SELECT columns FROM table_name WHERE condition;
```

### Example — Simple WHERE

```sql
SELECT * FROM Student
WHERE Branch = 'CSE';
```

**Output:**

| RollNo | Name    | Branch | Marks | City  |
|--------|---------|--------|-------|-------|
| 101    | Alice   | CSE    | 88    | Delhi |
| 103    | Charlie | CSE    | 91    | Delhi |
| 106    | Fiona   | CSE    | 45    | Delhi |

---

# Arithmetic Operations in SQL

**Arithmetic operators** are used in `SELECT` or `WHERE` clauses to perform calculations on column values.

| Operator | Meaning        | Example              |
|----------|----------------|----------------------|
| `+`      | Addition       | `Marks + 10`         |
| `-`      | Subtraction    | `Marks - 5`          |
| `*`      | Multiplication | `Marks * 2`          |
| `/`      | Division       | `Marks / 2`          |
| `%`      | Modulus        | `Marks % 10`         |

### Example — Arithmetic in SELECT

```sql
SELECT Name, Marks, Marks + 10 AS BonusMarks
FROM Student;
```

**Output:**

| Name    | Marks | BonusMarks |
|---------|-------|------------|
| Alice   | 88    | 98         |
| Bob     | 72    | 82         |
| Charlie | 91    | 101        |
| Diana   | 65    | 75         |
| Evan    | 58    | 68         |
| Fiona   | 45    | 55         |

> Arithmetic in `SELECT` creates a **computed column** — the actual table data is unchanged.

### Example — Arithmetic in WHERE

```sql
SELECT Name, Marks
FROM Student
WHERE Marks * 2 > 150;
```

Retrieves students where double their marks exceeds 150 (i.e., Marks > 75).

**Output:**

| Name    | Marks |
|---------|-------|
| Alice   | 88    |
| Charlie | 91    |

---

# Logical / Comparison Operations in SQL

## Comparison Operators

| Operator | Meaning                  | Example          |
|----------|--------------------------|------------------|
| `=`      | Equal to                 | `Marks = 88`     |
| `!=` or `<>` | Not equal to         | `Branch <> 'ME'` |
| `>`      | Greater than             | `Marks > 70`     |
| `<`      | Less than                | `Marks < 60`     |
| `>=`     | Greater than or equal to | `Marks >= 88`    |
| `<=`     | Less than or equal to    | `Marks <= 65`    |

---

## Logical Operators

**Logical operators** combine multiple conditions in a `WHERE` clause.

| Operator | Meaning | Evaluates to TRUE when... |
|---|---|---|
| `AND` | Logical AND | **Both** conditions are true |
| `OR` | Logical OR | **At least one** condition is true |
| `NOT` | Logical NOT | The condition is **false** |

---

### AND Operator

```sql
SELECT * FROM Student
WHERE Branch = 'CSE' AND Marks > 80;
```

**Output:**

| RollNo | Name    | Branch | Marks | City  |
|--------|---------|--------|-------|-------|
| 101    | Alice   | CSE    | 88    | Delhi |
| 103    | Charlie | CSE    | 91    | Delhi |

Both conditions must be satisfied simultaneously.

---

### OR Operator

```sql
SELECT * FROM Student
WHERE Branch = 'ME' OR Marks > 85;
```

**Output:**

| RollNo | Name    | Branch | Marks | City    |
|--------|---------|--------|-------|---------|
| 101    | Alice   | CSE    | 88    | Delhi   |
| 103    | Charlie | CSE    | 91    | Delhi   |
| 104    | Diana   | ME     | 65    | Chennai |

Either condition being true is sufficient.

---

### NOT Operator

```sql
SELECT * FROM Student
WHERE NOT Branch = 'CSE';

-- Equivalent to:
SELECT * FROM Student
WHERE Branch <> 'CSE';
```

**Output:**

| RollNo | Name  | Branch | Marks | City    |
|--------|-------|--------|-------|---------|
| 102    | Bob   | ECE    | 72    | Mumbai  |
| 104    | Diana | ME     | 65    | Chennai |
| 105    | Evan  | ECE    | 58    | Mumbai  |

---

## Special WHERE Operators

### BETWEEN — Range Check

```sql
SELECT * FROM Student
WHERE Marks BETWEEN 60 AND 90;
```

**Output:**

| RollNo | Name  | Branch | Marks | City    |
|--------|-------|--------|-------|---------|
| 101    | Alice | CSE    | 88    | Delhi   |
| 102    | Bob   | ECE    | 72    | Mumbai  |
| 104    | Diana | ME     | 65    | Chennai |

> `BETWEEN a AND b` is **inclusive** — equivalent to `Marks >= 60 AND Marks <= 90`.

---

### IN — Match Against a List

```sql
SELECT * FROM Student
WHERE Branch IN ('CSE', 'ME');
```

**Output:**

| RollNo | Name    | Branch | Marks | City    |
|--------|---------|--------|-------|---------|
| 101    | Alice   | CSE    | 88    | Delhi   |
| 103    | Charlie | CSE    | 91    | Delhi   |
| 104    | Diana   | ME     | 65    | Chennai |
| 106    | Fiona   | CSE    | 45    | Delhi   |

> `IN (list)` is a shorthand for multiple `OR` conditions.

---

### LIKE — Pattern Matching

| Wildcard | Meaning |
|---|---|
| `%` | Zero or more characters |
| `_` | Exactly one character |

```sql
-- Names starting with 'A'
SELECT * FROM Student WHERE Name LIKE 'A%';

-- Names ending with 'e'
SELECT * FROM Student WHERE Name LIKE '%e';

-- Names where second character is 'i'
SELECT * FROM Student WHERE Name LIKE '_i%';
```

**Example output for `Name LIKE 'A%'`:**

| RollNo | Name  | Branch | Marks | City  |
|--------|-------|--------|-------|-------|
| 101    | Alice | CSE    | 88    | Delhi |

---

### IS NULL / IS NOT NULL

```sql
-- Find students with no city recorded
SELECT * FROM Student WHERE City IS NULL;

-- Find students who have a city recorded
SELECT * FROM Student WHERE City IS NOT NULL;
```

> **Never** use `= NULL` — always use `IS NULL`. This is a common exam trap.

---

# Combining Everything — Complex Queries

### Query 1 — Arithmetic + AND + WHERE

```sql
SELECT Name, Marks, Marks + 5 AS AdjustedMarks
FROM Student
WHERE Branch = 'CSE' AND Marks + 5 > 90;
```

**Output:**

| Name    | Marks | AdjustedMarks |
|---------|-------|---------------|
| Alice   | CSE   | 93            |
| Charlie | CSE   | 96            |

---

### Query 2 — OR + BETWEEN

```sql
SELECT Name, Branch, Marks
FROM Student
WHERE Branch = 'ME' OR Marks BETWEEN 70 AND 80;
```

**Output:**

| Name  | Branch | Marks |
|-------|--------|-------|
| Bob   | ECE    | 72    |
| Diana | ME     | 65    |

---

### Query 3 — NOT + IN

```sql
SELECT Name, Branch
FROM Student
WHERE Branch NOT IN ('ECE', 'ME');
```

**Output:**

| Name    | Branch |
|---------|--------|
| Alice   | CSE    |
| Charlie | CSE    |
| Fiona   | CSE    |

---

# Operator Precedence in SQL

When multiple operators appear together, SQL evaluates them in this order (highest to lowest):

| Priority | Operator |
|---|---|
| 1 (Highest) | Arithmetic: `*`, `/`, `%` |
| 2 | Arithmetic: `+`, `-` |
| 3 | Comparison: `=`, `<>`, `>`, `<`, `>=`, `<=` |
| 4 | `NOT` |
| 5 | `AND` |
| 6 (Lowest) | `OR` |

> Always use **parentheses `()`** to make complex conditions unambiguous — both in exams and in practice.

```sql
-- Without parentheses — potentially ambiguous
SELECT * FROM Student WHERE Branch = 'CSE' OR Branch = 'ECE' AND Marks > 70;
-- AND is evaluated first, so this means: CSE (any marks) OR (ECE AND Marks > 70)

-- With parentheses — clear intent
SELECT * FROM Student WHERE (Branch = 'CSE' OR Branch = 'ECE') AND Marks > 70;
```

---

# Quick Revision Summary

| Concept | Command / Operator | Key Point |
|---|---|---|
| Insert rows | `INSERT INTO ... VALUES` | Column count & types must match |
| Update rows | `UPDATE ... SET ... WHERE` | Without `WHERE`, all rows update |
| Delete rows | `DELETE FROM ... WHERE` | Without `WHERE`, all rows delete |
| Retrieve all | `SELECT *` | Returns all columns and rows |
| Projection | `SELECT col1, col2` | Choose specific columns |
| Selection | `WHERE condition` | Filter rows |
| Arithmetic | `+`, `-`, `*`, `/`, `%` | Computes values; doesn't alter data |
| Range filter | `BETWEEN a AND b` | Inclusive on both ends |
| List filter | `IN (val1, val2)` | Shorthand for multiple `OR` |
| Pattern match | `LIKE 'A%'` | `%` = any chars, `_` = one char |
| Null check | `IS NULL` / `IS NOT NULL` | Never use `= NULL` |
| Combine conditions | `AND`, `OR`, `NOT` | AND binds tighter than OR |