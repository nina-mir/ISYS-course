# SQL Practice Worksheet 1 — Querying a Single Table

**ISYS 464 — Managing Enterprise Data**
**Table used:** `HR.COUNTRIES` (Oracle)

---

## Before you begin

Open your SQL environment and connect. Everything in this worksheet uses one small
table, so you can check your own answers by looking at the data.

Every SQL statement ends with a **semicolon** `;`

For each exercise: write the query, run it, and record **how many rows** came back.
If your row count doesn't match the answer key, your `WHERE` clause is the place to look.

---

## Part 0 — Meet the table

**0.1** Find out what columns the table has and what type of data each one holds.

```sql
DESCRIBE HR.COUNTRIES;
```

Write down the three column names: ________________  ________________  ________________

**0.2** Look at the entire table.

```sql
SELECT * FROM HR.COUNTRIES;
```

Rows returned: ______

> The `*` means "every column." It is convenient for looking around, but in real work
> you should name the columns you actually want.

---

## Part 1 — SELECT

`SELECT` chooses **which columns** you want. `FROM` says **which table** they live in.

```
SELECT  column1, column2
FROM    table;
```

**1.1** Show only the country names.

**1.2** Show the country ID and the country name, in that order.

**1.3** Show the country name with the heading `Country`.
Hint: use `AS "Country"` after the column name.

**1.4** Show each region ID **once**, with no repeats.
Hint: `SELECT DISTINCT ...`

Rows returned: ______ — and what does that number tell you about the data?

_______________________________________________________________

---

## Part 2 — WHERE

`WHERE` chooses **which rows** you want. It goes after `FROM`.

```
SELECT  columns
FROM    table
WHERE   condition;
```

Text values go in **single quotes**: `'Japan'`. Numbers do not: `30`.

**2.1** Show all countries in region 30.  Rows: ______

**2.2** Show the row for Japan.  Rows: ______

**2.3** Now run the same query but type `'japan'` in lowercase. Rows: ______

What happened, and why?

_______________________________________________________________

**2.4** Show every country that is **not** in region 10.
Hint: `<>` means "not equal to."  Rows: ______

**2.5** Show countries in region 40 **or** region 50.  Rows: ______

**2.6** Rewrite 2.5 using `IN (...)` instead of `OR`. Confirm you get the same rows.

**2.7** Show the countries whose ID is `'US'`, `'CA'`, or `'MX'`.  Rows: ______

---

## Part 3 — BETWEEN

`BETWEEN` tests whether a value falls in a range. **Both ends are included.**

```
WHERE  column BETWEEN low AND high
```

**3.1** Show countries with a region ID from 20 through 40.  Rows: ______

**3.2** Rewrite 3.1 without `BETWEEN`, using two conditions joined by `AND`.
Do you get the same rows?

**3.3** Now reverse it: show countries **outside** that range using `NOT BETWEEN`.
Rows: ______  (3.1 + 3.3 should add up to the whole table — does it?)

**3.4** `BETWEEN` also works on text. Run this:

```sql
SELECT COUNTRY_NAME
FROM   HR.COUNTRIES
WHERE  COUNTRY_NAME BETWEEN 'A' AND 'F';
```

Rows: ______

**France** does not appear. Egypt does. Explain why.

_______________________________________________________________

_______________________________________________________________

---

## Part 4 — LIKE

`LIKE` matches **patterns** in text. Two wildcards:

| Wildcard | Meaning |
|:---:|---|
| `%` | any number of characters, including none |
| `_` | exactly one character |

**4.1** Countries whose name starts with `A`.  Rows: ______

**4.2** Countries whose name ends in `ia`.  Rows: ______

**4.3** Countries whose name contains `land` **anywhere**.  Rows: ______

**4.4** Countries whose ID has any first letter and `R` as its second letter.
Hint: `COUNTRY_ID LIKE '_R'`  Rows: ______

**4.5** Now try `COUNTRY_ID LIKE '%R'`. Do you get the same rows? Why or why not?

_______________________________________________________________

**4.6** Countries whose name contains a space.
Hint: the pattern is `'% %'`  Rows: ______

---

## Part 5 — LENGTH

`LENGTH(column)` returns how many characters are in a text value. You can put it in
the `SELECT` list, in the `WHERE` clause, or both.

**5.1** Show each country name next to its length.

```sql
SELECT COUNTRY_NAME, LENGTH(COUNTRY_NAME)
FROM   HR.COUNTRIES;
```

**5.2** Give that second column the heading `Name_Length`.

**5.3** Show only the countries whose name is exactly 5 characters long.  Rows: ______

**5.4** Show only the countries whose name is longer than 10 characters.  Rows: ______

**5.5** Show the countries whose name length is between 6 and 8 characters.
Combine `LENGTH` and `BETWEEN`.  Rows: ______

---

## Part 6 — ORDER BY, ASC and DESC

`ORDER BY` sorts the result. It is always the **last** clause.

- `ASC` = ascending (A→Z, small→large). This is the default, so it is optional.
- `DESC` = descending (Z→A, large→small). You must type this one.

**6.1** All country names in alphabetical order. First row: ______________

**6.2** All country names in reverse alphabetical order. First row: ______________

**6.3** Sort by region ID from highest to lowest, and within each region sort the
country names alphabetically.
Hint: `ORDER BY REGION_ID DESC, COUNTRY_NAME ASC`

First row: ______________

**6.4** Show each country name and its length, sorted from longest name to shortest.

Which country is at the top? ______________

**6.5** Add this to the end of 6.4 to see only the top five:

```sql
FETCH FIRST 5 ROWS ONLY
```

> **Careful — `DESC` means two different things in SQL.**
> At the end of `ORDER BY`, it sorts descending.
> Typed on its own (`DESC HR.COUNTRIES;`) it is short for `DESCRIBE` and shows the
> table's structure. Same four letters, completely different jobs.

---

## Part 7 — Putting it together

**7.1** Country names in region 30 that are longer than 5 characters, sorted
alphabetically.  Rows: ______

**7.2** All countries whose name ends in the letter `a`, sorted Z→A.  Rows: ______

**7.3** Country ID and name for every country whose **ID** starts with `C`.
Rows: ______

Look closely at your results. One of them is surprising. Which one, and what does that
tell you about relying on an ID to guess the name?

_______________________________________________________________

**7.4** In this table, Malaysia has the country ID `ML`. The international standard
(ISO 3166) assigns `MY` to Malaysia and `ML` to Mali.

Nothing in the database is "broken" — every query still runs. So why does this matter?
Write two or three sentences.

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

---
---

# INSTRUCTOR ANSWER KEY

*(Remove this section before distributing.)*

**Table facts:** 25 rows. Region counts — 10: 8 rows, 20: 5, 30: 7, 40: 1, 50: 4.

### Part 0
- **0.2** 25 rows.

### Part 1
```sql
-- 1.1
SELECT COUNTRY_NAME FROM HR.COUNTRIES;                          -- 25 rows
-- 1.2
SELECT COUNTRY_ID, COUNTRY_NAME FROM HR.COUNTRIES;              -- 25 rows
-- 1.3
SELECT COUNTRY_NAME AS "Country" FROM HR.COUNTRIES;             -- 25 rows
-- 1.4
SELECT DISTINCT REGION_ID FROM HR.COUNTRIES;                    -- 5 rows
```
**1.4 discussion:** only five distinct regions across 25 countries, so `REGION_ID`
repeats. It is a foreign key to `HR.REGIONS`, not a unique value.

### Part 2
```sql
-- 2.1
SELECT * FROM HR.COUNTRIES WHERE REGION_ID = 30;                -- 7 rows
-- China, Israel, India, Japan, Kuwait, Malaysia, Singapore
-- 2.2
SELECT * FROM HR.COUNTRIES WHERE COUNTRY_NAME = 'Japan';        -- 1 row
-- 2.3
SELECT * FROM HR.COUNTRIES WHERE COUNTRY_NAME = 'japan';        -- 0 rows
-- 2.4
SELECT * FROM HR.COUNTRIES WHERE REGION_ID <> 10;               -- 17 rows
-- 2.5
SELECT * FROM HR.COUNTRIES WHERE REGION_ID = 40 OR REGION_ID = 50;  -- 5 rows
-- Australia, Egypt, Nigeria, Zambia, Zimbabwe
-- 2.6
SELECT * FROM HR.COUNTRIES WHERE REGION_ID IN (40, 50);         -- 5 rows
-- 2.7
SELECT * FROM HR.COUNTRIES WHERE COUNTRY_ID IN ('US','CA','MX');    -- 3 rows
```
**2.3 discussion:** Oracle string comparison is case sensitive, so `'japan'` matches
nothing. Zero rows is a *result*, not an error — the query was valid. Mention
`UPPER()` / `LOWER()` here if you want to preview them.

### Part 3
```sql
-- 3.1
SELECT * FROM HR.COUNTRIES WHERE REGION_ID BETWEEN 20 AND 40;   -- 13 rows
-- 3.2
SELECT * FROM HR.COUNTRIES WHERE REGION_ID >= 20 AND REGION_ID <= 40;  -- 13 rows
-- 3.3
SELECT * FROM HR.COUNTRIES WHERE REGION_ID NOT BETWEEN 20 AND 40;      -- 12 rows
-- 13 + 12 = 25. Works cleanly here only because there are no NULLs.
-- 3.4
SELECT COUNTRY_NAME FROM HR.COUNTRIES
WHERE COUNTRY_NAME BETWEEN 'A' AND 'F';                         -- 8 rows
-- Argentina, Australia, Belgium, Brazil, Canada, China, Denmark, Egypt
```
**3.4 discussion — the key idea of this worksheet.** Text is compared character by
character. `'France'` is longer than `'F'` and every character up to that point is
equal, so `'France'` sorts *after* `'F'` and falls outside the range. `'Egypt'` is
below `'F'` at the very first character, so it is inside. The fix students should
reach for: `BETWEEN 'A' AND 'F'` is not "A through F" — it is "up to and including the
single character F." Use `LIKE 'F%'` or `< 'G'` if that is what they meant.

### Part 4
```sql
-- 4.1  LIKE 'A%'        --> 2 rows: Argentina, Australia
-- 4.2  LIKE '%ia'       --> 5 rows: Australia, India, Malaysia, Nigeria, Zambia
-- 4.3  LIKE '%land%'    --> 3 rows: Switzerland, Netherlands,
--                            United Kingdom of Great Britain and Northern Ireland
-- 4.4  COUNTRY_ID LIKE '_R'  --> 3 rows: AR Argentina, BR Brazil, FR France
-- 4.5  COUNTRY_ID LIKE '%R'  --> same 3 rows here
-- 4.6  COUNTRY_NAME LIKE '% %' --> 2 rows (the UK and the USA)
```
**4.5 discussion:** identical results *only because* every `COUNTRY_ID` happens to be
exactly two characters. `_` demands exactly one character before the R; `%` would also
accept `ABCR` or `R` alone. Same answer, different rule — a good moment to stress that
matching output does not prove matching logic.

### Part 5
```sql
-- 5.2
SELECT COUNTRY_NAME, LENGTH(COUNTRY_NAME) AS "Name_Length" FROM HR.COUNTRIES;
-- 5.3  WHERE LENGTH(COUNTRY_NAME) = 5   --> 5 rows: China, Egypt, India, Italy, Japan
-- 5.4  WHERE LENGTH(COUNTRY_NAME) > 10  --> 4 rows: Switzerland (11),
--        Netherlands (11), United States of America (24), United Kingdom... (52)
-- 5.5  WHERE LENGTH(COUNTRY_NAME) BETWEEN 6 AND 8  --> 13 rows
```

### Part 6
```sql
-- 6.1  ORDER BY COUNTRY_NAME         --> first row Argentina
-- 6.2  ORDER BY COUNTRY_NAME DESC    --> first row Zimbabwe
-- 6.3  ORDER BY REGION_ID DESC, COUNTRY_NAME ASC
--        --> Egypt, Nigeria, Zambia, Zimbabwe (region 50), then Australia (40) ...
-- 6.4
SELECT COUNTRY_NAME, LENGTH(COUNTRY_NAME)
FROM   HR.COUNTRIES
ORDER BY LENGTH(COUNTRY_NAME) DESC;
--   United Kingdom of Great Britain and Northern Ireland (52),
--   United States of America (24), Switzerland (11), Netherlands (11), Argentina (9)
```

### Part 7
```sql
-- 7.1
SELECT COUNTRY_NAME FROM HR.COUNTRIES
WHERE REGION_ID = 30 AND LENGTH(COUNTRY_NAME) > 5
ORDER BY COUNTRY_NAME;              -- 4 rows: Israel, Kuwait, Malaysia, Singapore
-- 7.2
SELECT COUNTRY_NAME FROM HR.COUNTRIES
WHERE COUNTRY_NAME LIKE '%a' ORDER BY COUNTRY_NAME DESC;    -- 9 rows
-- 7.3
SELECT COUNTRY_ID, COUNTRY_NAME FROM HR.COUNTRIES
WHERE COUNTRY_ID LIKE 'C%';         -- 3 rows: CA Canada, CH Switzerland, CN China
```
**7.3 discussion:** Switzerland is `CH` (from *Confoederatio Helvetica*). The ID is an
identifier, not an abbreviation of the name — filtering on a key tells you nothing
reliable about the data it points to.

**7.4 discussion:** looking for data-quality reasoning, not a single right answer.
Strong responses mention: the query results are correct but the *meaning* is wrong;
integration with any other system that uses ISO codes will silently mismatch or
double-count; the error is invisible until someone joins on it; and standards exist
precisely so keys mean the same thing across organizations.

---

### Notes on environment

- Written for **Oracle**. On MySQL, `FETCH FIRST 5 ROWS ONLY` (6.5) becomes
  `LIMIT 5`, and `LENGTH()` counts bytes rather than characters — for this table the
  results are identical, since every name is plain ASCII.
- `DESCRIBE` (0.1) runs in SQL*Plus, SQL Developer and Oracle Live SQL. Confirm it
  works in whichever free environment the class is using; if not, substitute
  `SELECT * FROM HR.COUNTRIES FETCH FIRST 1 ROWS ONLY;`
- Every query here is read-only. Students need `SELECT` privileges on `HR` and nothing
  more.
