# Relational Model

---

## Introduction to the Relational Model

The **relational model** is a data model that represents data as a collection of **relations (tables)**, where each relation stores information about a specific entity or relationship. Proposed by **E.F. Codd in 1970**, it is the foundation of all modern relational database systems (RDBMS).

**Core idea:** All data is logically structured into two-dimensional tables called **relations**, and all operations on data are performed using these tables.

**Why it matters for exams:**
- It defines the mathematical foundation of SQL
- Every concept — keys, constraints, normalization — is built on top of it
- Understanding the relational model = understanding how databases work at their core

---

## Fundamental Concepts

### Domain

**Definition:** A **domain** is the set of all atomic (indivisible) values that are permitted for a given attribute.

- Domains define the **type** and **valid range** of data an attribute can hold
- Every attribute in a relation must draw its values from a defined domain
- Domains enforce data consistency at the most basic level

**Examples:**

| Domain Name | Description | Example Values |
|---|---|---|
| `INT_DOMAIN` | All positive integers | 1, 2, 3, 100 |
| `NAME_DOMAIN` | Strings up to 50 chars | "Alice", "Bob" |
| `DATE_DOMAIN` | Valid calendar dates | 2024-01-01 |
| `GENDER_DOMAIN` | Restricted set | 'M', 'F' |

> **Exam tip:** A domain is **not** the same as a data type. A domain adds semantic meaning. For example, `Age` and `StudentID` might both be integers, but they belong to **different domains** and should not be compared.

---

### Attribute

**Definition:** An **attribute** is a named column of a relation. Each attribute has a name and is associated with exactly one domain from which it draws its values.

- Attributes represent **properties or characteristics** of an entity
- Each attribute takes an **atomic** (single, indivisible) value from its domain in any given tuple
- The number of attributes in a relation is called its **degree** (or **arity**)

**Example:**

The relation `STUDENT` has attributes: `StudentID`, `Name`, `Age`, `Department`
- Degree = 4 (four attributes)

```
STUDENT(StudentID, Name, Age, Department)
         ─────────  ────  ───  ──────────
         Attribute  Attr  Attr   Attribute
```

> **Exam tip:** Attributes must be **atomic** — no multi-valued or composite values are allowed in a properly defined relation (this is enforced by 1NF).

---

### Tuple

**Definition:** A **tuple** is a single row in a relation. It is an ordered set of values, where each value is drawn from the domain of the corresponding attribute.

- Each tuple represents **one instance** of the entity described by the relation
- The number of tuples in a relation is called its **cardinality**
- Tuples in a relation are **unordered** — there is no first or last row in theory

**Example — STUDENT Relation:**

| StudentID | Name    | Age | Department |
|-----------|---------|-----|------------|
| 101       | Alice   | 20  | CS         |
| 102       | Bob     | 22  | IT         |
| 103       | Charlie | 21  | CS         |

- Each row above is one **tuple**
- Cardinality = 3 (three tuples)
- Degree = 4 (four attributes)

---

### Relation

**Definition:** A **relation** is a two-dimensional table consisting of a set of tuples, where each tuple shares the same set of attributes. Formally, a relation $R$ on domains $D_1, D_2, ..., D_n$ is a subset of the Cartesian product $D_1 \times D_2 \times ... \times D_n$.

A relation has two components:

**1. Relation Schema (Intension):**
- Defines the **structure** — the relation name and its attributes
- Does not change frequently
- Example: `STUDENT(StudentID, Name, Age, Department)`

**2. Relation Instance (Extension):**
- The **actual data** (set of tuples) at a given point in time
- Changes as data is inserted, updated, or deleted

#### Properties of a Relation

| Property | Description |
|---|---|
| No duplicate tuples | Every tuple is unique; no two rows are identical |
| Tuples are unordered | The order of rows does not matter |
| Attributes are unordered | The order of columns does not matter |
| All attribute values are atomic | Each cell holds exactly one indivisible value |
| Each attribute has a distinct name | No two columns share the same name |
| Attribute values are from a domain | Every value belongs to the defined domain |

---

### Relationship Between the Concepts

```
RELATION (Table)
│
├── SCHEMA  →  STUDENT(StudentID, Name, Age, Department)
│                        │          │     │       │
│                    ATTRIBUTE  ATTRIBUTE  ...  ATTRIBUTE
│                        │
│                    Drawn from DOMAIN (e.g., positive integers)
│
└── INSTANCE → Set of TUPLES (rows with actual data values)
```

---

## Null Values

### What is a Null Value?

**Definition:** A **null value** represents the **absence of a known value** for an attribute in a tuple. It is not the same as zero, an empty string, or a blank space — it means the value is **unknown, missing, or not applicable**.

### Why Null Values Exist — Three Interpretations

| Interpretation | Meaning | Example |
|---|---|---|
| **Unknown** | Value exists but is not known | Date of birth not provided |
| **Not applicable** | Attribute doesn't apply to this tuple | `MiddleName` for someone with no middle name |
| **Withheld** | Value exists but is intentionally hidden | Salary not disclosed |

### Importance of Null Values

- Null values allow the database to represent **incomplete or missing information** realistically without using placeholder values
- Without nulls, designers would be forced to use fake values (e.g., 0, "N/A") which corrupt data semantics
- Nulls participate in **three-valued logic (TRUE, FALSE, UNKNOWN)** — any comparison with NULL yields UNKNOWN

**Three-Valued Logic with NULL:**

| Expression | Result |
|---|---|
| `NULL = NULL` | UNKNOWN (not TRUE) |
| `NULL > 5` | UNKNOWN |
| `NULL + 5` | NULL |
| `IS NULL` check | TRUE (correct way to test) |

```sql
-- WRONG way to check null
SELECT * FROM STUDENT WHERE Age = NULL;   -- Returns nothing (UNKNOWN)

-- CORRECT way to check null
SELECT * FROM STUDENT WHERE Age IS NULL;
```

### Problems Caused by Null Values

- **Aggregate functions:** `COUNT(*)` counts nulls, but `SUM()`, `AVG()` ignore them — can cause misleading results
- **Join operations:** Tuples with null foreign keys may be excluded from natural joins
- **Logic complexity:** Three-valued logic makes query predicates harder to reason about
- **Storage overhead:** Null-heavy tables waste space and complicate indexing

---

## Relational Model Constraints

**Definition:** **Constraints** are rules enforced by the DBMS to ensure the **accuracy, validity, and consistency** of data stored in a relation. They define what constitutes a valid database state.

> **Exam tip:** Constraints are checked **before** any data modification (INSERT, UPDATE, DELETE) is committed. If a constraint is violated, the operation is rejected.

---

### 1. Domain Constraints

**Definition:** A **domain constraint** specifies that the value of each attribute must be an **atomic value drawn from its defined domain** — the correct data type, format, and valid range.

**What they enforce:**
- Data type correctness (e.g., Age must be an integer, not a string)
- Valid range (e.g., Age must be between 0 and 150)
- Format rules (e.g., phone number must be 10 digits)
- Atomicity (no sets or lists as attribute values)

```sql
CREATE TABLE STUDENT (
    StudentID   INT,
    Name        VARCHAR(50),
    Age         INT CHECK (Age >= 0 AND Age <= 150),   -- Domain constraint
    Gender      CHAR(1) CHECK (Gender IN ('M', 'F')),  -- Domain constraint
    CGPA        DECIMAL(3,2) CHECK (CGPA >= 0.0 AND CGPA <= 10.0)
);
```

**Importance:**
- First line of defense against invalid data
- Prevents garbage values from entering the database
- Enforced automatically by the DBMS on every INSERT and UPDATE

---

### 2. Key Constraints

**Definition:** A **key constraint** states that no two tuples in a relation can have the **same combination of values** for a designated set of attributes (the key). The key must **uniquely identify** every tuple.

First, understand the types of keys:

#### Types of Keys

| Key Type | Definition |
|---|---|
| **Superkey** | Any set of attributes that uniquely identifies a tuple. May contain redundant attributes. |
| **Candidate Key** | A **minimal superkey** — no proper subset of it is also a superkey. A relation can have multiple candidate keys. |
| **Primary Key** | The **chosen candidate key** used to uniquely identify tuples. Cannot be NULL. Only one per relation. |
| **Alternate Key** | Any candidate key that was **not** chosen as the primary key. |
| **Foreign Key** | An attribute in one relation that **references the primary key** of another relation. |
| **Composite Key** | A key made up of **two or more attributes** together. |

#### Key Constraint Rules

- A **primary key** value must be **unique** across all tuples — no two tuples can have the same primary key value
- A **primary key** attribute must **never be NULL** (this is also called the **Entity Integrity Constraint**)
- A relation must have **exactly one primary key**

```sql
CREATE TABLE STUDENT (
    StudentID   INT PRIMARY KEY,        -- Primary key: unique + not null
    Email       VARCHAR(100) UNIQUE,    -- Alternate/candidate key
    Name        VARCHAR(50) NOT NULL
);

-- Composite primary key example
CREATE TABLE ENROLLMENT (
    StudentID   INT,
    CourseID    INT,
    Grade       CHAR(2),
    PRIMARY KEY (StudentID, CourseID)   -- Composite key
);
```

**Superkey → Candidate Key → Primary Key (hierarchy):**

```
All Superkeys (e.g., {StudentID}, {Email}, {StudentID, Name}, ...)
        │
        ▼  (remove redundant attributes)
Candidate Keys (e.g., {StudentID}, {Email})
        │
        ▼  (DBA picks one)
Primary Key (e.g., {StudentID})
        │
        ▼  (remaining candidates become)
Alternate Keys (e.g., {Email})
```

---

### 3. Integrity Constraints

Integrity constraints are rules that maintain the **correctness and consistency** of data across the entire database. There are two primary integrity constraints in the relational model:

---

#### 3a. Entity Integrity Constraint

**Definition:** The **entity integrity constraint** states that **no attribute of a primary key can hold a NULL value** in any tuple of a relation.

**Why it matters:**
- A primary key exists to uniquely identify each tuple
- If the primary key is NULL, the tuple cannot be identified — it has no identity
- This would break the fundamental purpose of the primary key

**Rule:** For any relation $R$ with primary key $PK$: no tuple in $R$ can have `NULL` for any attribute in $PK$.

```sql
-- Entity Integrity is automatically enforced when you declare PRIMARY KEY
CREATE TABLE EMPLOYEE (
    EmpID   INT PRIMARY KEY,    -- EmpID can NEVER be NULL
    Name    VARCHAR(50)
);

-- This INSERT will FAIL — violates entity integrity
INSERT INTO EMPLOYEE VALUES (NULL, 'Alice');  -- ERROR
```

| Scenario | Valid? | Reason |
|---|---|---|
| `EmpID = 101` | ✅ Yes | Primary key has a value |
| `EmpID = NULL` | ❌ No | Primary key cannot be NULL |
| `Name = NULL` | ✅ Yes | Non-key attribute can be NULL |

---

#### 3b. Referential Integrity Constraint

**Definition:** The **referential integrity constraint** states that if a relation $R_1$ contains a **foreign key** referencing the primary key of relation $R_2$, then every foreign key value in $R_1$ must either:
1. **Match an existing primary key value** in $R_2$, OR
2. Be **NULL** (if the foreign key attribute allows nulls)

**Purpose:** Prevents **dangling references** — a tuple referencing a non-existent entity.

```sql
CREATE TABLE DEPARTMENT (
    DeptID   INT PRIMARY KEY,
    DeptName VARCHAR(50)
);

CREATE TABLE EMPLOYEE (
    EmpID    INT PRIMARY KEY,
    Name     VARCHAR(50),
    DeptID   INT,
    FOREIGN KEY (DeptID) REFERENCES DEPARTMENT(DeptID)
    -- Referential integrity: DeptID in EMPLOYEE must exist in DEPARTMENT
);
```

**What happens on a violation attempt:**

```sql
-- DEPARTMENT has DeptID: 10, 20, 30

INSERT INTO EMPLOYEE VALUES (1, 'Alice', 99);
-- ERROR: DeptID 99 does not exist in DEPARTMENT — Referential integrity violated
```

**DBMS Responses to Referential Integrity Violations:**

| Action | Description | SQL Syntax |
|---|---|---|
| `RESTRICT` / `NO ACTION` | Reject the operation | Default behavior |
| `CASCADE` | Propagate the change to child table | `ON DELETE CASCADE` |
| `SET NULL` | Set foreign key to NULL in child table | `ON DELETE SET NULL` |
| `SET DEFAULT` | Set foreign key to default value | `ON DELETE SET DEFAULT` |

```sql
-- Example: CASCADE delete
CREATE TABLE EMPLOYEE (
    EmpID   INT PRIMARY KEY,
    DeptID  INT,
    FOREIGN KEY (DeptID) REFERENCES DEPARTMENT(DeptID)
    ON DELETE CASCADE    -- If a dept is deleted, all its employees are deleted too
    ON UPDATE CASCADE    -- If DeptID changes, update it everywhere
);
```

---

### Summary: All Constraints at a Glance

| Constraint | What It Enforces | Applied On |
|---|---|---|
| **Domain Constraint** | Values must be atomic and from a valid domain/type/range | Individual attribute values |
| **Key Constraint** | Primary key values must be unique across all tuples | Primary key attribute(s) |
| **Entity Integrity** | Primary key attributes must never be NULL | Primary key of every relation |
| **Referential Integrity** | Foreign key values must match existing primary key values or be NULL | Foreign key references |

---

## Complete Example: Putting It All Together

```sql
-- Domain: DeptID is a positive integer, DeptName is a string
CREATE TABLE DEPARTMENT (
    DeptID   INT          PRIMARY KEY,          -- Key + Entity Integrity
    DeptName VARCHAR(100) NOT NULL UNIQUE        -- Domain + Key (alternate)
);

-- Domain: EmpID positive int, Name string, Age 18-65, Salary positive
CREATE TABLE EMPLOYEE (
    EmpID    INT          PRIMARY KEY,                          -- Entity Integrity
    Name     VARCHAR(50)  NOT NULL,                             -- Domain
    Age      INT          CHECK (Age BETWEEN 18 AND 65),        -- Domain
    Salary   DECIMAL(10,2) CHECK (Salary > 0),                  -- Domain
    DeptID   INT,
    FOREIGN KEY (DeptID) REFERENCES DEPARTMENT(DeptID)          -- Referential Integrity
    ON DELETE SET NULL
    ON UPDATE CASCADE
);
```

**Resulting Relation Instance:**

**DEPARTMENT:**

| DeptID | DeptName |
|--------|----------|
| 10     | CS       |
| 20     | IT       |

**EMPLOYEE:**

| EmpID | Name  | Age | Salary   | DeptID |
|-------|-------|-----|----------|--------|
| 1     | Alice | 25  | 50000.00 | 10     |
| 2     | Bob   | 30  | 60000.00 | 20     |
| 3     | Carol | 28  | NULL     | 10     |
| 4     | Dave  | 35  | 45000.00 | NULL   |

- Row 3: `Salary = NULL` → valid (non-key, nullable attribute)
- Row 4: `DeptID = NULL` → valid (foreign key can be null if not declared NOT NULL)
- Attempting to insert `DeptID = 99` → **rejected** (referential integrity violation)
- Attempting to insert `EmpID = NULL` → **rejected** (entity integrity violation)

---

## Quick Revision Summary

| Concept | One-Line Definition |
|---|---|
| **Domain** | Set of all valid atomic values for an attribute |
| **Attribute** | Named column of a relation with an associated domain |
| **Tuple** | A single row; one instance of the entity |
| **Relation** | A table = schema (structure) + instance (data) |
| **Null** | Absent, unknown, or inapplicable value — NOT zero or blank |
| **Domain Constraint** | Attribute value must be atomic and from its domain |
| **Key Constraint** | Primary key must uniquely identify every tuple |
| **Entity Integrity** | No attribute of a primary key can be NULL |
| **Referential Integrity** | Foreign key must reference an existing primary key or be NULL |