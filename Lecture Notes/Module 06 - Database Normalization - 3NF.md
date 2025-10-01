# Database Normalization: Design, Dependencies, and 3NF

---

## What is Normalization?

### Normalization
* The process of organizing data in a database to minimize redundancy and improve data integrity.
* Progressively dividing tables into smaller ones and defining relationships to avoid duplicate, inconsistent, and anomalous data.

**Goals:**
- Eliminate data redundancy
- Avoid insertion, update, and deletion anomalies
- Ensure atomic values (single data value per cell)

---

## Normalization Anomalies

- **Insertion anomaly**: Can’t add data due to missing related info
- **Deletion anomaly**: Removing a row unintentionally deletes other valuable data
- **Update anomaly**: Changes in one place don’t update values elsewhere, causing inconsistency

---

## Steps and Forms in Normalization

### First Normal Form (1NF)
- All columns have `atomic values` (no lists or repeating groups)
- Each row is unique (has a primary key)

**Example Not in 1NF**

| EmpNum | EmpName       | Skills                 |
|--------|---------------|-----------------------|
| 101    | Jane Doe      | DBMS, Python, Java    |

**Converted to 1NF**

| EmpNum | EmpName | Skill  |
|--------|---------|--------|
| 101    | Jane Doe| DBMS   |
| 101    | Jane Doe| Python |
| 101    | Jane Doe| Java   |

---

### Second Normal Form (2NF)
- Must be in 1NF
- `No partial dependency` (no non-key attribute depends on part of a `composite primary key`)
- Only applies to tables with **composite keys**

**Partial Dependency Example:**  
If a table has (ProjectID, EmpNum) as the key, but ProjectName depends only on ProjectID:  
- ProjectName must move to its own table.

**2NF Conversion Example:**
- **PROJECT**: ProjectID, ProjectName
- **EMPLOYEE**: EmpNum, EmpName, JobClass, ChargeHour
- **ASSIGNMENT**: ProjectID, EmpNum, HoursBilled

---

### Third Normal Form (3NF)
- Must be in 2NF
- `No transitive dependency` (`non-key attribute depends on another non-key attribute`)

**Transitive Dependency Example:**  
If JobClass → ChargeHour, and JobClass is not a key,
Move ChargeHour to a new table linked by JobClass.

**3NF Conversion Example:**  
- **EMPLOYEE**: EmpNum, EmpName, JobClass
- **JOB**: JobClass, ChargeHour

---

## Dependency Types

- **Partial Dependency**: Attribute dependent on part of a composite primary key  
- **Transitive Dependency**: Attribute dependent on another non-key attribute

**Visual Example – Assignment Table Dependency Diagram:**
- ProjectID, EmpNum → AssignHours *(primary key)*
- ProjectID → ProjectName *(partial dependency)*
- JobClass → ChargeHour *(transitive dependency)*

---

## Best Practices in Normalization

- Clean up table structures to remove redundant data
- Use meaningful names and consistent keys
- Prefer unique IDs over descriptive attributes to achieve integrity
- Normalize up to 3NF for most business cases

---
# Simple Examples: 1NF, 1NF not 2NF, 2NF not 3NF

---

## 1. In 1NF (atomic, but redundant)

| ProjectID | ProjectName | EmpNum | EmpName     | JobClass | ChargeHour | AssignHours |
|-----------|-------------|--------|-------------|----------|------------|-------------|
| 101       | Evergreen   | 15     | Arbough     | ENG      | 50         | 12          |
| 101       | Evergreen   | 21     | News        | ENG      | 50         | 20          |
| 102       | Amber Wave  | 22     | Johnson     | MGR      | 60         | 15          |
| 101       | Evergreen   | 23     | Smithfield  | MGR      | 60         | 13          |

*Every cell holds only one value. There are no repeating groups or arrays.*
*But: ProjectName, JobClass, ChargeHour repeat in the table.*

---

## 2. In 1NF but not 2NF (partial dependency)

| ProjectID | ProjectName | EmpNum | AssignHours |
|-----------|-------------|--------|-------------|
| 101       | Evergreen   | 15     | 12          |
| 101       | Evergreen   | 21     | 20          |
| 102       | Amber Wave  | 22     | 15          |
| 101       | Evergreen   | 23     | 13          |

*All values are atomic. But ProjectName only depends on part of the key (ProjectID),*
*not on the whole composite key (ProjectID, EmpNum). That’s a partial dependency.*

---

## 3. In 2NF but not 3NF (transitive dependency)

| EmpNum | EmpName     | JobClass | ChargeHour |
|--------|-------------|----------|------------|
| 15     | Arbough     | ENG      | 50         |
| 21     | News        | ENG      | 50         |
| 22     | Johnson     | MGR      | 60         |
| 23     | Smithfield  | MGR      | 60         |

*No partial dependencies; every non-key column depends on EmpNum.*
*But ChargeHour is determined by JobClass, not strictly by EmpNum.*
*So, ChargeHour is transitively dependent on EmpNum via JobClass.*

---
# 3NF Example (Project & Employee)

When fully normalized to Third Normal Form (3NF), each table has only columns that depend *directly* on its primary key—no partial or transitive dependencies remain.

---

## PROJECT Table

| ProjectID | ProjectName  |
|-----------|--------------|
| 101       | Evergreen    |
| 102       | Amber Wave   |

---

## EMPLOYEE Table

| EmpNum | EmpName    | JobClass |
|--------|------------|----------|
| 15     | Arbough    | ENG      |
| 21     | News       | ENG      |
| 22     | Johnson    | MGR      |

---

## JOB Table

| JobClass | ChargeHour |
|----------|------------|
| ENG      | 50         |
| MGR      | 60         |

---

## ASSIGNMENT Table

| ProjectID | EmpNum | AssignHours |
|-----------|--------|-------------|
| 101       | 15     | 12          |
| 101       | 21     | 20          |
| 102       | 22     | 15          |
| 101       | 23     | 13          |

---

**Key Points:**
- Each table’s non-key attributes depend on the *whole key* (or are the key).
- ProjectName is separated from assignments, so no partial dependency.
- ChargeHour is separated into JOB, so no transitive dependency.
- Data redundancy and update anomalies are minimized.

This structure is a classic, clean example of 3NF for a project-assignment scenario.

---

**Key points:**
- 1NF: Atomic values and unique rows
- 1NF but not 2NF: At least one partial dependency
- 2NF but not 3NF: At least one transitive dependency

---
## Summary Table

| Normal Form | Requirement                 | Removes                    | Features                                    |
|-------------|-----------------------------|----------------------------|----------------------------------------     |
| 1NF         | Atomic values, unique rows  | Repeating groups, lists    |cell does not contain list                   |
| 2NF         | No partial dependency       | Partial dependency         |applies only for tables with composite key   |
| 3NF         | No transitive dependency    | Transitive dependency      |non-key attr depends on another non-key attr |
| 4NF         | No multivalued dependency   | Multivalued dependency     |`non-key attribute values repeated`          |

---
* Multivalued dependency - combinatorial redundancy: `non-key attribute values repeated` combinatorially.
  
**Note:**  
Normalization produces several smaller tables connected by keys. 
* (+) Normalization helps maintain integrity and consistency,
* (-) but more joins may reduce query performance – balance is key for large systems.

---
“For most business applications, it is only necessary to normalize databases up to the Third Normal Form (3NF).”

Why?
* 3NF removes the most common problems: By the time a table is in 3NF, you have eliminated almost all redundancy, partial dependencies, and transitive dependencies. This prevents data anomalies (like update, insert, and delete errors) and makes the data structure clear and consistent.

* Going beyond 3NF is rarely necessary: Higher forms like BCNF, 4NF, and 5NF solve very specialized issues that typically do not arise in ordinary business systems. Using higher forms can complicate the design with no real benefit for most business needs.

* Good balance: 3NF gives a balance of data integrity and practicality. Going further can make the database more complex and slower without a real business reason.

* 3NF does not eliminate `multi-valued dependency`.

# Is the "Table Not in 4NF" in 3NF?

## Example Table (In 3NF, but not in 4NF)

| StudentID | Hobby      | Course   |
|-----------|------------|----------|
| S1        | Painting   | Math     |
| S1        | Painting   | Physics  |
| S1        | Hiking     | Math     |
| S1        | Hiking     | Physics  |
| S2        | Reading    | Chemistry|
| S2        | Reading    | Biology  |

---

## Normal Form Analysis

- **1NF**: All entries are atomic (no lists or arrays).
- **2NF**: No partial dependencies; every attribute is part of the composite key (StudentID, Hobby, Course).
- **3NF**: No transitive dependencies; no attribute is functionally dependent on a non-key attribute.

**Conclusion:**  
This table is *in 3NF*—none of the values violate 1NF, 2NF, or 3NF. However, it is **not in 4NF** because it contains a `multivalued dependency - combinatorial redundancy` (each student is independently associated with multiple hobbies and multiple courses). This redundancy only gets resolved by 4NF decomposition.

---

# 4NF Decomposition Example (for Student-Hobby-Course)

## Student-Hobby Table

| StudentID | Hobby     |
|-----------|-----------|
| S1        | Painting  |
| S1        | Hiking    |
| S2        | Reading   |

## Student-Course Table

| StudentID | Course    |
|-----------|-----------|
| S1        | Math      |
| S1        | Physics   |
| S2        | Chemistry |
| S2        | Biology   |

**Result:**  
Multivalued dependencies are removed. Now, each independent relationship is modeled in a separate table, eliminating combinatorial redundancy.

4NF decomposition - SQL CREATE and INSERT statements

```sql
-- Create Student-Hobby table
CREATE TABLE StudentHobby (
    StudentID VARCHAR(10),
    Hobby VARCHAR(50),
    PRIMARY KEY (StudentID, Hobby)
);

-- Create Student-Course table
CREATE TABLE StudentCourse (
    StudentID VARCHAR(10),
    Course VARCHAR(50),
    PRIMARY KEY (StudentID, Course)
);

-- Insert data into StudentHobby
INSERT INTO StudentHobby (StudentID, Hobby) VALUES
('S1', 'Painting'),
('S1', 'Hiking'),
('S2', 'Reading');

-- Insert data into StudentCourse
INSERT INTO StudentCourse (StudentID, Course) VALUES
('S1', 'Math'),
('S1', 'Physics'),
('S2', 'Chemistry'),
('S2', 'Biology');

```

In summary:
For almost all business systems, achieving 3NF is “enough” and recommended for effective, practical, and maintainable database design. 


