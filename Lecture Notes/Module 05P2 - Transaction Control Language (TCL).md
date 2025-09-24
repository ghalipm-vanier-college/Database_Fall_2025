# Transaction Control Language (TCL) in SQL  
**Commands: COMMIT, ROLLBACK, SAVEPOINT**

---

## What is TCL?

* Transaction Control Language (TCL) manages changes made by DML statements (`INSERT`, `UPDATE`, `DELETE`) within transactions. 
* Using TCL, you can save, undo, or segment your transaction logic for safety and control.

---

## Key TCL Commands

### 1. COMMIT

- Permanently saves changes made during the current transaction.

- **Syntax:**

- **Usage:** Once data is committed, it cannot be undone. Use after a group of successful DML statements to make changes permanent.

---

### 2. ROLLBACK

- Undoes changes made in the current transaction that have not yet been committed.
- **Syntax:**

- **Usage:** Use when an error occurs or you want to discard all changes made during the transaction since the last COMMIT.

---

### 3. SAVEPOINT

- Creates a named point within a transaction to which you can later rollback.

- **Syntax:**

- **Usage:** Allows you to rollback only part of a transaction, not the entire set of changes.

---
## TCL (Transaction Control Language) Example: CUSTOMER and ORDERS

This working example demonstrates the use of COMMIT, ROLLBACK, and SAVEPOINT commands in SQL within the **CUSTOMER** and **ORDERS** tables.

---

### 1. Table Creation

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


---

### 2. Data Insertion

```sql
INSERT INTO CUSTOMER (customer_id, first_name, last_name, email, city, zip_code) VALUES
(1, 'Grace', 'Hopper', 'grace@example.com', 'San Jose', '95112'),
(2, 'Gary', 'Cooper', NULL, 'Seattle', '98101'),
(3, 'Anna', 'Smith', 'anna@example.com', 'Chicago', '60610'),
(4, 'Bella', 'Jones', 'bella@example.com', 'Seattle', '98102'),
(5, 'Mark', 'Lee', '', 'San Jose', '95113'),
(6, 'Oliver', 'Brown', 'oliverb@example.com', 'New York', '10001'),
(7, 'Megan', 'Gale', NULL, 'San Jose', '95112');

INSERT INTO ORDERS (order_id, customer_id, order_date, amount, status) VALUES
(100, 1, '2025-10-01', 2500.00, 'PAID'),
(101, 1, '2025-11-01', 150.00, 'SHIPPED'),
(102, 1, '2026-01-02', 1500.00, 'PENDING'),
(103, 2, '2025-08-15', 1200.00, 'PAID'),
(104, 3, '2025-07-06', 3000.00, 'PAID'),
(105, 2, '2025-10-01', 6000.00, 'SHIPPED'),
(106, 5, '2025-08-20', 6500.00, 'PAID'),
(107, 5, '2025-12-20', 2500.00, 'PAID'),
(108, 4, '2025-05-21', 2200.00, 'CANCELLED'),
(109, 7, '2025-11-12', 2300.00, 'SHIPPED');
```


---

### 3. Transaction Control Example
```sql
-- Start Transaction
BEGIN TRANSACTION;

-- Step 1: Insert a new customer and create a savepoint
INSERT INTO CUSTOMER VALUES (8, 'Nina', 'Young', 'nina@example.com', 'Chicago', '60615');
SAVE TRAN add_customer;

-- Step 2: Insert new order for Nina and create another savepoint
INSERT INTO ORDERS VALUES (110, 8, '2025-12-22', 2700.00, 'PENDING');
SAVE TRAN add_order;

-- Step 3: Suppose you realize the order should be PAID, not PENDING
UPDATE ORDERS SET status = 'PAID' WHERE order_id = 110;

-- Step 4: Unexpected error occurs, so rollback to add_order (undo status update)
ROLLBACK TRAN add_order;

-- Step 5: Commit all changes so far (customer added, order with PENDING status created)
COMMIT;

-- At this point:
-- Customer Nina is added.
-- Nina's order is present with status 'PENDING'.
-- The status update to 'PAID' was rolled back.
```

---

### Summary Table

| Command    | Purpose                       | Typical Use Case                              |
|------------       |------------------------------|-----------------------------------------------|
| COMMIT            | Save all work in transaction | After successful DML operations               |
| ROLLBACK (TRAN)   | Undo current transaction     | If errors or unwanted changes occur           |
| SAVEPOINT         | Set rollback marker          | To allow partial undos inside a transaction   |
|(SAVE TRAN)        | SQL SERVER  SPECIFIC         |                                               |

---

## Key Points

- TCL commands only affect changes made by DML statements.
- Changes are **not permanent** until you use `COMMIT`.
- `ROLLBACK TRAN` undoes *all* uncommitted changes or up to a savepoint.
- `SAVEPOINT`(SAVE TRAN) makes advanced rollback possible within a transaction.

- **BEGIN TRANSACTION** starts the transaction session.
- **SAVEPOINT(SAVE TRAN)** marks points you can roll back to.
- **ROLLBACK (TO) add_order** undoes only changes after that savepoint (restores order status to 'PENDING').
- **COMMIT** saves all changes since the transaction started.


---

**Notice:** Use TCL commands to ensure reliable transaction control, maintain data integrity, and prevent unwanted changes in databases.
