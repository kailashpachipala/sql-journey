# SQL Functions: Date & Time, Numeric, and String/Conversion

---

# 1. SQL Functions — Overview

**SQL Functions** are built-in operations that accept input values (arguments), process them, and return a result. They are used inside `SELECT`, `WHERE`, `HAVING`, `ORDER BY`, and other clauses.

## Classification Used Here

| Category | Purpose |
|---|---|
| **Date & Time Functions** | Manipulate and retrieve date/time values |
| **Numeric Functions** | Perform mathematical operations on numbers |
| **String Functions** | Manipulate and format character/text data |
| **Conversion Functions** | Convert data from one type to another |

> **Exam Tip:** Most universities test MySQL/standard SQL syntax. All examples below use MySQL-compatible syntax unless noted.

---

# 2. Date and Time Functions

**Date and Time Functions** operate on `DATE`, `TIME`, `DATETIME`, and `TIMESTAMP` data types to extract, format, calculate, or compare temporal values.

## 2.1 Getting Current Date and Time

| Function | Returns | Example Output |
|---|---|---|
| `NOW()` | Current date and time | `2024-06-15 14:35:22` |
| `CURDATE()` / `CURRENT_DATE()` | Current date only | `2024-06-15` |
| `CURTIME()` / `CURRENT_TIME()` | Current time only | `14:35:22` |
| `SYSDATE()` | System date and time at execution | `2024-06-15 14:35:22` |

```sql
SELECT NOW()        AS current_datetime,
       CURDATE()    AS current_date,
       CURTIME()    AS current_time,
       SYSDATE()    AS system_time;
```

**Output:**

| current_datetime | current_date | current_time | system_time |
|---|---|---|---|
| 2024-06-15 14:35:22 | 2024-06-15 | 14:35:22 | 2024-06-15 14:35:22 |

---

## 2.2 Extracting Parts of a Date

### YEAR(), MONTH(), DAY()

**Used to extract individual components from a date value.**

```sql
SELECT YEAR('2024-06-15')   AS year_val,
       MONTH('2024-06-15')  AS month_val,
       DAY('2024-06-15')    AS day_val;
```

**Output:**

| year_val | month_val | day_val |
|---|---|---|
| 2024 | 6 | 15 |

---

### HOUR(), MINUTE(), SECOND()

```sql
SELECT HOUR('14:35:22')   AS hour_val,
       MINUTE('14:35:22') AS minute_val,
       SECOND('14:35:22') AS second_val;
```

**Output:**

| hour_val | minute_val | second_val |
|---|---|---|
| 14 | 35 | 22 |

---

### EXTRACT()

**`EXTRACT(unit FROM date)`** — Standard SQL function to pull a specific part from a date.

| Unit Keyword | Description |
|---|---|
| `YEAR` | Year component |
| `MONTH` | Month component |
| `DAY` | Day component |
| `HOUR` | Hour component |
| `MINUTE` | Minute component |
| `SECOND` | Second component |
| `WEEK` | Week number of the year |
| `QUARTER` | Quarter (1–4) |

```sql
SELECT EXTRACT(YEAR    FROM '2024-06-15 14:35:22') AS yr,
       EXTRACT(MONTH   FROM '2024-06-15 14:35:22') AS mo,
       EXTRACT(QUARTER FROM '2024-06-15 14:35:22') AS qtr,
       EXTRACT(WEEK    FROM '2024-06-15 14:35:22') AS wk;
```

**Output:**

| yr | mo | qtr | wk |
|---|---|---|---|
| 2024 | 6 | 2 | 24 |

---

### DAYNAME(), MONTHNAME(), DAYOFWEEK(), DAYOFYEAR()

```sql
SELECT DAYNAME('2024-06-15')    AS day_name,
       MONTHNAME('2024-06-15')  AS month_name,
       DAYOFWEEK('2024-06-15')  AS day_of_week,
       DAYOFYEAR('2024-06-15')  AS day_of_year;
```

**Output:**

| day_name | month_name | day_of_week | day_of_year |
|---|---|---|---|
| Saturday | June | 7 | 167 |

> `DAYOFWEEK()` returns 1 = Sunday, 2 = Monday, …, 7 = Saturday.

---

## 2.3 Date Arithmetic Functions

### DATE_ADD() and DATE_SUB()

**`DATE_ADD(date, INTERVAL value unit)`** — Adds an interval to a date.
**`DATE_SUB(date, INTERVAL value unit)`** — Subtracts an interval from a date.

```sql
SELECT DATE_ADD('2024-06-15', INTERVAL 10 DAY)   AS plus_10_days,
       DATE_SUB('2024-06-15', INTERVAL 3  MONTH)  AS minus_3_months,
       DATE_ADD('2024-06-15', INTERVAL 1  YEAR)   AS plus_1_year,
       DATE_ADD('2024-06-15 10:00:00', INTERVAL 90 MINUTE) AS plus_90_min;
```

**Output:**

| plus_10_days | minus_3_months | plus_1_year | plus_90_min |
|---|---|---|---|
| 2024-06-25 | 2024-03-15 | 2025-06-15 | 2024-06-15 11:30:00 |

---

### DATEDIFF()

**`DATEDIFF(date1, date2)`** — Returns the number of days between two dates (`date1 - date2`).

```sql
SELECT DATEDIFF('2024-12-31', '2024-06-15') AS days_remaining;
```

**Output:**

| days_remaining |
|---|
| 199 |

---

### TIMESTAMPDIFF()

**`TIMESTAMPDIFF(unit, datetime1, datetime2)`** — Returns the difference between two datetime values in the specified unit.

```sql
SELECT TIMESTAMPDIFF(YEAR,  '2000-05-20', '2024-06-15') AS age_years,
       TIMESTAMPDIFF(MONTH, '2024-01-01', '2024-06-15') AS months_diff,
       TIMESTAMPDIFF(DAY,   '2024-06-01', '2024-06-15') AS days_diff;
```

**Output:**

| age_years | months_diff | days_diff |
|---|---|---|
| 24 | 5 | 14 |

---

## 2.4 Formatting Dates

### DATE_FORMAT()

**`DATE_FORMAT(date, format_string)`** — Formats a date/time value according to a custom pattern.

| Format Code | Meaning | Example |
|---|---|---|
| `%Y` | 4-digit year | `2024` |
| `%y` | 2-digit year | `24` |
| `%M` | Full month name | `June` |
| `%m` | Month number (01–12) | `06` |
| `%d` | Day of month (01–31) | `15` |
| `%D` | Day with suffix | `15th` |
| `%W` | Full weekday name | `Saturday` |
| `%H` | Hour 24-hr (00–23) | `14` |
| `%h` | Hour 12-hr (01–12) | `02` |
| `%i` | Minutes (00–59) | `35` |
| `%s` | Seconds (00–59) | `22` |
| `%p` | AM or PM | `PM` |

```sql
SELECT DATE_FORMAT('2024-06-15 14:35:22', '%D %M %Y')          AS fmt1,
       DATE_FORMAT('2024-06-15 14:35:22', '%d/%m/%Y')           AS fmt2,
       DATE_FORMAT('2024-06-15 14:35:22', '%W, %M %d, %Y')      AS fmt3,
       DATE_FORMAT('2024-06-15 14:35:22', '%h:%i %p')           AS fmt4;
```

**Output:**

| fmt1 | fmt2 | fmt3 | fmt4 |
|---|---|---|---|
| 15th June 2024 | 15/06/2024 | Saturday, June 15, 2024 | 02:35 PM |

---

## 2.5 Other Useful Date Functions

| Function | Description | Example | Output |
|---|---|---|---|
| `LAST_DAY(date)` | Last day of the month | `LAST_DAY('2024-02-10')` | `2024-02-29` |
| `WEEKDAY(date)` | Weekday index (0=Mon…6=Sun) | `WEEKDAY('2024-06-15')` | `5` |
| `WEEK(date)` | Week number of the year | `WEEK('2024-06-15')` | `24` |
| `QUARTER(date)` | Quarter (1–4) | `QUARTER('2024-06-15')` | `2` |
| `TO_DAYS(date)` | Days since year 0 | `TO_DAYS('2024-06-15')` | `739388` |
| `FROM_DAYS(n)` | Date from day number | `FROM_DAYS(739388)` | `2024-06-15` |
| `MAKEDATE(year,day)` | Date from year + day-of-year | `MAKEDATE(2024, 167)` | `2024-06-15` |
| `STR_TO_DATE(str, fmt)` | Parse string to date | `STR_TO_DATE('15-06-2024','%d-%m-%Y')` | `2024-06-15` |

---

## 2.6 Real-World Date Function Example

**Scenario:** Employee table — find age, years of service, and formatted hire date.

```sql
-- Sample Table
CREATE TABLE employees (
    emp_id    INT,
    emp_name  VARCHAR(50),
    dob       DATE,
    hire_date DATE
);

INSERT INTO employees VALUES
(1, 'Anjali Sharma',  '1995-03-20', '2018-07-01'),
(2, 'Rohan Mehta',   '1990-11-05', '2015-01-15'),
(3, 'Priya Nair',    '2000-08-30', '2022-09-10');
```

```sql
SELECT
    emp_name,
    TIMESTAMPDIFF(YEAR, dob, CURDATE())       AS age,
    TIMESTAMPDIFF(YEAR, hire_date, CURDATE()) AS years_of_service,
    DATE_FORMAT(hire_date, '%D %M %Y')        AS formatted_hire_date,
    DATEDIFF(CURDATE(), hire_date)            AS total_days_worked
FROM employees;
```

**Output** *(assuming current date = 2024-06-15)*:

| emp_name | age | years_of_service | formatted_hire_date | total_days_worked |
|---|---|---|---|---|
| Anjali Sharma | 29 | 5 | 1st July 2018 | 2176 |
| Rohan Mehta | 33 | 9 | 15th January 2015 | 3439 |
| Priya Nair | 23 | 1 | 10th September 2022 | 644 |

---

# 3. Numeric Functions

**Numeric Functions** perform mathematical computations on numeric data types (`INT`, `FLOAT`, `DECIMAL`, etc.) and return a numeric result.

## 3.1 Rounding and Truncation

| Function | Syntax | Description |
|---|---|---|
| `ROUND()` | `ROUND(n, d)` | Rounds `n` to `d` decimal places |
| `CEIL()` / `CEILING()` | `CEIL(n)` | Smallest integer ≥ n |
| `FLOOR()` | `FLOOR(n)` | Largest integer ≤ n |
| `TRUNCATE()` | `TRUNCATE(n, d)` | Cuts off at `d` decimals (no rounding) |

```sql
SELECT ROUND(7.5678, 2)     AS rounded,
       ROUND(7.5678, 0)     AS round_to_int,
       CEIL(7.2)            AS ceiling_val,
       FLOOR(7.9)           AS floor_val,
       TRUNCATE(7.5678, 2)  AS truncated;
```

**Output:**

| rounded | round_to_int | ceiling_val | floor_val | truncated |
|---|---|---|---|---|
| 7.57 | 8 | 8 | 7 | 7.56 |

> **Key Difference:** `ROUND(7.567, 2)` = `7.57` but `TRUNCATE(7.567, 2)` = `7.56` — truncate never rounds up.

---

## 3.2 Absolute Value and Sign

```sql
SELECT ABS(-45.7)   AS absolute_val,
       ABS(45.7)    AS positive_val,
       SIGN(-10)    AS sign_neg,
       SIGN(0)      AS sign_zero,
       SIGN(10)     AS sign_pos;
```

**Output:**

| absolute_val | positive_val | sign_neg | sign_zero | sign_pos |
|---|---|---|---|---|
| 45.7 | 45.7 | -1 | 0 | 1 |

> **`SIGN(n)`** returns `-1` (negative), `0` (zero), or `1` (positive).

---

## 3.3 Power and Root Functions

| Function | Description | Example | Output |
|---|---|---|---|
| `POWER(x, y)` / `POW(x,y)` | x raised to the power y | `POWER(2, 10)` | `1024` |
| `SQRT(n)` | Square root of n | `SQRT(144)` | `12` |
| `EXP(n)` | e raised to the power n | `EXP(1)` | `2.718281...` |
| `LOG(n)` | Natural log (base e) | `LOG(2.718281)` | `1` |
| `LOG10(n)` | Base-10 logarithm | `LOG10(1000)` | `3` |
| `LOG2(n)` | Base-2 logarithm | `LOG2(8)` | `3` |

```sql
SELECT POWER(3, 4)    AS three_to_four,
       SQRT(225)      AS sqrt_225,
       EXP(2)         AS e_squared,
       LOG10(100)     AS log_base10;
```

**Output:**

| three_to_four | sqrt_225 | e_squared | log_base10 |
|---|---|---|---|
| 81 | 15 | 7.389056 | 2 |

---

## 3.4 Modulo and Integer Division

```sql
SELECT MOD(17, 5)      AS modulo_result,
       17 MOD 5        AS modulo_alt,
       17 % 5          AS modulo_symbol;
```

**Output:**

| modulo_result | modulo_alt | modulo_symbol |
|---|---|---|
| 2 | 2 | 2 |

---

## 3.5 Trigonometric Functions

| Function | Description |
|---|---|
| `SIN(x)` | Sine of x (radians) |
| `COS(x)` | Cosine of x (radians) |
| `TAN(x)` | Tangent of x (radians) |
| `ASIN(x)` | Arc sine |
| `ACOS(x)` | Arc cosine |
| `ATAN(x)` | Arc tangent |
| `DEGREES(x)` | Converts radians to degrees |
| `RADIANS(x)` | Converts degrees to radians |
| `PI()` | Returns π ≈ 3.141593 |

```sql
SELECT PI()                         AS pi_value,
       SIN(RADIANS(30))             AS sin_30,
       COS(RADIANS(60))             AS cos_60,
       DEGREES(PI())                AS pi_in_degrees;
```

**Output:**

| pi_value | sin_30 | cos_60 | pi_in_degrees |
|---|---|---|---|
| 3.141593 | 0.5 | 0.5 | 180 |

---

## 3.6 Random Numbers

**`RAND()`** — Returns a random float between 0 and 1 (exclusive).

```sql
-- Random float between 0 and 1
SELECT RAND() AS random_float;

-- Random integer between 1 and 100
SELECT FLOOR(1 + RAND() * 100) AS random_1_to_100;

-- Random integer between 50 and 100
SELECT FLOOR(50 + RAND() * 51) AS random_50_to_100;
```

---

## 3.7 Aggregate / Statistical Numeric Functions

> These work on a **set of rows** (used with `GROUP BY`).

| Function | Description |
|---|---|
| `SUM(col)` | Total sum of values |
| `AVG(col)` | Arithmetic mean |
| `MIN(col)` | Smallest value |
| `MAX(col)` | Largest value |
| `COUNT(col)` | Count of non-null values |

```sql
-- Sample: sales table
SELECT department,
       COUNT(*)        AS total_employees,
       SUM(salary)     AS total_salary,
       AVG(salary)     AS avg_salary,
       MIN(salary)     AS min_salary,
       MAX(salary)     AS max_salary,
       ROUND(AVG(salary), 2) AS avg_rounded
FROM employees
GROUP BY department;
```

---

## 3.8 Real-World Numeric Function Example

**Scenario:** Product pricing — apply discounts, tax, and rounded totals.

```sql
CREATE TABLE products (
    product_id   INT,
    product_name VARCHAR(50),
    price        DECIMAL(10, 2),
    discount_pct DECIMAL(5, 2)
);

INSERT INTO products VALUES
(1, 'Laptop',    75000.00, 10.00),
(2, 'Mouse',      850.75,   5.00),
(3, 'Keyboard',  2399.99,  15.00);
```

```sql
SELECT
    product_name,
    price,
    discount_pct,
    ROUND(price * discount_pct / 100, 2)              AS discount_amount,
    ROUND(price - (price * discount_pct / 100), 2)    AS discounted_price,
    ROUND((price - (price * discount_pct / 100)) * 1.18, 2) AS price_with_gst,
    CEIL(price)                                        AS ceil_price,
    FLOOR(