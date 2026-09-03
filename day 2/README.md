Query the Name of any student in STUDENTS who scored higher than  Marks. Order your output by the last three characters of each name. If two or more students both have names ending in the same last three characters (i.e.: Bobby, Robby, etc.), secondary sort them by ascending ID.


# SQL Query — Filter & Custom Sort

## Problem Statement

**Task:** Query the names of all students who scored higher than 75 marks. Order results by the **last 3 characters** of each name. For ties (same last 3 characters), sort by **ascending ID**.

---

## Table Structure

### STUDENTS Table

| Column | Type |
|--------|------|
| ID | INTEGER |
| NAME | VARCHAR |
| MARKS | INTEGER |

---

## Brute Force Approach

### Core Concepts Used

- **`WHERE` clause** — filters rows based on a condition
- **`SUBSTR(string, start, length)`** — extracts a substring from a string
- **`ORDER BY`** — sorts the result set; supports multiple sort keys
- **`LENGTH(string)`** — returns the total number of characters in a string

---

## The Query

```sql
SELECT NAME
FROM STUDENTS
WHERE MARKS > 75
ORDER BY SUBSTR(NAME, LENGTH(NAME) - 2, 3), ID ASC;
```

---

## Breaking Down the Brute Force Logic

### Step 1 — Filter with WHERE

```sql
WHERE MARKS > 75
```

Only students scoring **strictly greater than 75** are included. Students with exactly 75 are excluded.

---

### Step 2 — Extract Last 3 Characters (Brute Force Explained)

```sql
SUBSTR(NAME, LENGTH(NAME) - 2, 3)
```

This is the **key brute force trick**. Here's why it works:

| NAME | LENGTH(NAME) | LENGTH - 2 | SUBSTR starts at | Extracts |
|------|-------------|------------|-----------------|---------|
| Bobby | 5 | 3 | 3rd character | `bby` |
| Robby | 5 | 3 | 3rd character | `bby` |
| Sam | 3 | 1 | 1st character | `Sam` |
| Ashley | 6 | 4 | 4th character | `ley` |

**Formula logic:**
- `LENGTH(NAME)` gives total characters → e.g., `Bobby` = 5
- `LENGTH(NAME) - 2` gives the **starting index of the last 3 characters** → 5 - 2 = 3
- `SUBSTR(NAME, 3, 3)` → extracts 3 characters starting at position 3 → `bby`

> **Why not just `SUBSTR(NAME, -3)`?**
> The brute force approach avoids negative indexing (not supported in all DBMS). Using `LENGTH(NAME) - 2` is **universally safe** and exam-friendly.

---

### Step 3 — Tie-Breaking by ID

```sql
ORDER BY SUBSTR(NAME, LENGTH(NAME) - 2, 3), ID ASC
```

- **Primary sort:** Last 3 characters of name (ascending alphabetically)
- **Secondary sort:** `ID ASC` — when two names share the same last 3 characters, the one with the **smaller ID comes first**

---

## Example Walkthrough

### Sample Data

| ID | NAME | MARKS |
|----|------|-------|
| 1 | Ashley | 81 |
| 2 | Samantha | 75 |
| 3 | Julia | 88 |
| 4 | Belvet | 78 |
| 5 | Robby | 90 |
| 6 | Bobby | 85 |

---

### After `WHERE MARKS > 75`

| ID | NAME | MARKS |
|----|------|-------|
| 1 | Ashley | 81 |
| 3 | Julia | 88 |
| 4 | Belvet | 78 |
| 5 | Robby | 90 |
| 6 | Bobby | 85 |

*(Samantha with MARKS = 75 is excluded — condition is strictly **greater than**)*

---

### Last 3 Characters for Sorting

| ID | NAME | Last 3 Chars |
|----|------|-------------|
| 1 | Ashley | `ley` |
| 3 | Julia | `lia` |
| 4 | Belvet | `vet` |
| 5 | Robby | `bby` |
| 6 | Bobby | `bby` |

---

### After `ORDER BY SUBSTR(...), ID ASC`

| NAME |
|------|
| Robby |
| Bobby |
| Ashley |
| Julia |
| Belvet |

**Explanation of order:**
- `bby` (Robby ID=5, Bobby ID=6) → Robby comes first (smaller ID)
- `ley` → Ashley
- `lia` → Julia
- `vet` → Belvet

---

## Key Exam Points

- `SUBSTR` indexing starts at **1** in SQL (not 0 like most programming languages)
- `LENGTH(NAME) - 2` correctly points to the **3rd character from the end** for any name length
- Always specify `ASC` explicitly in secondary sort for clarity in exams — even though it is the default
- The `WHERE` condition uses `>` (strict), **not** `>=`

---

## Quick Revision Formula

```
To get last N characters using brute force:
SUBSTR(column, LENGTH(column) - (N - 1), N)

For last 3 characters (N = 3):
SUBSTR(NAME, LENGTH(NAME) - 2, 3)
```