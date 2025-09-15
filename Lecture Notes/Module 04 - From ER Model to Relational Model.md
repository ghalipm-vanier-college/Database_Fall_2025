# From Entity-Relationship Model to Relational Model  

### Crows Foot Symbol Summary
<img width="882" height="469" alt="image" src="https://github.com/user-attachments/assets/78423d6d-6781-4f79-8ea0-d807071b2401" />

---
## Database Design Process

<table>
<tr>
<td width="60%" valign="top">

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

</td>
<td width="40%" valign="top">
<br>
<img width="414" height="549" alt="image" src="https://github.com/user-attachments/assets/63072a18-bbd0-49c3-8268-59a56254c909" />

</td>
</tr>
</table>

---

## Logical Design  

* During the logical design phase, you transform the conceptual design into relational database
schemas

* The input to this process is the ER diagrams, and the output is the relational schemas

* Mapping the ER diagrams to relations is a relatively straightforward process with a well defined set
of rules

<table>
<tr>
<td width="50%" valign="top">
 <b>Input: E-R diagrams  
  
 Output: Relational schemas  
 </b>
 * `ER diagrams` are a `high-level abstraction`,
 * `Relational schemas` provide a lower-level, more `detailed view of the database structure`
   
  - Mapping E-R diagrams to relations follows a well-defined process:  
    - Each entity set → new table  
    - Each attribute → new table column  
    - Each relationship set → new columns or new table  
</td>
<td width="50%" valign="top">
<br>
<img width="699" height="531" alt="image" src="https://github.com/user-attachments/assets/bff79d38-a174-410b-a42c-34a7282427c4" />
</td>
</tr>
</table>

---

## Step 1: Mapping of Regular Entity Types  
* A `Strong Entity` is a type of entity that `has a key Attribute` that can `uniquely identify` each instance of the entity. A Strong Entity does not depend on any other Entity in the Schema for its identification.
* A `Weak Entity` cannot be uniquely identified by its own attributes alone. It `depends on a strong entity to be identified`. A weak entity is associated with an identifying entity (strong entity), which helps in its identification. A weak entity are represented by a double rectangle.

<img width="650" height="166" alt="image" src="https://github.com/user-attachments/assets/9a9fd344-32c3-4d56-885f-bc97522ca9be" />

  
- For each strong entity:  
  - Create a relation(table) named same as entity.  
  - Map simple attributes to relation attributes.
  - <img width="606" height="154" alt="image" src="https://github.com/user-attachments/assets/0c46ac0a-8611-4eff-842a-e1ffc98d0710" />
  - For `composite attributes`(attribute with attributes), map simple component attributes.  
  - Exclude derived attributes.  
  - Choose key attribute(s) as primary key.  
  - Composite keys if key is composite.

---

### Example: CUSTOMER Entity  
- Composite attribute: customer-address (street, city, state, zip)  
- Map to multiple simple attributes: street, city, state, zip  
- <img width="899" height="141" alt="image" src="https://github.com/user-attachments/assets/3817d197-1c39-461b-ab1a-f53eafe7d08c" />

    <img width="480" height="380" alt="image" src="https://github.com/user-attachments/assets/aeaf2aed-b0a8-4970-b32d-b48f1278f916" />
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

- <img width="748" height="192" alt="image" src="https://github.com/user-attachments/assets/e42578fc-5668-4ea8-8056-4f791ec77fe2" />


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

<img width="939" height="393" alt="image" src="https://github.com/user-attachments/assets/262b0906-dcfb-4e34-840e-635ce4a39e45" />


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
- <img width="924" height="440" alt="image" src="https://github.com/user-attachments/assets/062b45b2-7185-4f5b-89fe-1212e79f30a2" />


---

## Step 4: Mapping of Binary M:N Relationship Types  

- Create a new relation for M:N relationship.  
- Include as foreign keys the primary keys of related tables.  
- Combination of foreign keys form primary key of new relation.  
- Add any simple attributes of the relationship as attributes of the new relation.
- <img width="518" height="207" alt="image" src="https://github.com/user-attachments/assets/e6af65c1-4cef-451d-b6d6-18564a1d31e6" />

<img width="928" height="425" alt="image" src="https://github.com/user-attachments/assets/05220608-a251-4a7d-9e63-f9e302e05b93" />



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

<img width="725" height="293" alt="image" src="https://github.com/user-attachments/assets/ea7f782c-f7f9-4ff8-aa75-8c9d02b990c0" />


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

<img width="956" height="296" alt="image" src="https://github.com/user-attachments/assets/dbdfcfc6-d346-43cd-b1e5-3b8c26eac8dd" />

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
<img width="739" height="410" alt="image" src="https://github.com/user-attachments/assets/5256bc94-d945-410c-bb89-cf93c9f547ac" />

---

## Example ERD Relationships  

- DEPARTMENT manages PROFESSOR (1:M)  
- PROFESSOR works for DEPARTMENT  
- PROFESSOR teaches COURSE  
- COURSE offered by DEPARTMENT  
- STUDENT enrolls in COURSE (M:N)
<img width="559" height="404" alt="image" src="https://github.com/user-attachments/assets/c12c7ed7-c9fb-4b52-a0b7-da7ce70ca10f" />

---

## Summary  

- Database design translates requirements into schemas gradually.  
- Conceptual schemas focus on entities and relationships (ER diagrams).  
- Logical design converts ER diagrams into relational schemas applying mapping rules.  
- Physical design implements schemas in a chosen DBMS with SQL code.  
- Mapping includes careful attention to keys, foreign keys, weak entities, and relationships.  

---

# End of Lecture 4
