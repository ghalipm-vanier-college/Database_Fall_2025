# Module 3: The Relational Database Model

---

## 3.1 Chapter Objectives
By the end of this chapter, you should be able to:
- Describe the **logical structure** of the relational database model
- Identify basic relational model components and describe relational tables
- Use relational **database operators** to manipulate tables
- Explain the **data dictionary** and **system catalog**
- Identify entities and relationships in the relational database model
- Handle **data redundancy**
- Explain the **purpose of indexing**

---

## 3.2 Logical View of Data
- The relational model focuses on **logical representation** of data (tables and relationships), not on physical details.
- Logical simplicity results in more effective database design.

### Section Summary
Designing databases with a logical focus abstracts away physical storage, making the system easier to understand, maintain, and adapt to changing needs.

**Prompt:**  
Why is designing your database logically before worrying about physical storage helpful?

---

## 3.3 Structure and Characteristics of Tables
- Tables are two-dimensional: **rows** (tuples) and **columns** (attributes).
- A table is also called a **relation**.
- Each row represents an entity instance.
- Each column has a distinct name and holds values from a defined **domain**.
- The order of rows/columns doesn’t matter to the DBMS.
- Each table has a **primary key** that uniquely identifies every row.

| Characteristic              | Description                                                      |
|----------------------------|------------------------------------------------------------------|
| Two-dimensional structure   | Rows and columns                                                 |
| Row = entity instance       | Column = attribute                                               |
| Every cell = single value   | Values in a column come from the same domain                     |
| Table must have unique PK   | Order of rows/columns immaterial                                 |

### Section Summary
Tables organize data systematically in rows and columns where each row uniquely identifies a record. This structure underpins relational databases’ efficiency and simplicity.

**Prompt:**  
Examine a spreadsheet—what features does it share with a relational table?

---

## 3.4 Keys: The Foundation of Relationships
- **Key:** Attribute(s) that determine other attributes.
- The key identifies each row and establishes relationships between tables.

### Types of Keys
- **Primary Key (PK):** Uniquely identifies each row; never null.
- **Composite Key:** Made up of more than one attribute.
- **Superkey:** Uniquely identifies rows with possibly extra attributes.
- **Candidate Key:** Minimal superkey without unnecessary attributes.
- **Foreign Key (FK):** PK from another table used to link tables.
- **Secondary Key:** Used for data retrieval, not as a relational determinant.

<img width="750" height="450" alt="image" src="https://github.com/user-attachments/assets/52fa1ff9-98ac-4901-9b84-69c171ec385a" />

Figure 3.2  An Example of a Simple Relational Database


- **Functional Dependence:** Knowing one attribute lets you find another.

### Section Summary
Keys are essential to uniquely identify records and link related data across tables, ensuring consistency and enabling powerful relational operations.

### Real-world Case Study  
In an e-commerce system, the **OrderID** is the primary key for purchases, ensuring every order is uniquely tracked. The **CustomerID** serves as a foreign key linking orders to the customer table, enabling seamless retrieval of customer order history.

**Prompt:**  
Why can’t a primary key ever be null? Give examples from real-world systems.

---

## 3.5 Data Integrity Rules

| Rule Name            | Requirement & Purpose                                      | Example                                   |
|----------------------|------------------------------------------------------------|-------------------------------------------|
| Entity Integrity      | PK entries are unique and cannot be null                   | No invoice can have duplicate or missing numbers |
| Referential Integrity | FK must be null or match a valid PK in the related table  | Customer's sales rep must exist             |

<img width="650" height="400" alt="image" src="https://github.com/user-attachments/assets/fefa25eb-7d33-4793-8239-78b4461f4103" />

Figure 3.3  An Illustration of Integrity Rules


### Section Summary  
Data integrity rules enforce correctness and reliability by ensuring unique identification of records and valid cross-table references.

### Real-world Case Study  
A student database must ensure that every enrollment record links to a valid student (referential integrity); otherwise, orphaned records would hinder accurate reporting and decision-making.

**Prompt:**  
What would happen if PK or FK rules were not enforced in a student database?

---

## 3.6 Relational Algebra Operators
- **Relational Algebra:** Mathematical foundation for relational database operations.

### Key Operators
- **SELECT:** Returns rows meeting criteria.
- **PROJECT:** Returns specified columns.
- **JOIN:** Combines tables based on common columns.
- **INTERSECT:** Returns rows common to two tables.
- **UNION:** Merges tables, removes duplicates.
- **DIFFERENCE:** Returns rows in one table not in the other.
- **PRODUCT:** All possible row pairs from two tables (Cartesian product).
- **DIVIDE:** Finds rows associated with all values in another table.

### Section Summary  
Relational algebra provides the theoretical foundation for querying and manipulating data, enabling robust, flexible data retrieval across multiple tables.

### Real-world Case Study  
A university database uses JOIN operations to combine student information with course registrations, allowing queries like "List all courses taken by a particular student."

**Activity:**  
Think of a question you could answer with JOIN or SELECT in your school’s database.

---

## 3.7 Formal Terms
- **Relation:** The actual table visible to users.
- **Relvar:** A variable that holds a relation (data).
  - **Heading:** The attribute names (column headers).
  - **Body:** The data (tuples/rows).

### Section Summary  
Formal terminology clarifies the distinction between concepts and physical data, facilitating communication among database designers and developers.

---

## 3.8 Joins Explained
- **Natural Join:** Combines tables on common attributes automatically.
- **Equijoin:** Joins tables by explicit equality conditions.
- **Theta Join:** Joins with other comparison operators.
- **Inner Join:** Returns only matched records.
- **Outer Join:** Retains unmatched records, filling unmatched fields with nulls.
  - Variants: left, right, full outer joins.

### Section Summary  
Joins are powerful tools that bring together related data across tables, tailored to different scenarios by the type of join used.

### Real-world Case Study  
A healthcare system uses outer joins to generate patient reports that include all scheduled appointments, even for patients without completed visits yet.

**Prompt:**  
When would you need to use a JOIN instead of a SELECT?

---

## 3.9 The Data Dictionary & System Catalog
- **Data Dictionary:** Catalog of all tables, structures, and design decisions—“the designer’s database.”
- **System Catalog:** Detailed dictionary of all database objects.
- Avoid using homonyms (same name, different meanings) and synonyms (different names, same meaning).

### Section Summary  
The data dictionary and system catalog ensure consistent understanding and documentation of database components across the development team and users.

**Prompt:**  
Why is it important for everyone to use the same attribute names in a database project?

---

## 3.10 Relationships within the Relational Model
- **1:M (One-to-Many):** Most common relationship.
- **1:1 (One-to-One):** Rare, for special cases.
- **M:N (Many-to-Many):** Requires a **composite entity** or **linking table** to convert into 1:M relationships.

| Type | How Implemented                           |
|-------|-----------------------------------------|
| 1:M   | PK in one table appears as FK in another |
| 1:1   | Shared PK or tightly linked separate tables |
| M:N   | New linking table with PKs from both entities |

### Section Summary  
Relational database relationships are implemented through keys and linking tables, translating real-world associations into structured, maintainable designs.

### Real-world Case Study  
In a library system, a linking table connects books and authors to handle many-to-many relationships, as one book can have multiple authors, and authors can write multiple books.

**Activity:**  
Draw a diagram showing students registered in many classes and classes with many students. How do you set up the tables?

---

## 3.11 Handling Data Redundancy
- Uses foreign keys and sound design to minimize unnecessary duplication.
- Intentional redundancy used cautiously for performance or historical tracking.

### Section Summary  
Controlling data redundancy improves consistency and saves storage, but deliberate duplication can aid reporting or backup.

**Prompt:**  
Think of a case where having duplicate data in a database would be helpful.

---

## 3.12 Indexes
- An index in SQL is a database object designed to improve the speed of data retrieval operations on a table or view.
- It functions similarly to an index in a book, providing a quick way to locate specific data without having to scan the entire table.

- Creating an Index:
Indexes are created using the CREATE INDEX statement.

```sql
CREATE INDEX index_name
ON table_name (column1, column2, ...);
```
- An **index** organizes data for fast retrieval.
- **Index key:** The attribute value used to locate data quickly.
- **Unique Index:** Points to only one record.
- Tables can have multiple indexes, each relating to a single table.


### Section Summary  
Indexes improve performance by reducing search times at the cost of additional storage and maintenance effort.

---

## 3.13 Dr. Codd’s Relational Database Rules
- Essential features defining relational databases such as:
  - Logical representation (tables).
  - Guaranteed access by table, PK, and column name.
  - Use and handling of nulls.
  - Availability of a catalog (data dictionary).
  - Comprehensive relational language.
  - Data integrity and independence.
  - Distribution and non-subversion.

| Rule         | Description                      |
|--------------|--------------------------------|
| Information  | Data stored only as values in tables |
| Guaranteed Access | Every value accessible by table, PK, and column name |
|                   |                        |

### Section Summary  
Dr. Codd’s rules provide a foundational framework ensuring relational databases behave predictably, reliably, and universally.

---

## 3.14 Knowledge Checks & Discussion
- What is the difference between a database and a table?
- Which relational algebra operators require union-compatible tables?
- Why is the data dictionary called "the designer's database"?

---

## 3.15 Summary
- The relational model emphasizes a table-based logical structure.
- Tables comprise rows, columns, and keys that uniquely identify records.
- Relational algebra operators form the basis for data querying and manipulation.
- Proper design enforces data integrity and controls redundancy.
- Data dictionary and system catalog document and support the database design.
- Indexing and Dr. Codd’s rules guide efficient, consistent database management.

### Crows Foot Symbol Summary
<img width="882" height="469" alt="image" src="https://github.com/user-attachments/assets/78423d6d-6781-4f79-8ea0-d807071b2401" />


### Final Prompt  
Which relational database feature do you think is most important for accurate, effective data management—and why?

---
`ACID (Atomicity, Consistency, Isolation, Durability) compliance` which ensures data remains accurate, complete, and reliable
