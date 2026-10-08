# Data Normalization — Study Guide

**Goal:** Organize relational tables to reduce redundant data and prevent **insertion, update, and deletion anomalies**.

> **Main takeaway from the slide:** Third Normal Form (3NF) is generally considered sufficient for many everyday database designs. The slide also shows the more advanced BCNF, 4NF, and 5NF.

## 1. Why normalize tables?

Suppose a table stores the same department name repeatedly for every employee:

| EmployeeID | EmployeeName | DepartmentID | DepartmentName |
|---|---|---|---|
| 101 | Ada | D1 | Sales |
| 102 | Ben | D1 | Sales |
| 103 | Chen | D2 | IT |

Possible problems:

- **Update anomaly:** Renaming `Sales` means changing several rows; missing one creates inconsistency.
- **Insertion anomaly:** You cannot record a new department until you also have an employee, if `EmployeeID` is required.
- **Deletion anomaly:** Removing Chen could accidentally erase the only information that department D2 exists.

**Better design:** Store departments separately, and reference them from employees.

**DEPARTMENT**

| DepartmentID (PK) | DepartmentName |
|---|---|
| D1 | Sales |
| D2 | IT |

**EMPLOYEE**

| EmployeeID (PK) | EmployeeName | DepartmentID (FK) |
|---|---|---|
| 101 | Ada | D1 |
| 102 | Ben | D1 |
| 103 | Chen | D2 |

`PK` = primary key; `FK` = foreign key. The foreign key creates the relationship between the two tables.

---

## 2. First Normal Form (1NF): Remove multivalued attributes

**Rule:** Each cell contains one value (not a list or a repeating group), and rows can be uniquely identified.

### Before — not in 1NF

| StudentID | StudentName | PhoneNumbers |
|---|---|---|
| 1 | Ana | 555-0101, 555-0102 |
| 2 | Bob | 555-0103 |

The `PhoneNumbers` column contains multiple phone numbers in one cell.

### After — in 1NF

**STUDENT**

| StudentID (PK) | StudentName |
|---|---|
| 1 | Ana |
| 2 | Bob |

**STUDENT_PHONE**

| StudentID (PK, FK) | PhoneNumber (PK) |
|---|---|
| 1 | 555-0101 |
| 1 | 555-0102 |
| 2 | 555-0103 |

The combination `(StudentID, PhoneNumber)` uniquely identifies a phone record in this simple example.

**What to do:** Identify columns holding lists or repeating values. Move those values into a separate table linked to the original record.

---

## 3. Second Normal Form (2NF): Remove partial dependencies

**Rule:** Be in 1NF, and ensure every non-key attribute depends on the **whole candidate key**, not just part of a composite candidate key.

This is most easily seen in a table whose primary key has multiple columns.

### Before — in 1NF, but not 2NF

**ENROLLMENT** — composite primary key `(StudentID, CourseID)`

| StudentID (PK) | CourseID (PK) | StudentName | CourseTitle | Grade |
|---|---|---|---|---|
| 1 | C10 | Ana | Databases | A |
| 1 | C20 | Ana | Web Design | B |
| 2 | C10 | Bob | Databases | B |

Dependencies:

- `StudentID → StudentName` (only part of the key)
- `CourseID → CourseTitle` (only part of the key)
- `(StudentID, CourseID) → Grade` (the full key)

`StudentName` and `CourseTitle` have **partial dependencies**.

### After — in 2NF

**STUDENT**

| StudentID (PK) | StudentName |
|---|---|
| 1 | Ana |
| 2 | Bob |

**COURSE**

| CourseID (PK) | CourseTitle |
|---|---|
| C10 | Databases |
| C20 | Web Design |

**ENROLLMENT**

| StudentID (PK, FK) | CourseID (PK, FK) | Grade |
|---|---|---|
| 1 | C10 | A |
| 1 | C20 | B |
| 2 | C10 | B |

**What to do:** When a non-key column depends on only part of a composite key, move it to a table keyed by that part. Keep attributes such as `Grade` that describe the entire enrollment.

**Tip:** A table with a single-column candidate key cannot have a partial dependency on a *part* of that key. When multiple candidate keys exist, examine all of them.

---

## 4. Third Normal Form (3NF): Remove transitive dependencies

**Rule (introductory version):** Be in 2NF, and do not store non-key attributes that depend on other non-key attributes rather than directly on a key.

### Before — in 2NF, but not 3NF

**EMPLOYEE**

| EmployeeID (PK) | EmployeeName | DepartmentID | DepartmentName |
|---|---|---|---|
| 101 | Ada | D1 | Sales |
| 102 | Ben | D1 | Sales |
| 103 | Chen | D2 | IT |

Dependencies:

`EmployeeID → DepartmentID → DepartmentName`

`DepartmentName` depends on `DepartmentID`, which in turn depends on `EmployeeID`. This is a **transitive dependency**.

### After — in 3NF

**DEPARTMENT**(`DepartmentID` PK, `DepartmentName`)

**EMPLOYEE**(`EmployeeID` PK, `EmployeeName`, `DepartmentID` FK)

The full sample tables are shown in Section 1.

**What to do:** Move the indirectly dependent data (`DepartmentName`) into its own table, and reference it using `DepartmentID`.

**Remember:** The formal definition of 3NF involves all functional dependencies and candidate keys. This simpler rule works for the examples in this guide.

---

## 5. Boyce–Codd Normal Form (BCNF): Check every determinant

**Rule:** For every nontrivial functional dependency `X → Y`, `X` must be a **superkey** (a set of attributes that uniquely identifies a row).

BCNF is stricter than 3NF; a table can satisfy 3NF but fail BCNF when candidate keys overlap.

### Example — 3NF but not BCNF

Assume these business rules:

- A student taking a course has one instructor for that course.
- Every instructor teaches exactly one course (but a course may have multiple instructors).

**STUDENT_CLASS**

| Student | Course | Instructor |
|---|---|---|
| Ana | DB | Lee |
| Bob | DB | Kim |
| Ana | Web | Rao |

Dependencies:

- `(Student, Course) → Instructor`
- `Instructor → Course`

Candidate keys are `(Student, Course)` and `(Student, Instructor)`. All attributes are prime (belong to a candidate key), so this example satisfies 3NF. But `Instructor` is **not** a superkey, so `Instructor → Course` violates BCNF.

### After — in BCNF

**INSTRUCTOR_COURSE**

| Instructor (PK) | Course |
|---|---|
| Lee | DB |
| Kim | DB |
| Rao | Web |

**STUDENT_INSTRUCTOR**

| Student (PK) | Instructor (PK) |
|---|---|
| Ana | Lee |
| Bob | Kim |
| Ana | Rao |

**What to do:** Find determinants (`X` in `X → Y`) that are not superkeys and consider decomposing around them.

**Advanced caution:** BCNF decompositions can lose the ability to enforce some original functional dependencies in individual tables. Check dependency preservation as well as lossless joins.

---

## 6. Fourth Normal Form (4NF): Remove independent multivalued dependencies

**Rule:** Be in BCNF and eliminate nontrivial **multivalued dependencies** whose determinant is not a superkey.

### Before — independent lists create combinations

Suppose each employee independently has multiple skills and multiple spoken languages.

**EMPLOYEE_SKILL_LANGUAGE**

| EmployeeID | Skill | Language |
|---|---|---|
| 1 | SQL | English |
| 1 | SQL | Spanish |
| 1 | Python | English |
| 1 | Python | Spanish |

The independent facts `EmployeeID ↠ Skill` and `EmployeeID ↠ Language` force unnecessary combinations.

### After — in 4NF

**EMPLOYEE_SKILL**

| EmployeeID (PK) | Skill (PK) |
|---|---|
| 1 | SQL |
| 1 | Python |

**EMPLOYEE_LANGUAGE**

| EmployeeID (PK) | Language (PK) |
|---|---|
| 1 | English |
| 1 | Spanish |

**What to do:** Store each independent many-valued fact in its own relationship/table. This only applies when the two facts truly are independent.

---

## 7. Fifth Normal Form (5NF): Remove remaining join anomalies

**Rule:** Eliminate nontrivial **join dependencies** that are not implied by candidate keys. A table should not be decomposable into smaller projections and then rejoined losslessly in a way that exposes additional redundancy.

### Conceptual example

Consider a relationship **SUPPLY**(`Supplier`, `Part`, `Project`). Under a **special business rule**, a `(Supplier, Part, Project)` triple is valid precisely when all three pairwise relationships are valid:

1. Supplier can supply Part.
2. Supplier is approved for Project.
3. Project uses Part.

If that rule truly holds, the three-way table can be represented by three smaller tables:

- **SUPPLIER_PART**(`Supplier`, `Part`)
- **SUPPLIER_PROJECT**(`Supplier`, `Project`)
- **PROJECT_PART**(`Project`, `Part`)

Joining the three tables reconstructs the valid supplier–part–project combinations.

**Important:** Do **not** decompose a three-way relationship this way without confirming the business rule. In general, joining its pairwise projections can invent combinations that never existed.

**What to do:** Check whether a complex relationship can be reconstructed exactly from smaller relationships. 5NF is an advanced, less frequently needed step.

---

## 8. Quick reference

| Form | What to check | Typical fix |
|---|---|---|
| **1NF** | Lists/repeating values in a cell | Split values into separate rows/table |
| **2NF** | Non-key data depends on part of a composite candidate key | Separate partial dependencies |
| **3NF** | Non-key data depends indirectly on a key through another non-key attribute | Separate transitive dependencies |
| **BCNF** | A determinant is not a superkey | Decompose based on functional dependencies |
| **4NF** | Independent many-valued facts are combined | Create separate tables for each fact |
| **5NF** | Join dependency causes redundancy | Decompose only when lossless reconstruction is guaranteed |

**Progression shown in the slide:** Multivalued attributes → **1NF** → remove partial dependencies → **2NF** → remove transitive dependencies → **3NF** → resolve remaining candidate-key-related anomalies → **BCNF** → remove multivalued dependencies → **4NF** → resolve remaining join anomalies → **5NF**.

*Note:* The slide summarizes BCNF and 5NF in broad terms; the precise rules and advanced examples above expand on the slide for studying.

## 9. How to approach a normalization problem

1. **List the attributes** and write down the business rules.
2. **Identify candidate keys** (and choose a primary key).
3. **Identify functional dependencies:** which attributes determine others?
4. **Check 1NF:** Are there any lists or repeating groups?
5. **Check 2NF:** Does any non-key attribute depend on just part of a composite candidate key?
6. **Check 3NF:** Do non-key attributes depend on other non-key attributes?
7. **If needed, check BCNF, 4NF, 5NF** using their stricter dependency rules.
8. **Verify the decomposition:** Can the original valid information be reconstructed by joining tables without creating spurious rows? Are important constraints enforceable?

## 10. Practice (try before checking answers)

**A — 1NF**

`CUSTOMER(CustomerID, Name, Emails)` where `Emails` stores `a@example.com, b@example.com` in one cell.

**Question:** How should you redesign the tables?

**B — 2NF**

`ORDER_LINE(OrderID, ProductID, ProductName, Quantity)` with composite PK `(OrderID, ProductID)` and `ProductID → ProductName`.

**Question:** What should be moved?

**C — 3NF**

`BOOK(BookID, Title, PublisherID, PublisherName)` with `PublisherID → PublisherName`.

**Question:** What creates the transitive dependency?

<details>
<summary><strong>Show answers</strong></summary>

**A:** `CUSTOMER(CustomerID PK, Name)` and `CUSTOMER_EMAIL(CustomerID FK, Email)` with a suitable key, such as `(CustomerID, Email)` if duplicate email addresses per customer are not allowed.

**B:** `PRODUCT(ProductID PK, ProductName)` and `ORDER_LINE(OrderID, ProductID, Quantity)`; `ProductID` remains a foreign key in `ORDER_LINE`.

**C:** `BookID → PublisherID → PublisherName`. Use `PUBLISHER(PublisherID PK, PublisherName)` and `BOOK(BookID PK, Title, PublisherID FK)`.

</details>

---

**Study priority:** Understand **1NF, 2NF, 3NF** thoroughly first. Know the main purpose of **BCNF, 4NF, and 5NF**, and why their dependencies are more specialized.
