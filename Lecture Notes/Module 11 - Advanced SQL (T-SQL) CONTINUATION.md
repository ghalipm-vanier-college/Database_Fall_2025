
---

##  Learning Objectives

- Understand the role of **T-SQL (Transact-SQL)** in Microsoft SQL Server.
- Declare and use **variables** in procedural SQL blocks.
- Write **conditional logic** with `IF`, `CASE`, and **looping** with `WHILE`.
- Implement **error handling** using `TRY...CATCH`.
- Perform **transactions** with `BEGIN TRANSACTION`, `COMMIT`, and `ROLLBACK`.

---

## 1️⃣ Introduction to T-SQL

**T-SQL (Transact-SQL)** is Microsoft’s procedural extension of SQL, used in SQL Server and Sybase.

- **SQL** tells the database *what to do*.
- **T-SQL** tells the database *how to do it* — with logic, flow control, and error handling.

### 🧩 Key Features

| Feature | Description |
|----------|--------------|
| Variables | Store temporary values with `DECLARE` |
| Control Flow | `IF`, `CASE`, `WHILE` statements |
| Exception Handling | `TRY...CATCH` blocks |
| Transaction Control | `BEGIN TRANSACTION`, `COMMIT`, `ROLLBACK` |
| Procedural Units | Anonymous blocks, procedures, triggers, functions |

---

## 2️⃣ Anonymous T-SQL Blocks

A **block** groups one or more SQL statements into a single executable unit.

### 🧩 Syntax
```sql
BEGIN
   { sql_statement | statement_block }
END;
```

### ✅ Example: Insert and Print Message
```sql
BEGIN
   INSERT INTO Staff VALUES ('SE12', 'John', 'Smith', NULL, 'M', NULL, 12300, 'B002');
   PRINT 'New Staff Added!';
END;
```

---

## 3️⃣ Declaring Variables

Variables are used to store temporary values.

### 🧩 Syntax
```sql
DECLARE @variable_name datatype [= initial_value];
```

### 🧩 Example
```sql
BEGIN
   DECLARE @vLastName VARCHAR(30), @vFirstName VARCHAR(30);
   SET @vLastName = 'Ford';
   SELECT @vFirstName = fName FROM Staff WHERE LName = @vLastName;
   PRINT @vFirstName + ' ' + @vLastName;
END;
```

---

## 4️⃣ Conditional Statements (IF / ELSE)

### 🧩 Syntax
```sql
IF (condition)
   statement_block
ELSE
   statement_block
```

### ✅ Example 1 – Count Employees in a Branch
```sql
BEGIN
   DECLARE @VCOUNT INT, @VBRANCHNO CHAR(5) = 'B003';
   SELECT @VCOUNT = COUNT(*) FROM STAFF WHERE BRANCHNO = @VBRANCHNO;

   IF @VCOUNT > 3
      PRINT 'Branch B003 has 4 or more employees';
   ELSE
   BEGIN
      PRINT 'Employees in Branch B003:';
      SELECT * FROM STAFF WHERE BRANCHNO = @VBRANCHNO;
   END;
END;
```

### ✅ Example 2 – Update Salaries Conditionally
```sql
BEGIN
   DECLARE @VPOSITION VARCHAR(20);
   SELECT @VPOSITION = POSITION FROM STAFF WHERE STAFFNO = 'SL21';

   IF @VPOSITION = 'Manager'
      UPDATE STAFF SET Salary = Salary * 1.05 WHERE POSITION = 'Manager';
   ELSE
      UPDATE STAFF SET Salary = Salary * 1.10 WHERE POSITION <> 'Manager';
END;
```

---

## 5️⃣ CASE Statement

A **CASE** expression chooses which action to perform based on a value.

### 🧩 Syntax
```sql
CASE 
   WHEN condition1 THEN result1
   WHEN condition2 THEN result2
   ELSE resultN
END;
```

### ✅ Example – Salary Increase by Position
```sql
BEGIN
   UPDATE Staff
   SET Salary = CASE
      WHEN Position = 'Manager' THEN Salary * 1.05
      WHEN Position = 'Assistant' THEN Salary * 1.03
      ELSE Salary * 1.02
   END;
END;
```

### ✅ Example – Semester Code Conversion
```sql
BEGIN
   DECLARE @semester CHAR(1) = 'F', @result VARCHAR(20);

   IF @semester NOT IN ('S', 'W', 'F')
      PRINT 'Invalid code';
   ELSE
   BEGIN
      SET @result = CASE
         WHEN @semester = 'F' THEN 'Fall Term'
         WHEN @semester = 'W' THEN 'Winter Term'
         WHEN @semester = 'S' THEN 'Summer Term'
      END;
      PRINT @result;
   END;
END;
```

---

## 6️⃣ WHILE Loop

Used for **iteration** — repeats as long as a condition is true.

### 🧩 Syntax
```sql
WHILE (condition)
BEGIN
   -- statements
END;
```

### ✅ Example – Print Numbers 1 to 5
```sql
BEGIN
   DECLARE @counter INT = 1;
   WHILE @counter <= 5
   BEGIN
      PRINT @counter;
      SET @counter = @counter + 1;
   END;
   PRINT 'End of Program!';
END;
```

### ✅ Example – Salary Loop with BREAK / CONTINUE
```sql
BEGIN
   WHILE (SELECT SUM(Salary) FROM Staff) > 18000
   BEGIN
      UPDATE Staff SET Salary = Salary * 1.1;

      IF (SELECT MAX(Salary) FROM Staff) > 24000
      BEGIN
         PRINT 'We are at BREAK section';
         BREAK;
      END;
      ELSE
      BEGIN
         PRINT 'We are at CONTINUE section';
         CONTINUE;
      END;
   END;

   PRINT 'End of My Program';
END;
```

---

## 7️⃣ Exception Handling (TRY...CATCH)

T-SQL uses **TRY...CATCH** blocks to handle errors gracefully.

### 🧩 Basic Example
```sql
BEGIN
   DECLARE @value1 NUMERIC = 20, @value2 NUMERIC = 0, @result NUMERIC;

   BEGIN TRY
      SET @result = @value1 / @value2;
      PRINT @result;
   END TRY
   BEGIN CATCH
      PRINT 'Divide by ZERO -- Error!';
   END CATCH;
END;
```

---

## 8️⃣ Transactions in T-SQL

Transactions ensure **data integrity**.  
If any statement fails, all changes are rolled back.

### 🧩 Syntax
```sql
BEGIN TRY
   BEGIN TRANSACTION;

   -- SQL operations
   COMMIT TRANSACTION;
   PRINT 'Transaction committed.';
END TRY
BEGIN CATCH
   ROLLBACK TRANSACTION;
   PRINT 'Transaction rolled back.';
END CATCH;
```

### ✅ Example – Insert Staff with Error Handling
```sql
BEGIN
   BEGIN TRY
      BEGIN TRANSACTION;

      INSERT INTO Staff VALUES ('SA10','John','Smith',NULL,'M',NULL,33000,'B002');
      INSERT INTO Staff VALUES ('SW21','Matthew','Jones',NULL,'M',NULL,33000,'B003');
      INSERT INTO Staff VALUES ('SW01','John','Barrimore',NULL,'F',NULL,13000,'B009'); -- invalid FK

      COMMIT TRANSACTION;
      PRINT 'Transaction committed.';
   END TRY
   BEGIN CATCH
      ROLLBACK TRANSACTION;
      PRINT 'Transaction rolled back.';
   END CATCH;
END;
```

---

##  Practice Exercises

1. Write a T-SQL block that inserts a new row into the **Branch** table and prints `"New Branch Added!"`.
2. Declare variables for a staff member’s first and last name, retrieve their full name, and print it.
3. Write a T-SQL script that checks if a branch has more than 5 staff; print a message accordingly.
4. Use a **CASE** statement to update salaries based on positions.
5. Write a **WHILE** loop that prints numbers 10 down to 1.
6. Perform a transaction with two inserts and one invalid insert — handle rollback properly.

---

✅ **End of Module 11 – Advanced SQL: T-SQL**

---

