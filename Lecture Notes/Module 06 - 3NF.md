# Database Normalization: Design, Dependencies, and 3NF

---

## What is Normalization?

Normalization is the process of organizing data in a database to minimize redundancy and improve data integrity.  
It involves progressively dividing tables into smaller ones and defining relationships to avoid duplicate, inconsistent, and anomalous data.

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
- All columns have atomic values (no lists or repeating groups)
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
- No partial dependency (no non-key attribute depends on part of a composite primary key)
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
- No transitive dependency (non-key attribute depends on another non-key attribute)

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

## Summary Table

| Normal Form | Requirement                 | Removes                    |
|-------------|-----------------------------|----------------------------|
| 1NF         | Atomic values, unique rows  | Repeating groups, lists    |
| 2NF         | No partial dependency       | Partial dependency         |
| 3NF         | No transitive dependency    | Transitive dependency      |

---

**Note:**  
Normalization produces several smaller tables connected by keys. It helps maintain integrity and consistency, but more joins may reduce query performance – balance is key for large systems.

---
