# Lecture: Data Manipulation Language (DML)

## What is DML?
- Part of SQL used to **manipulate data in tables**.
- Main statements:
  - **INSERT** → Add new rows
  - **UPDATE** → Modify existing rows
  - **DELETE** → Remove rows

---

## INSERT – Adding Data
**Syntax:**
```sql
INSERT INTO TableName [(columnList)]
VALUES (valueList);
```
  * columnList is optional (default = all columns).
  * valueList must match column list in number, order, and data types.
  * Foreign keys must be NULL or reference an existing parent value.
  * Integrity constraints (PK, NOT NULL, UNIQUE) must be satisfied.

Examples:
-- Insert into all columns
```sql
INSERT INTO Staff
VALUES ('SG16','Alan','Brown','Assistant','M','1957-05-25',8300,'B003');
```
-- Insert into selected columns
```sql
INSERT INTO Staff (staffNo,fName,lName,position,salary,branchNo)
VALUES ('SG44','Anne','Jones','Assistant',8100,'B003');
```
-- Insert from another table
```sql
INSERT INTO StaffCount
SELECT staffNo, fName, lName FROM Staff;
```
## UPDATE – Modifying Data

Syntax:
```sql
UPDATE TableName
SET column1 = value1, column2 = value2
[WHERE condition];
```

  * Without WHERE, all rows are updated.
  * Can change multiple columns at once.
  * Data types must be compatible.

Examples:

-- Increase all salaries by 3%
```sql
UPDATE Staff SET salary = salary * 1.03;
```
-- Increase only Managers’ salaries by 5%
```sql
UPDATE Staff
SET salary = salary * 1.05
WHERE position = 'Manager';
```
-- Promote and update salary
```sql
UPDATE Staff
SET position = 'Manager', salary = 18000
WHERE staffNo = 'SG14';
```
## DELETE – Removing Data

Syntax:
```sql
DELETE FROM TableName
[WHERE condition];
```
  * Without WHERE, all rows are deleted.
  * If row is referenced by a foreign key, deletion may fail unless:
    * ON DELETE CASCADE or ON DELETE SET NULL is defined.

Examples:

-- Delete specific rows
```sql
DELETE FROM Viewing
WHERE propertyNo = 'PG4';
```
-- Delete all rows
```sql
DELETE FROM Viewing;
```
## Common Errors

  * Violating constraints:
    * CHECK: inserting credits = 6 when allowed range is 0–4.
    * UNIQUE: inserting duplicate title.
    * NOT NULL: omitting a required column.
    * PRIMARY KEY: inserting duplicate or NULL key.

* Foreign key violations:
  * Inserting a non-existing reference.
  * Deleting a parent row while child rows still exist.

## Transactions
* A transaction = set of SQL statements executed as a unit.
* Either all succeed or all fail (rolled back).

### Commands:
  * BEGIN TRANSACTION → start transaction
  * COMMIT → save changes permanently
  * ROLLBACK → undo changes

### Example:
```sql
BEGIN TRANSACTION;
UPDATE Account SET balance = balance - 500 WHERE accNo = 3209;
UPDATE Account SET balance = balance + 500 WHERE accNo = 3208;
INSERT INTO Journal VALUES ('Transfer 500 from 3209 to 3208');
COMMIT;
```
### Savepoint:
```sql
SAVE TRAN savepoint_name;
ROLLBACK TRAN savepoint_name;
```
## TRUNCATE vs DELETE

* TRUNCATE TABLE tableName;
  * Removes all rows quickly.
  * DDL command, cannot use WHERE.
  * Cannot be rolled back in most DBs.

* DELETE FROM tableName;
  * Removes rows, optionally with WHERE.
  * DML command, can be rolled back.

Comparison:
| Feature  | DELETE              | TRUNCATE            |
| -------- | ------------------- | ------------------- |
| Clause   | Supports `WHERE`    | No `WHERE`          |
| Type     | DML                 | DDL                 |
| Rollback | Possible            | Not possible        |
| Speed    | Slower (row by row) | Faster (deallocate) |

Note: 

* If a parent row is referenced by a child table’s foreign key, it cannot be deleted (error occurs).
* To allow deletion: define the foreign key with ON DELETE CASCADE (delete child rows too) or ON DELETE SET NULL (set child key to NULL).
* Instead of :
*
 ```sql
CREATE TABLE CUSTOMER (
customer_id INT PRIMARY KEY,
first_name VARCHAR(50),
last_name VARCHAR(50),
email VARCHAR(100),
city VARCHAR(50),
zip_code VARCHAR(10)
);

CREATE TABLE ORDERS (
order_id INT PRIMARY KEY,
customer_id INT,
order_date DATE,
amount DECIMAL(10,2),
status VARCHAR(20),
FOREIGN KEY (customer_id) REFERENCES CUSTOMER(customer_id)
);
```
if you have:
```sql
CREATE TABLE CUSTOMER (
customer_id INT PRIMARY KEY,
first_name VARCHAR(50),
last_name VARCHAR(50),
email VARCHAR(100),
city VARCHAR(50),
zip_code VARCHAR(10)
);

CREATE TABLE ORDERS (
order_id INT PRIMARY KEY,
customer_id INT,
order_date DATE,
amount DECIMAL(10,2),
status VARCHAR(20),
FOREIGN KEY (customer_id) REFERENCES CUSTOMER(customer_id) ON DELETE CASCADE
);
```
Then when you delete a row from Customer in a transaction, also hanging/asscociated row from the Orders table will be deleted without issues
