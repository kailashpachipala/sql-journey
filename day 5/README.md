 Database Schema, Data Types & Table Definitions

---

## 1. Database Schema

### Definition

A **database schema** is the logical blueprint or structure of a database that defines how data is organized, stored, and related — without containing the actual data itself.

> **Simple explanation:** Schema = structure of the database (like a blueprint of a house, not the house itself).

### Schema vs Instance

| Feature | Schema | Instance |
|---|---|---|
| **Definition** | Structure/design of the database | Actual data stored at a point in time |
| **Changes** | Rarely changes | Changes frequently (with every insert/update/delete) |
| **Also called** | Intension | Extension |
| **Example** | Table names, column names, data types | The rows currently present in a table |

### Types of Schema (Three-Schema Architecture)

| Level | Schema Type | Description |
|---|---|---|
| **External Level** | External / View Schema | How individual users see the data (user views) |
| **Conceptual Level** | Conceptual Schema | Overall logical structure of the entire database |
| **Internal Level** | Internal / Physical Schema | How data is physically stored on disk |

> **Exam tip:** This is called the **ANSI/SPARC Three-Level Architecture**. It provides **data abstraction** and **data independence**.

---

## 2. Data Types in SQL

### Standard SQL Data Types

#### Numeric Types

| Data Type | Description | Example |
|---|---|---|
| `INT` / `INTEGER` | Whole numbers | `age INT` |
| `SMALLINT` | Smaller range integers | `quantity SMALLINT` |
| `BIGINT` | Large whole numbers | `population BIGINT` |
| `DECIMAL(p, s)` | Exact decimal — p digits total, s after decimal | `salary DECIMAL(10,2)` |
| `NUMERIC(p, s)` | Same as DECIMAL (interchangeable) | `price NUMERIC(8,2)` |
| `FLOAT(n)` | Approximate floating point | `weight FLOAT(5)` |
| `REAL` | Single-precision floating point | `rating REAL` |
| `DOUBLE PRECISION` | Double-precision floating point | `measurement DOUBLE PRECISION` |

#### Character / String Types

| Data Type | Description | Example |
|---|---|---|
| `CHAR(n)` | Fixed-length string — always stores exactly n characters | `gender CHAR(1)` |
| `VARCHAR(n)` | Variable-length string — stores up to n characters | `name VARCHAR(50)` |
| `TEXT` | Variable unlimited-length string | `description TEXT` |

> **Key difference:** `CHAR(10)` storing `'ABC'` pads it as `'ABC       '` (10 chars). `VARCHAR(10)` stores it as `'ABC'` (3 chars). `VARCHAR` is more storage-efficient for variable-length data.

#### Date and Time Types

| Data Type | Description | Example Value |
|---|---|---|
| `DATE` | Calendar date | `'2024-01-15'` |
| `TIME` | Time of day | `'14:30:00'` |
| `DATETIME` | Date and time combined | `'2024-01-15 14:30:00'` |
| `TIMESTAMP` | Date + time + timezone info | `'2024-01-15 14:30:00'` |
| `YEAR` | Year only (MySQL) | `2024` |

#### Boolean Type

| Data Type | Description |
|---|---|
| `BOOLEAN` / `BOOL` | Stores `TRUE` or `FALSE` (internally 1 or 0) |

#### Large Object (LOB) Types

| Data Type | Description |
|---|---|
| `BLOB` | **Binary Large Object** — images, audio, video |
| `CLOB` | **Character Large Object** — large text documents |

---

## 3. Constraints in SQL

**Constraints** are rules enforced on table columns to ensure data integrity and accuracy.

| Constraint | Description | Example |
|---|---|---|
| `NOT NULL` | Column cannot have NULL values | `name VARCHAR(50) NOT NULL` |
| `UNIQUE` | All values in column must be distinct | `email VARCHAR(100) UNIQUE` |
| `PRIMARY KEY` | Uniquely identifies each row — NOT NULL + UNIQUE | `student_id INT PRIMARY KEY` |
| `FOREIGN KEY` | Links a column to the PRIMARY KEY of another table | `dept_id INT REFERENCES dept(id)` |
| `CHECK` | Ensures values satisfy a given condition | `age INT CHECK (age >= 18)` |
| `DEFAULT` | Assigns a default value when none is provided | `status VARCHAR(10) DEFAULT 'Active'` |

> **Exam tip:** `PRIMARY KEY` = `NOT NULL` + `UNIQUE` combined. A table can have only **one** primary key but **multiple** unique keys.

---

## 4. CREATE TABLE Statement

### Syntax

```sql
CREATE TABLE table_name (
    column1 datatype [constraints],
    column2 datatype [constraints],
    ...
    [table_level_constraints]
);
```

### Example 1 — Simple Table

```sql
CREATE TABLE Student (
    student_id   INT            PRIMARY KEY,
    name         VARCHAR(100)   NOT NULL,
    age          INT            CHECK (age >= 17 AND age <= 30),
    email        VARCHAR(100)   UNIQUE,
    gender       CHAR(1),
    dob          DATE,
    gpa          DECIMAL(3, 2)  DEFAULT 0.00
);
```

### Example 2 — Table with Foreign Key (Column Level)

```sql
CREATE TABLE Department (
    dept_id   INT          PRIMARY KEY,
    dept_name VARCHAR(50)  NOT NULL,
    location  VARCHAR(50)
);

CREATE TABLE Employee (
    emp_id    INT           PRIMARY KEY,
    emp_name  VARCHAR(100)  NOT NULL,
    salary    DECIMAL(10,2) CHECK (salary > 0),
    dept_id   INT           REFERENCES Department(dept_id)
);
```

### Example 3 — Table with Table-Level Constraints

```sql
CREATE TABLE Enrollment (
    student_id  INT,
    course_id   INT,
    enroll_date DATE  NOT NULL,
    grade       CHAR(2),

    PRIMARY KEY (student_id, course_id),           -- Composite Primary Key
    FOREIGN KEY (student_id) REFERENCES Student(student_id),
    FOREIGN KEY (course_id)  REFERENCES Course(course_id)
);
```

> **Exam tip:** When a primary key is made up of **more than one column** (composite key), it **must** be defined at the **table level**, not the column level.

### Column-Level vs Table-Level Constraints

| Feature | Column Level | Table Level |
|---|---|---|
| **Defined** | Inline with the column | After all columns, separately |
| **Composite keys** | Not possible | Possible |
| **When to use** | Single-column constraints | Multi-column (composite) constraints |
| **Example** | `id INT PRIMARY KEY` | `PRIMARY KEY(col1, col2)` |

---

## 5. ALTER TABLE Statement

The `ALTER TABLE` statement is used to **modify an existing table's structure** without deleting it.

> **Exam tip:** `ALTER TABLE` modifies **structure (schema)**. It is a **DDL (Data Definition Language)** command.

### 5.1 ADD a Column

```sql
ALTER TABLE Student
ADD phone_number VARCHAR(15);
```

### 5.2 ADD Multiple Columns

```sql
ALTER TABLE Student
ADD address VARCHAR(200),
ADD pincode CHAR(6);
```

### 5.3 DROP a Column

```sql
ALTER TABLE Employee
DROP COLUMN phone_number;
```

### 5.4 MODIFY / ALTER a Column's Data Type or Size

```sql
-- MySQL Syntax
ALTER TABLE Student
MODIFY name VARCHAR(150) NOT NULL;

-- Standard SQL / PostgreSQL Syntax
ALTER TABLE Student
ALTER COLUMN name TYPE VARCHAR(150);
```

### 5.5 RENAME a Column

```sql
-- MySQL 8.0+ / PostgreSQL
ALTER TABLE Student
RENAME COLUMN name TO full_name;
```

### 5.6 RENAME the Table

```sql
-- MySQL
ALTER TABLE Student
RENAME TO StudentInfo;

-- Oracle / Standard SQL
RENAME Student TO StudentInfo;
```

### 5.7 ADD a Constraint

```sql
-- Add a NOT NULL constraint
ALTER TABLE Employee
MODIFY salary DECIMAL(10,2) NOT NULL;

-- Add a CHECK constraint
ALTER TABLE Employee
ADD CONSTRAINT chk_salary CHECK (salary > 10000);

-- Add a FOREIGN KEY
ALTER TABLE Employee
ADD CONSTRAINT fk_dept
FOREIGN KEY (dept_id) REFERENCES Department(dept_id);

-- Add a UNIQUE constraint
ALTER TABLE Student
ADD CONSTRAINT uq_email UNIQUE (email);
```

### 5.8 DROP a Constraint

```sql
-- Drop a named constraint
ALTER TABLE Employee
DROP CONSTRAINT chk_salary;

-- Drop PRIMARY KEY (MySQL)
ALTER TABLE Student
DROP PRIMARY KEY;

-- Drop FOREIGN KEY (MySQL)
ALTER TABLE Employee
DROP FOREIGN KEY fk_dept;
```

### ALTER TABLE Operations — Quick Reference

| Operation | MySQL Syntax |
|---|---|
| Add column | `ALTER TABLE t ADD col datatype;` |
| Drop column | `ALTER TABLE t DROP COLUMN col;` |
| Modify column type | `ALTER TABLE t MODIFY col new_datatype;` |
| Rename column | `ALTER TABLE t RENAME COLUMN old TO new;` |
| Rename table | `ALTER TABLE t RENAME TO new_name;` |
| Add constraint | `ALTER TABLE t ADD CONSTRAINT name type;` |
| Drop constraint | `ALTER TABLE t DROP CONSTRAINT name;` |

---

## 6. DROP, TRUNCATE, and DELETE — Key Comparison

| Feature | `DROP` | `TRUNCATE` | `DELETE` |
|---|---|---|---|
| **Type** | DDL | DDL | DML |
| **What it does** | Removes entire table (structure + data) | Removes all rows, keeps structure | Removes specific or all rows |
| **WHERE clause** | Not applicable | Not supported | Supported |
| **Rollback possible** | No | No (in most DBMS) | Yes |
| **Resets AUTO_INCREMENT** | Yes (table gone) | Yes | No |
| **Speed** | Fast | Fast | Slow (row by row) |

```sql
DROP TABLE Student;          -- Deletes table entirely

TRUNCATE TABLE Student;      -- Deletes all rows, keeps table structure

DELETE FROM Student
WHERE student_id = 5;        -- Deletes specific rows
```

---

## 7. DDL vs DML vs DCL vs TCL — Quick Reference

| Category | Full Form | Commands | Purpose |
|---|---|---|---|
| **DDL** | Data Definition Language | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME` | Define/modify database structure |
| **DML** | Data Manipulation Language | `SELECT`, `INSERT`, `UPDATE`, `DELETE` | Manipulate actual data |
| **DCL** | Data Control Language | `GRANT`, `REVOKE` | Control access and permissions |
| **TCL** | Transaction Control Language | `COMMIT`, `ROLLBACK`, `SAVEPOINT` | Manage transactions |

---

## 8. Complete Worked Example

### Schema Design Problem

> Design tables for a University database with Students, Departments, and Courses.

```sql
-- Step 1: Create Department table first (referenced by others)
CREATE TABLE Department (
    dept_id   INT          PRIMARY KEY,
    dept_name VARCHAR(100) NOT NULL UNIQUE,
    hod_name  VARCHAR(100)
);

-- Step 2: Create Student table
CREATE TABLE Student (
    student_id INT           PRIMARY KEY,
    full_name  VARCHAR(100)  NOT NULL,
    dob        DATE          NOT NULL,
    gender     CHAR(1)       CHECK (gender IN ('M', 'F', 'O')),
    email      VARCHAR(100)  UNIQUE,
    dept_id    INT           REFERENCES Department(dept_id),
    gpa        DECIMAL(3,2)  DEFAULT 0.00 CHECK (gpa >= 0.00 AND gpa <= 10.00)
);

-- Step 3: Create Course table
CREATE TABLE Course (
    course_id   INT          PRIMARY KEY,
    course_name VARCHAR(100) NOT NULL,
    credits     INT          CHECK (credits BETWEEN 1 AND 6),
    dept_id     INT          REFERENCES Department(dept_id)
);

-- Step 4: Create Enrollment table (associative/junction table)
CREATE TABLE Enrollment (
    student_id  INT,
    course_id   INT,
    semester    VARCHAR(20)  NOT NULL,
    grade       CHAR(2),

    PRIMARY KEY (student_id, course_id, semester),
    FOREIGN KEY (student_id) REFERENCES Student(student_id)
        ON DELETE CASCADE,
    FOREIGN KEY (course_id)  REFERENCES Course(course_id)
        ON DELETE CASCADE
);
```

> **`ON DELETE CASCADE`** means: if a Student is deleted, all their Enrollment records are automatically deleted too. This maintains **referential integrity**.

### Alter the Schema (Post-Creation Modifications)

```sql
-- Add a phone column to Student
ALTER TABLE Student
ADD phone VARCHAR(15);

-- Increase length of full_name
ALTER TABLE Student
MODIFY full_name VARCHAR(150) NOT NULL;

-- Add an admission year column with default
ALTER TABLE Student
ADD admission_year YEAR DEFAULT 2024;

-- Add a constraint to ensure valid semester format
ALTER TABLE Enrollment
ADD CONSTRAINT chk_semester CHECK (semester IN ('Odd', 'Even'));
```

---

## 9. Exam Must-Know Points

- A **schema** defines structure; an **instance** is the actual data at a given time.
- `CHAR` is **fixed-length**; `VARCHAR` is **variable-length** — `VARCHAR` saves space.
- `PRIMARY KEY` = `NOT NULL` + `UNIQUE`. Only **one** per table.
- `FOREIGN KEY` enforces **referential integrity** between tables.
- **Composite primary keys** must be declared at the **table level**.
- `ALTER TABLE` is a **DDL** command — it modifies schema, not data.
- `DROP` removes the table permanently; `TRUNCATE` removes all data but keeps structure; `DELETE` removes rows conditionally.
- Always create **referenced tables** (parent) before **referencing tables** (child) to avoid foreign key errors.
- `ON DELETE CASCADE` automatically removes child records when the parent is deleted.
- `DEFAULT` is used when no value is explicitly provided during `INSERT`.