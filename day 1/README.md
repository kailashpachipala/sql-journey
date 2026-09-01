# SQL Journey 🗄️

> A structured, hands-on journey to learn SQL from fundamentals to advanced database concepts.

This repository contains my **SQL learning notes, practice queries, exercises, and projects**.
The goal is to build a strong understanding of SQL through consistent practice and real-world problem solving.

---

## 🎯 Goals

* Learn SQL from **beginner to advanced**
* Understand how relational databases work
* Write clean and efficient SQL queries
* Practice SQL problems regularly
* Build database-based projects
* Prepare for **technical interviews and examinations**
* Develop strong **data analysis and database skills**

---

## 📚 Learning Roadmap

```text
SQL Fundamentals
       ↓
SELECT & Filtering
       ↓
Aggregate Functions
       ↓
GROUP BY & HAVING
       ↓
JOINS
       ↓
Subqueries
       ↓
Constraints
       ↓
INSERT / UPDATE / DELETE
       ↓
DDL & Database Design
       ↓
Views & Indexes
       ↓
Advanced SQL
       ↓
Projects
```

---

## 📂 Repository Structure

```text
sql-journey/
│
├── README.md
│
├── 01-basics/
│   ├── notes/
│   ├── practice/
│   └── exercises/
│
├── 02-filtering/
│   ├── notes/
│   ├── practice/
│   └── exercises/
│
├── 03-aggregate-functions/
│   ├── notes/
│   ├── practice/
│   └── exercises/
│
├── 04-grouping/
│   ├── notes/
│   ├── practice/
│   └── exercises/
│
├── 05-joins/
│   ├── notes/
│   ├── practice/
│   └── exercises/
│
├── 06-subqueries/
│   ├── notes/
│   ├── practice/
│   └── exercises/
│
├── 07-constraints/
│   ├── notes/
│   └── practice/
│
├── 08-dml/
│   ├── notes/
│   └── practice/
│
├── 09-ddl/
│   ├── notes/
│   └── practice/
│
├── 10-advanced-sql/
│   ├── notes/
│   └── practice/
│
└── projects/
    ├── project-01/
    └── project-02/
```

---

# 🧠 Topics Covered

### 01. SQL Basics

* Databases and tables
* `SELECT`
* `FROM`
* Selecting specific columns
* `SELECT *`
* Column aliases using `AS`

### 02. Filtering

* `WHERE`
* Comparison operators
* `AND`
* `OR`
* `NOT`
* `BETWEEN`
* `IN`
* `NOT IN`
* `LIKE`
* `IS NULL`
* `IS NOT NULL`

### 03. Aggregate Functions

* `COUNT()`
* `SUM()`
* `AVG()`
* `MAX()`
* `MIN()`
* `COUNT(DISTINCT ...)`

### 04. Grouping

* `GROUP BY`
* `HAVING`
* Filtering aggregated results
* Multiple aggregate conditions

### 05. Sorting & Limiting

* `ORDER BY`
* `ASC`
* `DESC`
* `LIMIT`
* `TOP`
* Database-specific row limiting

### 06. Joins

* `INNER JOIN`
* `LEFT JOIN`
* `RIGHT JOIN`
* `FULL OUTER JOIN`
* Self joins
* Multiple-table joins

### 07. Subqueries

* Scalar subqueries
* Single-row subqueries
* Multi-row subqueries
* Correlated subqueries
* `EXISTS`
* `NOT EXISTS`

### 08. Data Manipulation

* `INSERT`
* `UPDATE`
* `DELETE`

### 09. Database Definition

* `CREATE`
* `ALTER`
* `DROP`
* `TRUNCATE`
* Tables
* Constraints

### 10. Advanced SQL

* Views
* Indexes
* Common Table Expressions (CTEs)
* Window functions
* `CASE`
* Stored procedures
* Transactions
* Query optimization

---

# 📖 Current Notes

## SQL SELECT

The first major set of notes covers retrieving, filtering, grouping, sorting, and aggregating student data.

Topics currently covered:

| Topic                | Status |
| -------------------- | :----: |
| `SELECT`             |    ✅   |
| `FROM`               |    ✅   |
| `WHERE`              |    ✅   |
| Comparison Operators |    ✅   |
| `AND / OR / NOT`     |    ✅   |
| `BETWEEN`            |    ✅   |
| `IN`                 |    ✅   |
| `LIKE`               |    ✅   |
| `NULL`               |    ✅   |
| `DISTINCT`           |    ✅   |
| `ORDER BY`           |    ✅   |
| `LIMIT`              |    ✅   |
| Aggregate Functions  |    ✅   |
| `GROUP BY`           |    ✅   |
| `HAVING`             |    ✅   |
| Aliases              |    ✅   |
| Combining Clauses    |    ✅   |

Detailed notes:

```text
01-basics/
└── notes/
    └── sql-select.md
```

---

# 🧪 Practice Method

Each topic follows this learning cycle:

```text
Learn the concept
       ↓
Understand the syntax
       ↓
Study examples
       ↓
Write queries yourself
       ↓
Solve exercises
       ↓
Review mistakes
       ↓
Apply the concept in a project
```

The purpose is not just to memorize SQL syntax.

The goal is to understand **why a query works and when to use it**.

---

# 📝 SQL Query Pattern

A common SQL query structure is:

```sql
SELECT columns
FROM table
WHERE row_condition
GROUP BY columns
HAVING group_condition
ORDER BY columns
LIMIT n;
```

Not every query requires every clause.

### Logical Processing Order

```text
FROM
  ↓
WHERE
  ↓
GROUP BY
  ↓
HAVING
  ↓
SELECT
  ↓
ORDER BY
  ↓
LIMIT
```

---

# ⚠️ Important SQL Exam Traps

### `WHERE` vs `HAVING`

```sql
WHERE marks > 80
```

filters individual rows.

```sql
HAVING AVG(marks) > 80
```

filters groups after aggregation.

### `NULL`

Incorrect:

```sql
WHERE city = NULL;
```

Correct:

```sql
WHERE city IS NULL;
```

### `BETWEEN`

`BETWEEN` includes both endpoints.

```sql
marks BETWEEN 70 AND 90
```

means:

```sql
marks >= 70 AND marks <= 90
```

### `LIKE`

```text
% → zero or more characters
_ → exactly one character
```

### `DISTINCT`

With multiple columns, `DISTINCT` removes duplicate **combinations** of the selected columns.

---

# 📈 Progress

| Stage               |     Status     |
| ------------------- | :------------: |
| SQL Fundamentals    | 🟢 In Progress |
| SELECT & Filtering  | 🟢 In Progress |
| Aggregate Functions |        ⬜       |
| GROUP BY & HAVING   |        ⬜       |
| Joins               |        ⬜       |
| Subqueries          |        ⬜       |
| DML                 |        ⬜       |
| DDL                 |        ⬜       |
| Constraints         |        ⬜       |
| Advanced SQL        |        ⬜       |
| Projects            |        ⬜       |

> Progress will be updated as new topics are completed.

---

# 🛠️ Tools & Technologies

* SQL
* MySQL / PostgreSQL / SQL Server
* Git
* GitHub
* VS Code

Database-specific syntax may differ between SQL systems, so examples will identify the relevant database when necessary.

---

# 🚀 Projects

Practical SQL projects will be added here as the learning journey progresses.

```text
projects/
├── project-01/
└── project-02/
```

Projects will focus on applying SQL to realistic datasets rather than only solving isolated syntax exercises.

---

# 🔄 Git Workflow

After adding or modifying SQL files:

```bash
git add .
git commit -m "Add SQL practice"
git push
```

Example:

```bash
git add .
git commit -m "Add SQL SELECT notes and practice"
git push
```

---

# 📌 Repository Philosophy

> **Learn → Practice → Make mistakes → Fix → Build → Repeat**

This repository is a record of the learning process, including notes, queries, exercises, and projects.

---

## 👤 Author

**Kailash Pachipala**

SQL Learning Journey — 2026
