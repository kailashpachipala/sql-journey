# Data Models, Schema & Database Architecture

---

## Table of Contents
1. [Data Models](#data-models)
2. [Schema, Instance & Data Independence](#schema-instance--data-independence)
3. [Three-Tier Schema Architecture](#three-tier-schema-architecture)
4. [Database System Structure & Environment](#database-system-structure--environment)
5. [Centralized vs Client-Server Architecture](#centralized-vs-client-server-architecture)

---

# 1. Data Models

**Definition:** A **Data Model** is a collection of conceptual tools used to describe data, data relationships, data semantics, and consistency constraints.

It answers: *"How is data organized and represented in a database?"*

## Types of Data Models

| Data Model | Description | Example / Usage |
|---|---|---|
| **Hierarchical** | Data organized as a tree (parent-child) | IBM IMS |
| **Network** | Data as graph — records linked via pointers | CODASYL |
| **Relational** | Data as 2D tables (relations) | MySQL, Oracle, PostgreSQL |
| **Entity-Relationship (ER)** | Conceptual model using entities and relationships | Used in DB design |
| **Object-Oriented** | Data as objects with attributes and methods | db4o, ObjectDB |
| **Object-Relational** | Hybrid of relational + object-oriented | PostgreSQL |
| **Semi-Structured** | Flexible schema, e.g. XML, JSON | MongoDB, XML DBs |
| **Flat File** | Simple text/CSV files, no structure enforcement | CSV, spreadsheets |

## Categories of Data Models

```
Data Models
├── High-Level (Conceptual) Models     → ER Model (close to user view)
├── Representational (Logical) Models  → Relational Model (used in most DBMS)
└── Low-Level (Physical) Models        → Describe how data is stored on disk
```

---

# 2. Schema, Instance & Data Independence

## 2.1 Schema

**Definition:** A **Schema** is the overall logical description (blueprint/structure) of the entire database. It defines what data is stored and how it is organized — but NOT the actual data itself.

- Schema is defined at **design time** and **rarely changes**
- Also called the **intension** of the database
- Described using a **Data Definition Language (DDL)**

### Example

```sql
-- This is a schema definition
CREATE TABLE Student (
    StudentID   INT          PRIMARY KEY,
    Name        VARCHAR(50)  NOT NULL,
    Department  VARCHAR(30),
    CGPA        DECIMAL(3,2)
);
```

The table structure above is the **schema** — it tells us what columns exist and their types, but contains no actual student records.

---

## 2.2 Instance

**Definition:** An **Instance** (also called a **Database State** or **snapshot**) is the actual data stored in the database at a **particular moment in time**.

- Instance changes **frequently** as data is inserted, updated, or deleted
- Also called the **extension** of the database

### Example

At time T₁, the Student table instance might be:

| StudentID | Name    | Department | CGPA |
|-----------|---------|------------|------|
| 101       | Alice   | CSE        | 9.1  |
| 102       | Bob     | ECE        | 8.4  |
| 103       | Charlie | ME         | 7.8  |

At time T₂ (after inserting a new row), the instance changes — but the **schema remains the same**.

---

## 2.3 Schema vs Instance — Comparison

| Feature | Schema | Instance |
|---|---|---|
| **Definition** | Structure/blueprint of the DB | Actual data at a point in time |
| **Analogy** | Variable declaration in a program | Value stored in that variable |
| **Changes** | Rarely (only on redesign) | Frequently (on every DML operation) |
| **Other Names** | Intension, metadata | Extension, database state, snapshot |
| **Defined by** | DDL (CREATE, ALTER) | DML (INSERT, UPDATE, DELETE) |

---

## 2.4 Data Independence

**Definition:** **Data Independence** is the ability to modify the schema at one level of the database without affecting the schema at the next higher level.

> It decouples the application from the internal storage, so changes at one level don't break the other levels.

### Two Types of Data Independence

---

### 2.4.1 Logical Data Independence

**Definition:** The ability to change the **conceptual (logical) schema** without having to change the **external schema** (user views) or application programs.

- Concerned with changes in **table structure** (adding/removing columns, splitting/merging tables)
- **Harder to achieve** because application programs are tightly coupled with logical structure

**Example:**
```
Before: Student(StudentID, Name, Department, CGPA)
After:  Student(StudentID, Name, CGPA) + StudentDept(StudentID, Department)

→ Views/application programs should still work without modification.
```

---

### 2.4.2 Physical Data Independence

**Definition:** The ability to change the **internal (physical) schema** without affecting the **conceptual schema** or application programs.

- Concerned with changes in **storage details** (indexing strategy, file organization, compression)
- **Easier to achieve** because most DBMS already abstract storage from logic

**Example:**
```
Changing from B-Tree index to Hash index,
or moving data from HDD to SSD,
or changing file block size

→ Logical schema and user applications remain unaffected.
```

---

### Summary Table — Data Independence

| Feature | Logical Data Independence | Physical Data Independence |
|---|---|---|
| **Level affected** | Conceptual schema changed | Internal schema changed |
| **Upper level safe** | External schema unchanged | Conceptual schema unchanged |
| **Difficulty** | Hard to achieve | Relatively easier |
| **Example change** | Add a new column to a table | Change indexing method |

---

# 3. Three-Tier Schema Architecture (ANSI/SPARC Model)

**Definition:** The **Three-Level/Three-Tier Schema Architecture** (proposed by ANSI/SPARC in 1975) separates the database into three levels of abstraction to achieve **data independence** and allow **multiple user views**.

```
┌─────────────────────────────────────────────┐
│         EXTERNAL LEVEL (View Level)          │  ← User/Application Layer
│   View 1 | View 2 | View 3 | ... | View N   │
└─────────────────────┬───────────────────────┘
                      │  Logical Data Independence
┌─────────────────────▼───────────────────────┐
│       CONCEPTUAL LEVEL (Logical Level)       │  ← DBA Layer
│    Full logical structure of the database    │
└─────────────────────┬───────────────────────┘
                      │  Physical Data Independence
┌─────────────────────▼───────────────────────┐
│        INTERNAL LEVEL (Physical Level)       │  ← Storage Layer
│  Actual physical storage — files, indexes    │
└─────────────────────────────────────────────┘
```

---

## Level 1 — External Level (View Level)

- **Highest** level of abstraction
- Describes how **individual users or user groups** see the data
- Each user sees only a **relevant subset** (view) of the entire database
- Multiple different views can exist simultaneously
- Hides irrelevant data and provides **data security** by restricting access

**Example:**
```sql
-- A student can only see their own record
CREATE VIEW StudentView AS
    SELECT Name, Department, CGPA
    FROM Student
    WHERE StudentID = CURRENT_USER_ID;

-- HR sees only Name and Department, not salary details of other departments
```

---

## Level 2 — Conceptual Level (Logical Level)

- **Middle** level — the "heart" of the three-tier architecture
- Describes **what data is stored** and the **relationships** among data for the whole DB
- Maintained by the **Database Administrator (DBA)**
- Contains the complete logical structure: all entities, attributes, relationships, constraints, and security rules
- **Hides physical storage details**

**Example:**
```
Conceptual schema describes:
- Student(StudentID, Name, Department, CGPA)
- Course(CourseID, CourseName, Credits)
- Enrolls(StudentID, CourseID, Grade)
- Constraints: StudentID is PK, CGPA between 0 and 10, etc.
```

---

## Level 3 — Internal Level (Physical Level)

- **Lowest** level of abstraction
- Describes **how data is physically stored** on the storage medium
- Deals with physical implementation: file structures, access paths, indexing, compression, encryption, record formats, block sizes
- Closest to actual hardware

**Example:**
```
Internal level describes:
- Student data stored in a B+ Tree indexed file
- Index on StudentID using hash indexing
- Records stored in 512-byte blocks
- Data compressed using LZ4 algorithm
```

---

## Comparison — Three Levels

| Feature | External Level | Conceptual Level | Internal Level |
|---|---|---|---|
| **Also called** | View level, User level | Logical level, Community level | Physical level, Storage level |
| **Who uses it** | End users, Application programs | DBA | System programmers, OS |
| **What it describes** | User-specific partial view | Entire logical DB structure | Physical storage details |
| **What it hides** | Rest of the DB, storage | Physical storage | Logical structure |
| **Language used** | External DDL / SQL Views | Conceptual DDL | Internal DDL |
| **Number** | Multiple (one per user group) | Single | Single |

---

## Mappings Between Levels

**Definition:** **Mappings** define how data at one level corresponds to data at another level. The DBMS uses these mappings to transform requests and results between levels.

```
External/Conceptual Mapping:
  → Transforms user view queries into conceptual schema queries
  → Supports LOGICAL DATA INDEPENDENCE

Conceptual/Internal Mapping:
  → Transforms conceptual queries into physical storage access
  → Supports PHYSICAL DATA INDEPENDENCE
```

---

# 4. Database System Structure & Environment

## 4.1 Components of a DBMS

```
┌──────────────────────────────────────────────────────────┐
│                    DATABASE SYSTEM                        │
│                                                          │
│  ┌─────────────┐        ┌──────────────────────────┐    │
│  │    Users    │        │    DBMS Software          │    │
│  │ ─ End Users │◄──────►│ ─ Query Processor         │    │
│  │ ─ DBA       │        │ ─ Storage Manager         │    │
│  │ ─ App Dev.  │        │ ─ Transaction Manager     │    │
│  └─────────────┘        │ ─ Authorization Manager   │    │
│                         └──────────┬───────────────┘    │
│                                    │                     │
│                         ┌──────────▼───────────┐        │
│                         │   Database (Storage)  │        │
│                         │ ─ Data                │        │
│                         │ ─ Data Dictionary     │        │
│                         │ ─ Indexes             │        │
│                         └──────────────────────┘        │
└──────────────────────────────────────────────────────────┘
```

---

## 4.2 DBMS Component Modules

### Query Processor Components

| Component | Function |
|---|---|
| **DDL Interpreter** | Interprets DDL instructions and records definitions in the data dictionary |
| **DML Compiler** | Translates DML queries into low-level instructions for the Query Evaluation Engine |
| **Query Evaluation Engine** | Executes the low-level instructions generated by the DML compiler |
| **Embedded DML Pre-compiler** | Converts DML statements embedded in application code into procedure calls |

### Storage Manager Components

| Component | Function |
|---|---|
| **Authorization & Integrity Manager** | Tests for integrity constraints and checks user authority |
| **Transaction Manager** | Ensures atomicity and isolation; handles concurrent transactions |
| **File Manager** | Manages allocation of disk space and data structures used to store information |
| **Buffer Manager** | Responsible for fetching data from disk to main memory; decides what to cache |

---

## 4.3 Data Dictionary (System Catalog)

**Definition:** The **Data Dictionary** (also called **System Catalog**) is a repository that stores **metadata** — data about data — including schema definitions, constraints, user privileges, and statistics.

- Stores: table names, column names, data types, constraints, indexes, views, user privileges
- Queried by the DBMS itself before executing any user query
- Also accessible (partially) by DBAs and developers

```sql
-- Example: Viewing metadata in SQL (varies by DBMS)
-- In MySQL:
DESCRIBE Student;
SHOW COLUMNS FROM Student;

-- In PostgreSQL:
SELECT column_name, data_type
FROM information_schema.columns
WHERE table_name = 'student';
```

---

## 4.4 Types of Database Users

| User Type | Role | Interaction Method |
|---|---|---|
| **Naive / End Users** | Routine operations (bank tellers, airline clerks) | Forms-based GUI applications |
| **Application Programmers** | Write programs using DML embedded in host language | Pro*C, JDBC, ODBC, Python |
| **Sophisticated Users** | Write complex ad hoc queries | SQL directly |
| **Database Administrator (DBA)** | Full control: schema definition, tuning, backup | DDL + admin tools |
| **Database Designer** | Designs conceptual and logical schema | ER tools, DDL |

---

# 5. Centralized vs Client-Server Architecture

## 5.1 Centralized Database Architecture

**Definition:** In **Centralized Architecture**, both the DBMS software and the database itself reside on a **single central machine (mainframe or server)**. All processing is done on this central system.

```
┌────────────────────────────────────┐
│         Central System             │
│  ┌──────────────────────────────┐  │
│  │    DBMS + Application Logic  │  │
│  │    + Database Storage        │  │
│  └──────────────────────────────┘  │
│        ▲          ▲         ▲      │
└────────┼──────────┼─────────┼──────┘
         │          │         │
    Terminal    Terminal   Terminal
    (Dumb)      (Dumb)     (Dumb)
```

- **Terminals** (dumb clients) are only used for input/output — **no local processing**
- All computation done at the central machine
- Used in **mainframe-based** legacy systems

### Advantages
- Easy to maintain and administer
- Strong centralized security and control
- No data redundancy issues

### Disadvantages
- **Single point of failure** — if central machine crashes, entire system goes down
- Poor **scalability** — adding users strains the single machine
- High cost of mainframe hardware
- Network bottleneck as all data travels to/from one point

---

## 5.2 Client-Server Architecture

**Definition:** In **Client-Server Architecture**, the processing is distributed. The **client** handles the user interface and application logic, while the **server** manages the database and query processing.

```
┌────────────┐         ┌────────────────────────┐
│   CLIENT   │ ──────► │        SERVER           │
│            │ Request │  ┌──────────────────┐   │
│ ─ UI Layer │         │  │   DBMS Software  │   │
│ ─ App Logic│ ◄────── │  │   + Database     │   │
└────────────┘ Result  │  └──────────────────┘   │
                        └────────────────────────┘

Multiple clients can connect to the same server simultaneously.
```

---

## 5.3 Two-Tier Client-Server Architecture

```
┌───────────────┐          ┌───────────────────────┐
│ CLIENT (Tier 1)│◄────────►│  DATABASE SERVER (T2)  │
│               │  SQL/API  │                        │
│ - UI          │           │ - Query Processing     │
│ - Business    │           │ - Transaction Mgmt     │
│   Logic       │           │ - Data Storage         │
└───────────────┘           └───────────────────────┘
```

- Client sends SQL queries directly to the DB server
- Server processes and returns results
- **Simpler** but **less scalable** — business logic is on the client, hard to update across many clients

---

## 5.4 Three-Tier Client-Server Architecture

**Definition:** In **Three-Tier Architecture**, there are three distinct layers — Presentation, Application (Business Logic), and Data — each on a separate tier.

```
┌──────────────┐      ┌─────────────────────┐      ┌──────────────────┐
│  CLIENT TIER  │      │  APPLICATION SERVER  │      │  DATABASE SERVER  │
│  (Tier 1)    │◄────►│     (Tier 2)         │◄────►│    (Tier 3)       │
│              │ HTTP │                      │ SQL  │                  │
│ - Browser    │      │ - Business Logic     │      │ - DBMS           │
│ - Mobile App │      │ - App Processing     │      │ - Data Storage   │
│ - Desktop UI │      │ - API Layer          │      │ - Query Engine   │
└──────────────┘      └─────────────────────┘      └──────────────────┘
```

- **Tier 1 (Presentation):** User interface — browser, mobile app
- **Tier 2 (Application/Middle):** Business logic, API calls, application rules
- **Tier 3 (Data):** DBMS + physical database

### Advantages of Three-Tier
- **Scalability** — each tier can be scaled independently
- **Maintainability** — business logic changes don't affect client or database
- **Security** — clients never directly access the database
- **Reusability** — multiple clients (web, mobile, desktop) share same middle tier

---

## 5.5 Comparison — Centralized vs Client-Server

| Feature | Centralized | Two-Tier C/S | Three-Tier C/S |
|---|---|---|---|
| **Processing location** | Single machine | Client + DB Server | Client + App Server + DB |
| **Scalability** | Poor | Moderate | Excellent |
| **Fault tolerance** | Very low | Moderate | High |
| **Maintenance** | Simple | Moderate | Complex |
| **Security** | Centralized, easy | Moderate | High — DB never exposed |
| **Cost** | High (mainframe) | Moderate | Higher (infrastructure) |
| **Typical use** | Legacy mainframes | Small business apps | Enterprise web systems |
| **Client type** | Dumb terminals | Fat clients | Thin clients (browser) |

---

## 5.6 Other Distributed Architectures (Brief)

### Parallel Database Systems
- Multiple processors/disks work **in parallel** on a single query
- Goal: **performance** — faster query execution
- Types: Shared Memory, Shared Disk, Shared Nothing (most scalable)

### Distributed Database Systems
- Database stored across **multiple geographically separated** sites
- Each site runs its own local DBMS
- Goal: **availability + fault tolerance + locality**

```
Site A (Mumbai) ────── Site B (Delhi) ────── Site C (Bangalore)
[Local DB A]           [Local DB B]           [Local DB C]
     └───────────── Distributed DBMS ──────────────┘
```

| Feature | Parallel DB | Distributed DB |
|---|---|---|
| **Location** | Single site, multiple CPUs | Multiple geographically separated sites |
| **Goal** | Speed (