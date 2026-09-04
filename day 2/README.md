# Introduction to DBMS and SQL


## 1. History and Evolution of DBMS

### Timeline of Development

| Era | Period | Technology | Limitation |
|-----|--------|------------|------------|
| File Processing Systems | 1950s–1960s | Flat files, magnetic tapes | Data redundancy, no sharing |
| Hierarchical & Network DBMS | 1960s–1970s | IMS (IBM), CODASYL | Complex navigation, rigid structure |
| Relational DBMS (RDBMS) | 1970s–1980s | Codd's relational model, SQL | Performance overhead (then) |
| Object-Oriented DBMS | 1980s–1990s | OQL, persistent objects | Complexity, limited adoption |
| Web & Distributed DBMS | 1990s–2000s | Client-server, distributed DB | Network dependency |
| NoSQL & Big Data | 2000s–present | MongoDB, Cassandra, Hadoop | Eventual consistency trade-offs |

### Key Milestones
- **1970** — E.F. Codd publishes *"A Relational Model of Data for Large Shared Data Banks"* — foundation of RDBMS
- **1974** — IBM develops **SEQUEL** (later renamed SQL)
- **1979** — Oracle releases first commercial RDBMS
- **1986** — SQL becomes **ANSI standard**
- **1990s** — Rise of client-server architecture and enterprise DBMS (Oracle, DB2, SQL Server)

---

## 2. What is a DBMS?

> **Definition:** A **Database Management System (DBMS)** is a software system that enables users to define, create, maintain, and control access to a database. It acts as an interface between the user/application and the physical database.

**Examples:** MySQL, Oracle, PostgreSQL, SQL Server, MongoDB, DB2

### Database vs. DBMS vs. RDBMS

| Term | Definition |
|------|------------|
| **Database (DB)** | Organized collection of interrelated data stored persistently |
| **DBMS** | Software that manages the database (storage, retrieval, security) |
| **RDBMS** | A DBMS that organizes data into tables using the relational model |
| **Data** | Raw, unprocessed facts (e.g., `101`, `"Alice"`) |
| **Information** | Processed, meaningful data (e.g., *"Employee 101 is Alice"*) |

### Advantages of DBMS over File Systems

| File Processing System | DBMS |
|------------------------|------|
| Data redundancy and inconsistency | Controlled redundancy via normalization |
| No data sharing | Concurrent multi-user access |
| No standard access method | Standard query language (SQL) |
| Poor data integrity | Integrity constraints enforced |
| No security mechanism | Fine-grained access control |
| No crash recovery | Transaction management and recovery |
| Program-data dependency | Program-data independence |

---

## 3. Data Independence

> **Definition:** **Data Independence** is the ability to modify the schema definition at one level of the database system without affecting the schema definition at the next higher level.

This is one of the **most important advantages** of a DBMS and is made possible by the **three-level ANSI-SPARC architecture**.

### Types of Data Independence

#### Physical Data Independence (Lower Level)
> **Definition:** The ability to modify the **physical schema** (storage structures, file organization, indexes) without affecting the **logical/conceptual schema**.

- Example: Changing storage from sequential files to B+ tree indexes does not require changes to SQL queries or application logic.
- **More commonly achieved** in modern DBMS systems.

#### Logical Data Independence (Higher Level)
> **Definition:** The ability to modify the **conceptual schema** (adding/removing tables, columns) without affecting the **external schemas (user views)** or application programs.

- Example: Adding a new column `phone_number` to an `Employee` table should not break existing views or applications that do not use it.
- **Harder to achieve** because applications are tightly coupled to logical structure.

### Comparison Table

| Property | Physical Data Independence | Logical Data Independence |
|----------|---------------------------|--------------------------|
| **Layer affected** | Physical ↔ Conceptual | Conceptual ↔ External |
| **What changes** | Storage, indexing, file organization | Table structure, attributes, relationships |
| **Impact on users** | No impact on queries or views | No impact on user views (if managed) |
| **Ease of achievement** | Easier | Harder |
| **Handled by** | Storage Manager / OS mapping | View definitions and mappings |

---

## 4. Levels of Abstraction (Three-Level Architecture)

> **Definition:** The **ANSI/SPARC three-level architecture** separates the user's view of the database from the physical storage, providing data independence and security.

```
┌─────────────────────────────────────────────┐
│           EXTERNAL LEVEL                    │  ← User Views (View 1, View 2, ...)
│   (What individual users see)               │
├─────────────────────────────────────────────┤
│           CONCEPTUAL LEVEL                  │  ← Logical Structure (DBA's view)
│   (What data exists and relationships)      │
├─────────────────────────────────────────────┤
│           INTERNAL LEVEL                    │  ← Physical Storage
│   (How data is physically stored)           │
└─────────────────────────────────────────────┘
```

### Level 1 — External Level (View Level)

> **Definition:** The **external level** (also called the **view level**) is the highest level of abstraction. It describes how individual users or user groups see the data.

- Each user has a **customized view** of the database (called a **subschema** or **user view**)
- Multiple external views can exist simultaneously
- Hides irrelevant data and provides **security** (users only see what they are authorized to)
- Defined using **`CREATE VIEW`** in SQL

**Example:** A student sees only `name`, `grade`; an admin sees `name`, `grade`, `fee_paid`, `address`.

### Level 2 — Conceptual Level (Logical Level)

> **Definition:** The **conceptual level** describes the **logical structure of the entire database** — what data is stored, the relationships among data, constraints, and semantics — without concern for physical implementation.

- Maintained by the **Database Administrator (DBA)**
- Defines all **entities, attributes, relationships, constraints**
- Represented by the **conceptual schema** (logical schema)
- **Independent of physical storage** details

**Example:** The full `Employee`, `Department`, `Project` table definitions with their relationships and constraints.

### Level 3 — Internal Level (Physical Level)

> **Definition:** The **internal level** is the lowest level of abstraction. It describes **how data is physically stored** on the storage medium — file structures, indexes, storage allocation, access paths.

- Closest to physical storage (disk blocks, pages, B-trees, hash indexes)
- Managed by the DBMS's **storage engine**
- Described by the **internal schema**
- Concerned with performance and efficiency

**Example:** `Employee` data stored in a B+ tree indexed on `emp_id`, 8KB page size, stored on `/data/emp.dbf`.

### Three-Level Summary Table

| Feature | External Level | Conceptual Level | Internal Level |
|---------|---------------|-----------------|----------------|
| **Also called** | View level / User level | Logical level | Physical level |
| **Managed by** | End users / Application developers | DBA | DBMS / System designers |
| **Describes** | Individual user views | Entire logical structure | Physical storage details |
| **Schema name** | External schema / Subschema | Conceptual schema | Internal schema |
| **Data independence** | Logical data independence | — | Physical data independence |
| **Examples** | SQL Views | Table definitions, constraints | B-trees, file blocks, indexes |

---

## 5. Data Models

> **Definition:** A **data model** is a collection of conceptual tools for describing data, data relationships, data semantics, and consistency constraints.

Data models define **how data is organized, stored, and manipulated**.

### Classification of Data Models

```
Data Models
├── Object-Based (Conceptual) Models
│   ├── Entity-Relationship (ER) Model
│   ├── Object-Oriented Model
│   └── Semantic Model
├── Record-Based (Logical) Models
│   ├── Relational Model       ← Most Important
│   ├── Hierarchical Model
│   └── Network Model
└── Physical Models
    └── Unifying Model, Frame Memory Model
```

---

### 5.1 Relational Model

> **Definition:** The **relational model** represents data as a collection of **relations (tables)**, where each table consists of rows (**tuples**) and columns (**attributes**).

- Proposed by **E.F. Codd (1970)**
- Data is stored in **two-dimensional tables**
- Relationships are expressed via **foreign keys**
- Manipulated using **Relational Algebra** and **SQL**

**Example:**

| emp_id | emp_name | dept_id | salary |
|--------|----------|---------|--------|
| 101 | Alice | D01 | 60000 |
| 102 | Bob | D02 | 75000 |
| 103 | Carol | D01 | 55000 |

**Key Terminology:**

| Term | Meaning |
|------|---------|
| **Relation** | Table |
| **Tuple** | Row / Record |
| **Attribute** | Column / Field |
| **Domain** | Set of allowed values for an attribute |
| **Degree** | Number of attributes (columns) |
| **Cardinality** | Number of tuples (rows) |
| **Schema** | Structure / definition of the relation |
| **Instance** | Actual data stored at a given time |

---

### 5.2 Entity-Relationship (ER) Model

> **Definition:** The **ER model** is a high-level conceptual data model that describes data as **entities**, **attributes**, and **relationships** between entities.

- Proposed by **Peter Chen (1976)**
- Used during **database design phase** (not physical storage)
- Visualized using **ER diagrams**

**Core Components:**

| Component | Symbol (in diagram) | Definition |
|-----------|-------------------|------------|
| **Entity** | Rectangle | Real-world object with independent existence (e.g., `Student`, `Course`) |
| **Weak Entity** | Double Rectangle | Entity that depends on another entity for identification |
| **Attribute** | Ellipse | Property of an entity (e.g., `name`, `age`) |
| **Key Attribute** | Underlined Ellipse | Attribute that uniquely identifies an entity |
| **Multivalued Attribute** | Double Ellipse | Attribute with multiple values (e.g., `phone_numbers`) |
| **Derived Attribute** | Dashed Ellipse | Value derived from other attributes (e.g., `age` from `DOB`) |
| **Relationship** | Diamond | Association between entities |
| **Identifying Relationship** | Double Diamond | Relationship connecting weak entity to owner |

**ER Diagram (Text Representation):**
```
[STUDENT] ───── (enrolls in) ───── [COURSE]
   │                                   │
 student_id                        course_id
 name                               title
 dob                                credits
 age (derived)
```

---

### 5.3 Hierarchical Model

> **Definition:** The **hierarchical model** organizes data in a **tree-like structure** with a single root, where each parent can have multiple children but each child has **only one parent**.

- Developed by IBM (**IMS — Information Management System, 1968**)
- Navigation is top-down (parent to child)
- Relationships represented as **one-to-many (1:N)**

```
         [Company]
        /          \
  [Dept A]       [Dept B]
  /      \            \
[Emp 1] [Emp 2]    [Emp 3]
```

**Limitations:** Cannot represent many-to-many (M:N) relationships easily; data duplication required.

---

### 5.4 Network Model

> **Definition:** The **network model** is an extension of the hierarchical model where a child record can have **multiple parent records**, forming a **graph structure** (not just a tree).

- Developed by **CODASYL (Conference on Data Systems Languages, 1969)**
- Records are called **nodes**; links between records are called **sets**
- Supports **many-to-many** relationships

```
     [Student A] ────────────────┐
          │                      ▼
     [Course X]          [Course Y]
          │                      │
     [Student B] ────────────────┘
```

**Limitation:** Navigational access — programmers must know the physical data structure.

---

### 5.5 Object-Oriented Model

> **Definition:** The **object-oriented model** represents data as **objects** (similar to OOP), with each object having **attributes** (data) and **methods** (behavior), supporting concepts like **encapsulation, inheritance, and polymorphism**.

- Suitable for complex data types (images, video, spatial data)
- Examples: **ObjectStore**, **db4o**
- Combined with relational model → **Object-Relational DBMS** (e.g., PostgreSQL)

---

### Data Models Comparison Table

| Feature | Relational | Hierarchical | Network | Object-Oriented |
|---------|-----------|-------------|---------|-----------------|
| **Structure** | Tables | Tree | Graph | Objects |
| **Relationships** | Via foreign keys | 1:N (parent-child) | M:N supported | Via references |
| **Language** | SQL | DL/1 | CODASYL DML | OQL |
| **M:N support** | Yes (via junction table) | No | Yes | Yes |
| **Redundancy** | Minimal (normalized) | High | Moderate | Low |
| **Ease of use** | High | Low | Low | Moderate |
| **Example DBMS** | Oracle, MySQL | IBM IMS | IDMS | db4o, ObjectStore |

---

## 6. Structure of a DBMS

> **Definition:** The **structure (architecture) of a DBMS** refers to its internal components and how they interact to manage database operations efficiently and securely.

### 6.1 DBMS System Architecture

```
┌──────────────────────────────────────────────────────┐
│                   USERS / APPLICATIONS                │
│    (Naive Users, Programmers, Analysts, DBA)         │
└──────────────────┬───────────────────────────────────┘
                   │
         ┌─────────▼──────────┐
         │   Query Processor   │
         │  ┌───────────────┐  │
         │  │ DDL Compiler  │  │
         │  │ DML Compiler  │  │
         │  │ Query Parser  │  │
         │  │ Query Optimizer│ │
         │  └───────────────┘  │
         └─────────┬───────────┘
                   │
         ┌─────────▼──────────┐
         │  Storage Manager   │
         │  ┌───────────────┐  │
         │  │Auth & Integrity│ │
         │  │ Transaction Mgr│ │
         │  │   File Manager │ │
         │  │  Buffer Manager│ │
         │  └───────────────┘  │
         └─────────┬───────────┘
                   │
         ┌─────────▼──────────┐
         │   Physical Storage  │
         │  Data Files, Index  │
         │  Files, Data Dict.  │
         └────────────────────┘
```

---

### 6.2 Components of a DBMS

#### Query Processor

Translates high-level user queries into low-level instructions for the storage manager.

| Component | Function |
|-----------|----------|
| **DDL Compiler** | Processes Data Definition Language statements (`CREATE`, `ALTER`, `DROP`); updates data dictionary |
| **DML Compiler** | Translates DML queries (`SELECT`, `INSERT`, `UPDATE`, `DELETE`) into query evaluation plans |
| **Query Parser** | Parses and validates the syntax and semantics of SQL queries |
| **Query Optimizer** | Generates the most efficient execution plan for a query (cost-based optimization) |
| **Query Evaluation Engine** | Executes the query evaluation plan produced by the optimizer |

#### Storage Manager

Acts as the interface between low-level data stored in the database and application programs.

| Component | Function |
|-----------|----------|
| **Authorization & Integrity Manager** | Enforces access control and integrity constraints |
| **Transaction Manager** | Ensures atomicity and isolation of concurrent transactions |
| **File Manager** | Manages allocation of disk space and file structures |
| **Buffer Manager** | Manages the transfer of data between disk and main memory (cache) |

#### Physical Storage Structures

| Structure | Purpose |
|-----------|---------|
| **Data Files** | Store actual database content |
| **Data Dictionary (System Catalog)** | Stores metadata — schema definitions, user permissions, constraints |
| **Index Files** | Speed up data retrieval (B+ trees, hash indexes) |
| **Log Files** | Record transaction history for recovery purposes |
| **Statistical Data** | Used by query optimizer to estimate query costs |

---

### 6.3 Types of DBMS Users

| User Type | Description | Interface Used |
|-----------|-------------|----------------|
| **Naive Users** | Non-technical end users (ATM, bank tellers) | Application forms / menus |
| **Application Programmers** | Write applications using DML embedded in host languages | APIs (JDBC, ODBC), host language |
| **Sophisticated Users** | Analysts who write SQL queries directly | SQL prompt, reporting tools |
| **Database Administrators (DBA)** | Manage schema, users, performance, backups | DDL commands, admin tools |

---

### 6.4 Database Languages

> **DDL (Data Definition Language):** Used to define and modify the database structure/schema.

```sql
CREATE TABLE Employee (
    emp_id   INT PRIMARY KEY,
    emp_name VARCHAR(50) NOT NULL,
    dept_id  INT,
    salary   DECIMAL(10, 2)
);

ALTER TABLE Employee ADD COLUMN email VARCHAR(100);

DROP TABLE Employee;
```

> **DML (Data Manipulation Language):** Used to retrieve and manipulate data.

```sql
-- Retrieval (Query Language)
SELECT emp_name, salary FROM Employee WHERE dept_id = 1;

-- Insertion
INSERT INTO Employee VALUES (101, 'Alice', 1, 60000.00);

-- Update
UPDATE Employee SET salary = 65000 WHERE emp_id = 101;

-- Deletion
DELETE FROM Employee WHERE emp_id = 101;
```

> **DCL (Data Control Language):** Used to control access permissions.

```sql
GRANT SELECT, INSERT ON Employee TO user_alice;
REVOKE INSERT ON Employee FROM user_alice;
```

> **TCL (Transaction Control Language):** Used to manage transactions.

```sql
BEGIN TRANSACTION;
    UPDATE Account SET balance = balance - 500 WHERE acc_id = 1;
    UPDATE Account SET balance = balance + 500 WHERE acc_id = 2;
COMMIT;

ROLLBACK;  -- Undo if something fails
SAVEPOINT sp1;
```

### DBMS Language Summary

| Language | Full Form | Commands | Purpose |
|----------|-----------|----------|---------|
| **DDL** | Data Definition Language | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME` | Define schema |
| **DML** | Data Manipulation Language | `SELECT`, `INSERT`, `UPDATE`, `DELETE` | Manipulate data |
| **DCL** | Data Control Language | `GRANT`, `REVOKE` | Access control |
| **TCL** | Transaction Control Language | `COMMIT`, `ROLLBACK`, `SAVEPOINT` | Transaction control |

---
