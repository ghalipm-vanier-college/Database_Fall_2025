
#  Lecture 05 – Define the Database in a DBMS

**Goal:** Understand how databases are designed, created, and managed within a DBMS.

---

## 🎯 Learning Objectives

By the end of this lecture, students should be able to:

1. Explain the stages of database design.
2. Create a new database and define its structure.
3. Identify and use SQL data types appropriately.
4. Apply key database constraints such as **PRIMARY KEY**, **FOREIGN KEY**, **NOT NULL**, **UNIQUE**, and **CHECK**.
5. Modify or delete database structures using SQL commands.

---

## 🧠 1. Database Design Process

Designing a database involves several steps from idea to implementation.

| Phase | Description |
|-------|--------------|
| **Requirements Collection & Analysis** | Gather and understand what data the users need and how they will use it. |
| **Conceptual Design** | Create a *conceptual schema* (e.g., ER Diagram) showing entities, attributes, and relationships — independent of DBMS choice. |
| **Logical Design** | Transform the conceptual model into a *relational schema* (tables, keys, relationships). |
| **Physical Design** | Implement the schema using a specific DBMS, optimizing for performance. |

```

Requirements → Conceptual Design → Logical Design → Physical Design

````

---

## 2. Creating a Database

**Syntax:**
```sql
CREATE DATABASE database_name;
````

**Notes:**

* Some systems require space allocation before table creation.
* When a database is created, **data dictionary tables** are automatically generated to store metadata.
* A default database administrator (DBA) is also created.

---

## 3. Data Definition Language (DDL)

DDL commands are used to define the structure of the database.

| Command  | Description                         |
| -------- | ----------------------------------- |
| `CREATE` | Create new tables or databases      |
| `ALTER`  | Modify existing database structures |
| `DROP`   | Delete tables or databases          |

These statements can define:

* Tables and columns
* Keys and constraints
* Views, indexes, and stored procedures

---

## 4. Creating a Table

**Syntax:**

```sql
CREATE TABLE TableName (
   column_name data_type [(size)]
   [CONSTRAINT constraint_name constraint_type],
   ...
);
```

### Rules

* Table names: up to **128 characters**, may include letters, numbers, `_`, `$`, `#`.
* Column names must be **unique** within a table.
* Can define constraints at the **column** or **table** level.

---

## 5. SQL Data Types

| Type                      | Description                     | Example                    |
| ------------------------- | ------------------------------- | -------------------------- |
| **CHAR(n)**               | Fixed-length string             | `CHAR(10)`                 |
| **VARCHAR(n)**            | Variable-length string          | `VARCHAR(50)`              |
| **NUMERIC(p, s)**         | Number with precision and scale | `NUMERIC(8,2)`             |
| **INT, SMALLINT, BIGINT** | Whole numbers                   | `INT`                      |
| **FLOAT, REAL**           | Decimal numbers (approximate)   | `FLOAT(6)`                 |
| **DATE, TIME, DATETIME**  | Date and time values            | `DATE`, `TIME`, `DATETIME` |
| **BIT**                   | Boolean values (0 or 1)         | `BIT`                      |

👉 For string data, always use **single quotes**: `'Hello'`.

**Tip:**
Use the smallest suitable data type to save space and improve performance.

---

##  6. Database Constraints

Constraints enforce rules on data integrity and relationships.

| Constraint      | Purpose                                  | Example                                                |
| --------------- | ---------------------------------------- | ------------------------------------------------------ |
| **PRIMARY KEY** | Uniquely identifies each row             | `PRIMARY KEY(StudentID)`                               |
| **FOREIGN KEY** | Ensures reference to another table’s key | `FOREIGN KEY(FacultyID) REFERENCES Faculty(FacultyID)` |
| **NOT NULL**    | Ensures a column cannot have NULL        | `Name VARCHAR(50) NOT NULL`                            |
| **UNIQUE**      | Prevents duplicate values                | `UNIQUE(Email)`                                        |
| **CHECK**       | Restricts values based on a condition    | `CHECK (Grade BETWEEN 0 AND 100)`                      |
| **DEFAULT**     | Assigns a default value if none provided | `DEFAULT 0`                                            |

**Naming Convention:**

```
<table>_<column>_<constraint type>
Example: STUDENT_ID_PK
```

---

## 7. Entity & Referential Integrity

### Entity Integrity

* Each table must have a **unique primary key**.
* The key **cannot contain NULL values**.

```sql
PRIMARY KEY(StudentID)
```

### Referential Integrity

* Maintained using **FOREIGN KEY** constraints.
* The foreign key must exist in the referenced (parent) table.

```sql
FOREIGN KEY(FacultyID) REFERENCES Faculty(FacultyID)
```

### Actions on Delete/Update

```sql
ON DELETE CASCADE
ON DELETE SET NULL
ON UPDATE CASCADE
```

---

## 8. ALTER TABLE

Used to modify an existing table structure.

**Examples:**

```sql
ALTER TABLE Staff
ADD SocialSecurity CHAR(9);

ALTER TABLE Staff
ALTER COLUMN SocialSecurity VARCHAR(11);

ALTER TABLE Staff
ADD CONSTRAINT Staff_Branch_FK FOREIGN KEY(BranchNo) REFERENCES Branch;
```

You can:

* Add or remove columns
* Add or drop constraints
* Rename columns or constraints

---

## ❌ 9. DROP TABLE

Removes a table and all its data permanently.

**Syntax:**

```sql
DROP TABLE TableName;
```

**Example:**

```sql
DROP TABLE Employee;
```

⚠️ Use with caution — this command cannot be undone!

---

## 🧾 Example: Creating Tables

```sql
CREATE TABLE Department (
    Dept_Number CHAR(6) PRIMARY KEY,
    Dept_Name VARCHAR(20) NOT NULL,
    Location VARCHAR(20)
);

CREATE TABLE Employees (
    Emp_Number NUMBER(10),
    Emp_Name VARCHAR(50) NOT NULL,
    Dept_ID CHAR(6),
    CONSTRAINT Emp_EmpNum_PK PRIMARY KEY(Emp_Number),
    CONSTRAINT Emp_DeptID_FK FOREIGN KEY(Dept_ID)
        REFERENCES Department(Dept_Number)
);
```

---

## 🧭 Summary

* **DDL** defines the database structure.
* **CREATE DATABASE** and **CREATE TABLE** start the schema.
* **Data types** define what can be stored.
* **Constraints** ensure accuracy and consistency.
* **ALTER TABLE** modifies, and **DROP TABLE** deletes structures.

---

## 💡 Review Questions

1. What are the four main stages of the database design process?
2. What is the difference between `CHAR` and `VARCHAR`?
3. Give one example each of a column-level and table-level constraint.
4. What happens when `ON DELETE CASCADE` is used?
5. Write SQL to create a table `Course` with:

   * `CourseID` (Primary Key)
   * `CourseName` (VARCHAR(50))
   * `Credits` (INTEGER between 1 and 5)

---

## 📚 References

* Microsoft SQL Server Documentation:
  [Data Types (Transact-SQL)](https://docs.microsoft.com/en-us/sql/t-sql/data-types/data-types-transact-sql)
* Elmasri & Navathe, *Fundamentals of Database Systems*, 7th Ed.

---

> “Design your database like you design your thoughts — structured, consistent, and ready to scale.”

```

