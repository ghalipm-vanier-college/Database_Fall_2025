
# Lecture 08 – Database Views (Virtual Tables)

---

## 1️⃣ Introduction to Views
- A **view** is a **virtual table** based on a **SELECT query**.  
- It **does not store data physically**; instead, it provides a **dynamic result** each time it’s queried.  
- Think of a view as a **saved SQL query** you can reuse.

---

## 2️⃣ Why Use Views
- **Simplify repeated queries** (e.g., daily reports).  
- **Restrict access** — show only certain columns or rows to specific users.  
- **Hide complexity** — users can query the view instead of writing long joins.  
- **Improve security** — hide sensitive data like salaries.  

---

## 3️⃣ Types of Views

| Type | Description | Updatable? |
|------|--------------|-------------|
| **Simple View** | Based on one table, no aggregates or GROUP BY | ✅ Yes |
| **Complex View** | Based on multiple tables or includes aggregates | ⚠️ Not always |

---

## 4️⃣ Creating a View

**Syntax:**
```sql
CREATE VIEW view_name [(column_list)]
AS
SELECT ...
[WITH CHECK OPTION];
````

* **column_list** → optional; needed if columns are computed or aggregated.
* **WITH CHECK OPTION** → ensures inserted/updated rows satisfy the view’s WHERE condition.

---

### Examples

**(a)** Staff at branch B003:

```sql
CREATE VIEW vwManager3Staff AS
SELECT * FROM Staff
WHERE branchNo = 'B003';
```

**(b)** Staff at B003 excluding salaries:

```sql
CREATE VIEW vwStaff3 AS
SELECT staffNo, fName, lName, position, sex
FROM Staff
WHERE branchNo = 'B003';
```

**(c)** View with aggregation (must name columns):

```sql
CREATE VIEW vwStaffCnt(branch_Num, v_count) AS
SELECT branchNo, COUNT(*) FROM Staff GROUP BY branchNo;
```

or using an alias:

```sql
CREATE VIEW vwStaffCnt AS
SELECT branchNo, COUNT(*) AS Count_Staff
FROM Staff GROUP BY branchNo;
```

---

## 5️⃣ DML Operations on Views

Views can be used with `SELECT`, `INSERT`, `UPDATE`, and `DELETE`, but:

### ✅ Allowed only when:

* The view is **simple** (one base table).
* No aggregate or GROUP BY.
* No DISTINCT.

### ❌ Not allowed when:

* View combines **multiple tables**.
* Uses **aggregate functions**.
* Includes **GROUP BY** or **DISTINCT**.

---

### INSERT Example

```sql
CREATE VIEW vw_branch AS
SELECT branchNo, city FROM Branch;

INSERT INTO vw_branch VALUES ('B009', 'MONTREAL');
```

This inserts a row into **Branch**.
Use **WITH CHECK OPTION** to ensure inserted rows meet the view’s WHERE condition.

---

## 6️⃣ WITH CHECK OPTION Example

```sql
CREATE VIEW vwManager3Staff AS
SELECT staffNo, fName, lName, branchNo
FROM Staff
WHERE branchNo = 'B003'
WITH CHECK OPTION;
```

* Prevents inserting or updating rows with a branch number other than B003.

---

## 7️⃣ Grouped / Joined Views

Example (count staff managing properties):

```sql
CREATE VIEW vwStaffPropCnt(branchNo, staffNo, cnt) AS
SELECT s.branchNo, s.staffNo, COUNT(*)
FROM Staff s, PropertyForRent p
WHERE s.staffNo = p.staffNo
GROUP BY s.branchNo, s.staffNo;
```

> ❗ Data modification not allowed (aggregates and multiple tables).

---

## 8️⃣ Dropping and Altering Views

```sql
DROP VIEW view_name;
ALTER VIEW view_name AS SELECT ...;
```

* Dropping a view does **not** delete data in the base table.
* Altering rewrites the view definition.

---

## 9️⃣ Summary

✅ A view:

* Is a saved query showing data from one or more tables.
* Simplifies data access and enforces security.
* Can be updatable if simple.
* Becomes invalid if base tables are dropped.

---

### Key Benefits

* Simplifies queries.
* Restricts access.
* Hides data complexity.
* Provides consistent, reusable logic.

---

```

```
