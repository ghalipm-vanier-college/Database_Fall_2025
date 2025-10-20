# Module 10 - Introduction to Procedural SQL
## PL/SQL (Oracle) | <mark>`T-SQL (SQL Server)`</mark> | PL/pgSQL (PostgreSQL)

---

##  Learning Objectives

By the end of this lecture, students will be able to:
- Understand what **procedural SQL** is and how it extends standard SQL.
- Compare PL/SQL, **T-SQL**, and PL/pgSQL.
- Create and execute **Stored Procedures**, **Triggers**, and **Functions** in each.
- Handle **errors** and **transactions** using procedural SQL constructs.

---

## 1️⃣ What is Procedural SQL?

**SQL** is *nonprocedural*: it tells the database **what** to do but not **how**.

To support programmatic logic such as variables, loops, and conditions,  
database systems extend SQL into **Procedural SQL** languages.

| Database | Procedural Language | Vendor |
|-----------|--------------------|---------|
| Oracle | PL/SQL | Oracle Corporation |
| Microsoft SQL Server | T-SQL (Transact-SQL) | Microsoft |
| PostgreSQL | PL/pgSQL | PostgreSQL Global Development Group |

---

## 2️⃣ Key Features Comparison

| Feature | PL/SQL (Oracle) | <mark>T-SQL (SQL Server)</mark> | PL/pgSQL (PostgreSQL) |
|----------|-----------------|--------------------|------------------------|
| Variables | ✅ `DECLARE` section | ✅ `DECLARE @var` | ✅ `DECLARE var` |
| Flow Control | ✅ IF, LOOP, CASE | ✅ IF, WHILE, CASE | ✅ IF, LOOP, CASE |
| Error Handling | EXCEPTION block | TRY...CATCH | EXCEPTION block |
| Functions | CREATE FUNCTION | CREATE FUNCTION | CREATE FUNCTION |
| Procedures | CREATE PROCEDURE | CREATE PROCEDURE | CREATE PROCEDURE |
| Triggers | CREATE TRIGGER | CREATE TRIGGER | CREATE TRIGGER |
| Transaction Control | COMMIT / ROLLBACK | BEGIN TRANSACTION / COMMIT | BEGIN / COMMIT |
| Block Syntax | `BEGIN...END;` | `BEGIN...END;` | `BEGIN...END;` |

---

## 3️⃣ Structure of a Procedural Block

### 🧩 General Format (All Systems)
```sql
-- General block structure
[DECLARE Section]
BEGIN
   -- Executable Statements
[EXCEPTION / CATCH Section]
END;
````

---

## 4️⃣ Creating Stored Procedures

Stored Procedures are reusable, precompiled SQL blocks stored in the database.

### 🧩 Oracle (PL/SQL)

```sql
CREATE OR REPLACE PROCEDURE Show_All_Customers IS
BEGIN
   FOR rec IN (SELECT * FROM Customers) LOOP
      DBMS_OUTPUT.PUT_LINE(rec.FirstName || ' ' || rec.LastName);
   END LOOP;
END;
/
```

Execute:

```sql
EXEC Show_All_Customers;
```

---

### 🧩 SQL Server (T-SQL)

```sql
CREATE PROCEDURE ShowAllCustomers
AS
BEGIN
   SELECT * FROM Customers;
END;
GO

EXEC ShowAllCustomers;
```

---

### 🧩 PostgreSQL (PL/pgSQL)

```sql
CREATE OR REPLACE PROCEDURE Show_All_Customers()
LANGUAGE plpgsql
AS $$
BEGIN
   RAISE NOTICE 'Customer: %', (SELECT string_agg(firstname || ' ' || lastname, ', ') FROM customers);
END;
$$;

CALL Show_All_Customers();
```

---

## 5️⃣ Creating Functions

Functions return a **value** (scalar or table) and can be reused inside SQL queries.

### 🧩 Oracle (PL/SQL)

```sql
CREATE OR REPLACE FUNCTION GetFullName(fName VARCHAR2, lName VARCHAR2)
RETURN VARCHAR2 IS
BEGIN
   RETURN fName || ' ' || lName;
END;
/
```

Usage:

```sql
SELECT GetFullName('Alice', 'Brown') FROM dual;
```

---

### 🧩 SQL Server (T-SQL)

```sql
CREATE FUNCTION GetFullName(@FirstName VARCHAR(50), @LastName VARCHAR(50))
RETURNS VARCHAR(100)
AS
BEGIN
   RETURN @FirstName + ' ' + @LastName;
END;

SELECT dbo.GetFullName('Alice','Brown') AS FullName;
```

---

### 🧩 PostgreSQL (PL/pgSQL)

```sql
CREATE OR REPLACE FUNCTION GetFullName(fName TEXT, lName TEXT)
RETURNS TEXT AS $$
BEGIN
   RETURN fName || ' ' || lName;
END;
$$ LANGUAGE plpgsql;

SELECT GetFullName('Alice', 'Brown');
```

---

## 6️⃣ Creating Triggers

A **trigger** executes automatically in response to a database event.

### 🧩 Oracle (PL/SQL)

```sql
CREATE OR REPLACE TRIGGER trg_AuditOrder
AFTER INSERT ON Orders
FOR EACH ROW
BEGIN
   INSERT INTO Order_Audit(OrderID, ActionDate)
   VALUES (:NEW.OrderID, SYSDATE);
END;
/
```

---

### 🧩 SQL Server (T-SQL)

```sql
CREATE TRIGGER trg_AuditOrder
ON Orders
AFTER INSERT
AS
BEGIN
   INSERT INTO Order_Audit(OrderID, ActionDate)
   SELECT OrderID, GETDATE() FROM inserted;
END;
```

---

### 🧩 PostgreSQL (PL/pgSQL)

```sql
CREATE OR REPLACE FUNCTION AuditOrder()
RETURNS TRIGGER AS $$
BEGIN
   INSERT INTO Order_Audit(OrderID, ActionDate)
   VALUES (NEW.OrderID, NOW());
   RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_AuditOrder
AFTER INSERT ON Orders
FOR EACH ROW
EXECUTE FUNCTION AuditOrder();
```

---

## 7️⃣ Exception Handling

### 🧩 Oracle (PL/SQL)

```sql
BEGIN
   DECLARE
      v_div NUMBER;
   BEGIN
      v_div := 10 / 0;
   EXCEPTION
      WHEN ZERO_DIVIDE THEN
         DBMS_OUTPUT.PUT_LINE('Division by zero error!');
   END;
END;
/
```

---

### 🧩 SQL Server (T-SQL)

```sql
BEGIN TRY
   DECLARE @x INT = 10, @y INT = 0;
   SELECT @x / @y;
END TRY
BEGIN CATCH
   PRINT 'Division by zero error!';
END CATCH;
```

---

### 🧩 PostgreSQL (PL/pgSQL)

```sql
DO $$
DECLARE
   x INTEGER := 10;
   y INTEGER := 0;
   result INTEGER;
BEGIN
   result := x / y;
EXCEPTION
   WHEN division_by_zero THEN
      RAISE NOTICE 'Division by zero error!';
END;
$$;
```

---

## 8️⃣ Transaction Control

| Database | Commands                               |
| -------- | -------------------------------------- |
| PL/SQL   | `COMMIT;`, `ROLLBACK;`, `SAVEPOINT;`   |
| T-SQL    | `BEGIN TRANSACTION; COMMIT; ROLLBACK;` |
| PL/pgSQL | `BEGIN; COMMIT; ROLLBACK;`             |

### Example (T-SQL)

```sql
BEGIN TRY
   BEGIN TRANSACTION;
   INSERT INTO Orders VALUES (1, 501, 101, 2, 200);
   COMMIT TRANSACTION;
END TRY
BEGIN CATCH
   ROLLBACK TRANSACTION;
END CATCH;
```

---

## 9️⃣ Best Practices

✅ Use **Stored Procedures** for repeated business logic.

✅ **Functions** should return consistent results and not modify data.

✅ Use **Triggers** for auditing and enforcing business rules only.

✅ Always include **error handling** (EXCEPTION or TRY...CATCH).

✅ Test procedural SQL in isolation before deploying.

---

## 🔟 Summary Comparison

| Feature                  | PL/SQL (Oracle)                | T-SQL (SQL Server)     | PL/pgSQL (PostgreSQL)          |
| ------------------------ | ------------------------------ | ---------------------- | ------------------------------ |
| Block Syntax             | `DECLARE…BEGIN…EXCEPTION…END;` | `BEGIN TRY…END CATCH;` | `DECLARE…BEGIN…EXCEPTION…END;` |
| Output                   | `DBMS_OUTPUT.PUT_LINE`         | `PRINT`                | `RAISE NOTICE`                 |
| Parameter Prefix         | `:`                            | `@`                    | None                           |
| Function Return          | `RETURN value;`                | `RETURN value;`        | `RETURN value;`                |
| Triggers                 | `:NEW`, `:OLD`                 | `inserted`, `deleted`  | `NEW`, `OLD`                   |
| Exception Handling       | `EXCEPTION` block              | `TRY...CATCH`          | `EXCEPTION` block              |
| Default Transaction Mode | Implicit                       | Explicit               | Explicit                       |

---

## Practice Questions

1. Write a **stored procedure** to increase all product prices by 10% in each system.
2. Create a **function** that returns total sales by `CustomerID`.
3. Create a **trigger** to log every deleted product into a history table.
4. Modify your function to handle NULL values safely.
5. Implement error handling to avoid division-by-zero exceptions.

---

✅ **End of **
*PL/SQL – T-SQL – PL/pgSQL Overview*

```


