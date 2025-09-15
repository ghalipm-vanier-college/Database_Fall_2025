# From Entity-Relationship Model to Relational Model  
Lecture 4  
Nagat Drawel  

---

## Database Design Process  

### Requirements Collection and Analysis  
Database designers gather and understand the data requirements of database users.

### Conceptual Design  
- Create a conceptual schema that describes data requirements in detail.  
- Includes entity types, relationships, and constraints.  
- Independent of any specific DBMS.  

### Logical Design  
- Transform conceptual design into relational data model.  
- Ensures data structure suits relational database implementation.  

### Physical Design  
- Implement logical model considering DBMS and physical factors for optimal performance.  
- Create SQL statements for tables, views, procedures, and data insertion.  

---

## Phase 2: Logical Design  

- Input: E-R diagrams  
- Output: Relational schemas  
- Mapping E-R diagrams to relations follows a well-defined process:  
  - Each entity set → new table  
  - Each attribute → new table column  
  - Each relationship set → new columns or new table  

---

## Step 1: Mapping of Regular Entity Types  

- For each strong entity:  
  - Create a relation(table) named same as entity.  
  - Map simple attributes to relation attributes.  
  - For composite attributes, map simple component attributes.  
  - Exclude derived attributes.  
  - Choose key attribute(s) as primary key.  
  - Composite keys if key is composite.

---

### Example: CUSTOMER Entity  
- Composite attribute: customer-address (street, city, state, zip)  
- Map to multiple simple attributes: street, city, state, zip  

---

## Multi-valued Attributes  

- When entity has multi-valued attribute, create two relations:  
  1. One with entity attributes except multi-valued attribute.  
  2. One with the primary key from first relation + multi-valued attribute.  
- Name the second relation after the multi-valued attribute.

---

### Example: EMPLOYEE and Skills  
**EMPLOYEE Table:**  
employee-id, employee-name, employee-address   

**EMPLOYEE-SKILL Table:**  
employee-id, skill  

- Suggest adding new attributes like years-experience or certification-date.

---

## Step 2: Mapping of Binary 1:N Relationship Types  

- Add primary key of entity on the one side as foreign key to the many side.  
- Primary key migrates from one side to many side.

---

### Example: PAINTER paints PAINTING  

**PAINTER Table:**  
PAINTER_NUM (PK), PAINTER_LNAME, PAINTER_FNAME, PAINTER_INITIAL  

**PAINTING Table:**  
PAINTING_NUM (PK), PAINTING_TITLE, PAINTER_NUM (FK)  

---

## Step 3: Mapping of Binary 1:1 Relationship Types  

- Two options:  
  1. Add PK of Entity 1 as FK in Entity 2.  
  2. Add PK of Entity 2 as FK in Entity 1.  
- Efficient approach: PK on mandatory side becomes FK on optional side.  
- Avoids storing nulls in FK.

---

### Example: PROFESSOR chairs DEPARTMENT  

**PROFESSOR Table:**  
EMP_NUM (PK), DEPT_CODE (FK), PROF_OFFICE, PROF_EXTENSION, PROF_HIGH_DEGREE  

**DEPARTMENT Table:**  
DEPT_CODE (PK), DEPT_NAME, SCHOOL_CODE, EMP_NUM (FK), DEPT_ADDRESS, DEPT_EXTENSION  

- 1:M DEPARTMENT employs PROFESSOR relationship implemented by DEPT_CODE FK in PROFESSOR.  
- 1:1 PROFESSOR chairs DEPARTMENT implemented by EMP_NUM FK in DEPARTMENT.  

---

## Step 4: Mapping of Binary M:N Relationship Types  

- Create a new relation for M:N relationship.  
- Include as foreign keys the primary keys of related tables.  
- Combination of foreign keys form primary key of new relation.  
- Add any simple attributes of the relationship as attributes of the new relation.

---

### Example: STUDENT enrolled in CLASS  

**STUDENT Table:**  
STU_NUM (PK), STU_LNAME  

**CLASS Table:**  
CLASS_CODE (PK), CRS_CODE (FK), CLASS_SECTION, CLASS_TIME, CLASS_ROOM, PROF_NUM (FK)  

**ENROLL Table:**  
CLASS_CODE (PK, FK), STU_NUM (PK, FK), ENROLL_GRADE  

---

## Mapping a Unary (Recursive) 1:N Relationship  

- Foreign key references primary key of the same relation.  
- Example: EMPLOYEE manages EMPLOYEE.

---

### Example: EMPLOYEE_V2  

EMP_CODE (PK), EMP_LNAME, EMP_MANAGER (FK referencing EMP_CODE)  

| EMP_CODE | EMP_LNAME | EMP_MANAGER |
|----------|-----------|-------------|
| 101      | Waddell   | 102         |
| 102      | Orincona  |             |
| 103      | Jones     | 102         |
| ...      | ...       | ...         |

---

## Step 5: Mapping of Weak Entity Types  

- Create table for weak entity.  
- Include all attributes plus partial key and the primary key(s) of owner entity.  
- Primary key is combination of owner PK and weak entity partial key.

---

### Example: EMPLOYEE and DEPENDENT  

**EMPLOYEE Table:**  
employee-id (PK), employee-name  

**DEPENDENT Table:**  
dependent-name (partial key), employee-id (FK), DOB, gender  

| dependent-name | employee-id | DOB       | gender |
|----------------|-------------|-----------|--------|
| ...            | ...         | ...       | ...    |

---

## College Logical Schema Example   

- **DEPARTMENT**  
  DEP_ID (PK), DEPT_NAME, DEPT_LOC, PROF_ID (FK)  

- **PROFESSOR**  
  PROF_ID (PK), F_NAME, LNAME, DEP_ID (FK)  

- **COURSE**  
  COURSE_NO (PK), COURSE_TITLE, PROF_ID (FK), DEP_ID (FK)  

- **PROFESSOR_SKILL**  
  PROF_ID (PK, FK), SKILL, DEP_ID  

- **ENROLL**  
  STUD_NUM (PK, FK), COURSE_NO (PK, FK)  

- **STUDENT**  
  STUD_NUM (PK), F_NAME, LNAME, DOB  

---

## Example ERD Relationships  

- DEPARTMENT manages PROFESSOR (1:M)  
- PROFESSOR works for DEPARTMENT  
- PROFESSOR teaches COURSE  
- COURSE offered by DEPARTMENT  
- STUDENT enrolls in COURSE (M:N)

---

## Summary  

- Database design translates requirements into schemas gradually.  
- Conceptual schemas focus on entities and relationships (ER diagrams).  
- Logical design converts ER diagrams into relational schemas applying mapping rules.  
- Physical design implements schemas in a chosen DBMS with SQL code.  
- Mapping includes careful attention to keys, foreign keys, weak entities, and relationships.  

---

# End of Lecture 4
