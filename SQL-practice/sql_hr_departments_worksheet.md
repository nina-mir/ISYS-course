# SQL Practice Worksheet — `HR.DEPARTMENTS`

## Getting Started

Go to:

**https://freesql.com/**

There is **no need to sign up or log in**.

For all of the exercises in this worksheet, use the Oracle table:

```sql
HR.DEPARTMENTS
```

This table contains **27 departments** and four columns:

| Column | Description |
|---|---|
| `DEPARTMENT_ID` | Unique ID assigned to a department |
| `DEPARTMENT_NAME` | Name of the department |
| `MANAGER_ID` | ID of the employee who manages the department |
| `LOCATION_ID` | ID of the location where the department is located |

A few rows from the table are:

| DEPARTMENT_ID | DEPARTMENT_NAME | MANAGER_ID | LOCATION_ID |
|---:|---|---:|---:|
| 10 | Administration | 200 | 1700 |
| 20 | Marketing | 201 | 1800 |
| 30 | Purchasing | 114 | 1700 |
| 40 | Human Resources | 203 | 2400 |
| 50 | Shipping | 121 | 1500 |
| 60 | IT | 103 | 1400 |

For each task, **type your SQL query into FreeSQL and run it**.

---

# 1. `SELECT`

`SELECT` is used to retrieve data from a database table.

The basic structure is:

```sql
SELECT column_name
FROM table_name;
```

If you want to retrieve **every column**, use `*`:

```sql
SELECT *
FROM HR.DEPARTMENTS;
```

## Task 1 — View the Entire Table

Display **all columns and all rows** from `HR.DEPARTMENTS`.

---

# 2. Selecting Particular Columns

You usually do not need every column in a table.

To retrieve several columns, separate their names with commas:

```sql
SELECT column1, column2
FROM table_name;
```

For example:

```sql
SELECT DEPARTMENT_NAME, LOCATION_ID
FROM HR.DEPARTMENTS;
```

## Task 2 — Select Two Columns

Display only:

- `DEPARTMENT_ID`
- `DEPARTMENT_NAME`

from every department.

---

# 3. `WHERE`

`WHERE` filters rows.

For example:

```sql
SELECT *
FROM HR.DEPARTMENTS
WHERE DEPARTMENT_ID = 50;
```

Instead of returning every department, this query returns only the row whose `DEPARTMENT_ID` is `50`.

## Task 3 — Find One Department

Display **all columns** for the department whose `DEPARTMENT_ID` is `60`.

---

# 4. Selecting Rows and Columns Together

`SELECT` controls the **columns** you see, while `WHERE` controls the **rows** you see.

For example:

```sql
SELECT DEPARTMENT_NAME, LOCATION_ID
FROM HR.DEPARTMENTS
WHERE LOCATION_ID = 1700;
```

## Task 4 — Departments at One Location

Display only:

- `DEPARTMENT_ID`
- `DEPARTMENT_NAME`

for departments whose `LOCATION_ID` is `1700`.

---

# 5. `LIKE`

`LIKE` is used to search text using a pattern.

The `%` symbol is a **wildcard** representing zero or more characters.

For example:

```sql
WHERE DEPARTMENT_NAME LIKE 'S%'
```

means:

> Find names that begin with `S`.

This:

```sql
WHERE DEPARTMENT_NAME LIKE '%Sales'
```

means:

> Find names that end with `Sales`.

And this:

```sql
WHERE DEPARTMENT_NAME LIKE '%Sales%'
```

means:

> Find names containing `Sales` anywhere.

## Task 5 — Find Sales Departments

Display the `DEPARTMENT_ID` and `DEPARTMENT_NAME` of every department whose name contains the word `Sales`.

---

# 6. `NULL` and `IS NOT NULL`

A `NULL` means that a value is **missing or has not been assigned**.

Some rows in `HR.DEPARTMENTS` have no `MANAGER_ID`.

To find rows where a value exists, use:

```sql
IS NOT NULL
```

For example:

```sql
WHERE MANAGER_ID IS NOT NULL
```

Do **not** use:

```sql
MANAGER_ID = NULL
```

When testing for null values, SQL uses `IS NULL` or `IS NOT NULL`.

## Task 6 — Departments with Managers

Display:

- `DEPARTMENT_ID`
- `DEPARTMENT_NAME`
- `MANAGER_ID`

only for departments that have a manager assigned.

---

# 7. `BETWEEN`

`BETWEEN` checks whether a value falls within a range.

For example:

```sql
WHERE DEPARTMENT_ID BETWEEN 50 AND 100
```

`BETWEEN` is **inclusive**.

That means both `50` and `100` are included in the range.

## Task 7 — Find a Range of Departments

Display:

- `DEPARTMENT_ID`
- `DEPARTMENT_NAME`

for departments whose `DEPARTMENT_ID` is between `100` and `160`, including both endpoints.

---

# 8. Aggregate Functions

An **aggregate function** summarizes a collection of rows and produces a calculated result.

Common SQL aggregate functions include:

| Function | Purpose |
|---|---|
| `COUNT()` | Counts rows or values |
| `AVG()` | Calculates an average |
| `SUM()` | Adds numeric values |
| `MIN()` | Finds the smallest value |
| `MAX()` | Finds the largest value |

For example:

```sql
SELECT COUNT(*)
FROM HR.DEPARTMENTS;
```

The table contains many rows, but `COUNT(*)` summarizes them and returns **one number**.

You can use `AS` to give the result a useful column name:

```sql
SELECT COUNT(*) AS department_count
FROM HR.DEPARTMENTS;
```

### `COUNT(*)` versus `COUNT(column)`

There is an important difference:

```sql
COUNT(*)
```

counts **every row**.

But:

```sql
COUNT(MANAGER_ID)
```

counts only rows where `MANAGER_ID` is **not null**.

In this table:

- there are 27 department rows
- only 11 departments have a `MANAGER_ID`

## Task 8 — Practice `COUNT()`

Write **one query** that returns:

- the total number of departments using `COUNT(*)`
- the number of departments that have a manager using `COUNT(MANAGER_ID)`

Name the two result columns:

- `total_departments`
- `departments_with_manager`

---

# 9. `AVG()`

`AVG()` calculates the arithmetic average of a numeric column.

For example:

```sql
SELECT AVG(column_name)
FROM table_name;
```

The SQL function is called:

```sql
AVG()
```

not `AVERAGE()`.

### A Note About This Table

`HR.DEPARTMENTS` contains IDs rather than measurements such as salary, price, or quantity.

An ID is simply a label, so finding the average department ID would **not normally be meaningful business analysis**.

We will do it here only to practice how `AVG()` works.

## Task 9 — Practice `AVG()`

Calculate the average of `DEPARTMENT_ID`.

Name the result:

```text
average_department_id
```

---

# 10. `GROUP BY`

Aggregate functions become much more useful when combined with `GROUP BY`.

Without `GROUP BY`:

```sql
SELECT COUNT(*)
FROM HR.DEPARTMENTS;
```

SQL treats all 27 rows as **one collection of rows** and returns one result:

> How many departments are there altogether?

But suppose we want to answer:

> How many departments are at **each location**?

Now we need SQL to separate the rows into groups.

That is what `GROUP BY` does.

```sql
SELECT LOCATION_ID, COUNT(*)
FROM HR.DEPARTMENTS
GROUP BY LOCATION_ID;
```

You can think of SQL creating a separate **bucket** for each different `LOCATION_ID`:

```text
LOCATION 1400  → departments at 1400
LOCATION 1500  → departments at 1500
LOCATION 1700  → departments at 1700
...
```

Then `COUNT(*)` is performed **inside each bucket**.

The result therefore contains **one row for each location**, rather than one row for the whole table.

### Without `GROUP BY`

```text
All departments → COUNT()
```

One overall result.

### With `GROUP BY LOCATION_ID`

```text
Location 1400 → COUNT()
Location 1500 → COUNT()
Location 1700 → COUNT()
...
```

One result for each group.

### Important Rule

When using `GROUP BY`, a regular column appearing in the `SELECT` list should also appear in the `GROUP BY`.

For example:

```sql
SELECT LOCATION_ID, COUNT(*)
FROM HR.DEPARTMENTS
GROUP BY LOCATION_ID;
```

`LOCATION_ID` appears in both places.

## Task 10 — Count Departments by Location

Display:

- `LOCATION_ID`
- the number of departments at that location

Name the count:

```text
department_count
```

Group the rows by `LOCATION_ID`.

---

# 11. `ORDER BY`

`ORDER BY` sorts the rows returned by a query.

For example:

```sql
SELECT DEPARTMENT_ID, DEPARTMENT_NAME
FROM HR.DEPARTMENTS
ORDER BY DEPARTMENT_NAME;
```

Text will be sorted alphabetically.

Numbers will be sorted numerically.

SQL provides two directions:

```text
ASC   = ascending
DESC  = descending
```

> The SQL keyword is `ASC`, not `ASCEND`.

### Ascending Order

```sql
ORDER BY DEPARTMENT_ID ASC;
```

For numbers:

```text
10
20
30
40
...
```

For text, ascending order is generally alphabetical:

```text
Accounting
Administration
Benefits
...
```

`ASC` is the default. Therefore:

```sql
ORDER BY DEPARTMENT_NAME;
```

and:

```sql
ORDER BY DEPARTMENT_NAME ASC;
```

produce the same ordering.

## Task 11 — Sort Alphabetically

Display:

- `DEPARTMENT_ID`
- `DEPARTMENT_NAME`

for all departments.

Sort the results alphabetically by `DEPARTMENT_NAME` using `ASC`.

---

# 12. `DESC` — Descending Order

`DESC` reverses the sorting direction.

For example:

```sql
ORDER BY DEPARTMENT_ID DESC;
```

would produce:

```text
270
260
250
240
...
```

`ORDER BY` happens to the **final result**, so it can also be used with `WHERE`, aggregate functions, and `GROUP BY`.

Also, remember that `GROUP BY` itself does **not guarantee that the results will be displayed in sorted order**. If the order matters, use `ORDER BY`.

## Task 12 — Sort in Descending Order

Display:

- `DEPARTMENT_ID`
- `DEPARTMENT_NAME`

for every department.

Sort the departments from the **highest `DEPARTMENT_ID` to the lowest**.

---

# A Little More About `ORDER BY`

You can also sort using more than one column.

For example:

```sql
SELECT LOCATION_ID, DEPARTMENT_NAME
FROM HR.DEPARTMENTS
ORDER BY LOCATION_ID ASC, DEPARTMENT_NAME ASC;
```

SQL will:

1. sort rows by `LOCATION_ID`
2. when several departments have the same `LOCATION_ID`, sort those departments alphabetically by `DEPARTMENT_NAME`

You can also combine ascending and descending sorting:

```sql
ORDER BY LOCATION_ID ASC, DEPARTMENT_NAME DESC;
```

---

# Answer Key

## Task 1

```sql
SELECT *
FROM HR.DEPARTMENTS;
```

**Result:** 27 rows.

---

## Task 2

```sql
SELECT DEPARTMENT_ID, DEPARTMENT_NAME
FROM HR.DEPARTMENTS;
```

**Result:** 27 rows containing only the requested two columns.

---

## Task 3

```sql
SELECT *
FROM HR.DEPARTMENTS
WHERE DEPARTMENT_ID = 60;
```

Result:

| DEPARTMENT_ID | DEPARTMENT_NAME | MANAGER_ID | LOCATION_ID |
|---:|---|---:|---:|
| 60 | IT | 103 | 1400 |

---

## Task 4

```sql
SELECT DEPARTMENT_ID, DEPARTMENT_NAME
FROM HR.DEPARTMENTS
WHERE LOCATION_ID = 1700;
```

**Result:** 21 departments.

---

## Task 5

```sql
SELECT DEPARTMENT_ID, DEPARTMENT_NAME
FROM HR.DEPARTMENTS
WHERE DEPARTMENT_NAME LIKE '%Sales%';
```

Result:

| DEPARTMENT_ID | DEPARTMENT_NAME |
|---:|---|
| 80 | Sales |
| 240 | Government Sales |
| 250 | Retail Sales |

---

## Task 6

```sql
SELECT DEPARTMENT_ID, DEPARTMENT_NAME, MANAGER_ID
FROM HR.DEPARTMENTS
WHERE MANAGER_ID IS NOT NULL;
```

**Result:** 11 departments.

---

## Task 7

```sql
SELECT DEPARTMENT_ID, DEPARTMENT_NAME
FROM HR.DEPARTMENTS
WHERE DEPARTMENT_ID BETWEEN 100 AND 160;
```

Result:

| DEPARTMENT_ID | DEPARTMENT_NAME |
|---:|---|
| 100 | Finance |
| 110 | Accounting |
| 120 | Treasury |
| 130 | Corporate Tax |
| 140 | Control And Credit |
| 150 | Shareholder Services |
| 160 | Benefits |

---

## Task 8

```sql
SELECT COUNT(*) AS total_departments,
       COUNT(MANAGER_ID) AS departments_with_manager
FROM HR.DEPARTMENTS;
```

Result:

| TOTAL_DEPARTMENTS | DEPARTMENTS_WITH_MANAGER |
|---:|---:|
| 27 | 11 |

---

## Task 9

```sql
SELECT AVG(DEPARTMENT_ID) AS average_department_id
FROM HR.DEPARTMENTS;
```

Result:

| AVERAGE_DEPARTMENT_ID |
|---:|
| 140 |

Remember that averaging an ID is only being used here to practice the function.

---

## Task 10

```sql
SELECT LOCATION_ID, COUNT(*) AS department_count
FROM HR.DEPARTMENTS
GROUP BY LOCATION_ID;
```

The data contains these groups:

| LOCATION_ID | DEPARTMENT_COUNT |
|---:|---:|
| 1400 | 1 |
| 1500 | 1 |
| 1700 | 21 |
| 1800 | 1 |
| 2400 | 1 |
| 2500 | 1 |
| 2700 | 1 |

The rows may appear in a different order because this query does not use `ORDER BY`.

---

## Task 11

```sql
SELECT DEPARTMENT_ID, DEPARTMENT_NAME
FROM HR.DEPARTMENTS
ORDER BY DEPARTMENT_NAME ASC;
```

The departments will be displayed alphabetically by name.

---

## Task 12

```sql
SELECT DEPARTMENT_ID, DEPARTMENT_NAME
FROM HR.DEPARTMENTS
ORDER BY DEPARTMENT_ID DESC;
```

The result begins:

| DEPARTMENT_ID | DEPARTMENT_NAME |
|---:|---|
| 270 | Payroll |
| 260 | Recruiting |
| 250 | Retail Sales |
| 240 | Government Sales |
| 230 | IT Helpdesk |

and continues down to department `10`.

---

# Quick Reference

| SQL Keyword / Function | What It Does |
|---|---|
| `SELECT` | Chooses columns to retrieve |
| `*` | Selects all columns |
| `FROM` | Specifies the table |
| `WHERE` | Filters rows |
| `LIKE` | Searches text using a pattern |
| `%` | Represents zero or more characters in a `LIKE` pattern |
| `IS NULL` | Finds missing values |
| `IS NOT NULL` | Finds rows where a value exists |
| `BETWEEN` | Selects values within an inclusive range |
| `COUNT()` | Counts rows or non-null values |
| `AVG()` | Calculates an average |
| `SUM()` | Adds numeric values |
| `MIN()` | Finds the minimum value |
| `MAX()` | Finds the maximum value |
| `AS` | Gives a result column a temporary name |
| `GROUP BY` | Divides rows into groups before an aggregate calculation |
| `ORDER BY` | Sorts the final query result |
| `ASC` | Sorts from low to high / A to Z |
| `DESC` | Sorts from high to low / Z to A |
