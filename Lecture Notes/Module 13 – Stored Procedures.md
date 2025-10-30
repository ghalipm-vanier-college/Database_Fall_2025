#  Module 13 – Stored Procedures  
### SQL Server – Advanced SQL Programming  

---

## Learning Objectives

- Understand what **stored procedures** are and why they are used.  
- Create, modify, and execute stored procedures.  
- Pass **input**, **output**, and **return** parameters.  
- Use **TRY...CATCH** for exception handling inside procedures.  
- Differentiate between **functions** and **procedures** in SQL Server.  

---

## 1️⃣ What is a Stored Procedure?

A **stored procedure** is a named and precompiled block of **Transact-SQL (T-SQL)** statements that performs a specific task.  
It is stored in the database, compiled once and can be executed/called multiple times by name compared to function which is compiled each time it is called.

### ✅ Benefits
- Encapsulates **business logic** (e.g., updating discounts, deleting customers).  
- Improves **performance** — executes on the database server.  
- Increases **security** — users can run procedures without accessing tables directly.  
- Enhances **maintainability** — logic changes are made in one central location.  

---

## 2️⃣ Stored Procedure Syntax

```sql
CREATE PROCEDURE procedure_name
   [ @param_name datatype [= default_value] [OUTPUT], ... ]
AS
BEGIN
   -- Executable statements
   -- Optional logic, conditions, loops, transactions
END;
GO
```

### Notes:
- Use `ALTER PROCEDURE` to modify an existing procedure.  
- Use `DROP PROCEDURE` to remove it.  
- Parameters can be:
  - **Input** parameters (default)
  - **Output** parameters (`OUTPUT`)
  - **Return values** (using `RETURN`)

---

## 3️⃣ Example 1: Simple Stored Procedure

### Task:
Reduce product discounts by 5% for items whose quantity on hand is at least **twice the minimum**.

```sql
CREATE PROCEDURE PRC_Prod_Discount
AS
BEGIN
   UPDATE Product
   SET P_DISCOUNT = P_DISCOUNT -0.05   -- Reduce discount by 5%
   WHERE P_QOH >= (P_MIN * 2);

   PRINT '✅ Product discounts reduced by 5% successfully.';
END;
GO

-- Execute
EXEC PRC_Prod_Discount;
```

---

## 4️⃣ Example 2: Passing Input Parameters

Make the **discount rate** adjustable by passing it as a parameter.

```sql
CREATE PROCEDURE Price_After_Discount @PDR DECIMAL(4,2)  -- Product Discount Rate (e.g., 0.05 = 5%)
AS
BEGIN
   IF (@PDR <= 0 OR @PDR >= 1)
      PRINT '⚠️ ERROR: Value must be greater than 0 and less than 1.';
   ELSE
   BEGIN
      UPDATE Product
      SET P_Price = P_Price * (1 - @PDR)
      WHERE P_QOH >= (P_MIN * 2);

      PRINT '✅ Product prices updated successfully.';
   END;
END
GO

SELECT * FROM Product;
-- Execute with 5% discount reduction
EXEC Price_After_Discount 0.07;
SELECT * FROM Product;
GO
DROP PROCEDURE Price_After_Discount;
GO
```

---

## 5️⃣ Example 3: Deleting a Customer by ID (Basic Version)

```sql
CREATE PROCEDURE PROC_Remove_Customer_By_Id
   @CustID INT
AS
BEGIN
   DECLARE @CustomerCount INT;
   SELECT @CustomerCount = COUNT(*) FROM Customer WHERE CUS_CODE = @CustID;

   IF @CustomerCount = 0
      PRINT 'No customer found with this ID:' +cast(@CustID as varchar(5))+'';
   ELSE
   BEGIN
      DELETE FROM Customer WHERE CUS_CODE = @CustID;
      PRINT 'Customer with the ID '+cast(@CustID as varchar(5))+ ' deleted successfully.';
   END
END;
GO

EXEC PROC_Remove_Customer_By_Id 1003;

SELECT * FROM CUSTOMER;
```

---

## 6️⃣ Example 4: Prevent Deleting Customers with Invoices

```sql
CREATE PROCEDURE PROC_Remove_Customer
   @CustID INT
AS
BEGIN
   DECLARE @InvoiceCount INT;
   SELECT @InvoiceCount = COUNT(*) FROM Invoice WHERE CUS_CODE = @CustID;

   IF @InvoiceCount > 0
      PRINT ' Cannot delete: This customer has existing invoices.';
   ELSE
   BEGIN
      DELETE FROM Customer WHERE CUS_CODE = @CustID;
      PRINT ' Customer deleted successfully.';
   END
END;
GO

EXEC PROC_Remove_Customer 10019;
```

---

## 7️⃣ Example 5: Using TRY...CATCH for Error Handling

```sql
CREATE PROCEDURE PROC_Remove_Customer
   @CustID INT
AS
BEGIN
   BEGIN TRY
      DELETE FROM Customer WHERE CUS_CODE = @CustID;
      PRINT ' Customer deleted successfully.';
   END TRY
   BEGIN CATCH
      PRINT ' Error: Customer cannot be deleted (may have related invoices).';
   END CATCH
END;
GO

EXEC PROC_Remove_Customer 10019;
```

---

## 8️⃣ Example 6: Using OUTPUT Parameters

```sql
CREATE PROCEDURE Count_Customers
   @CustCount INT OUTPUT
AS
BEGIN
   SELECT @CustCount = COUNT(*) FROM Customer;
END;
GO

-- Execute procedure and retrieve result
DECLARE @Count INT;
EXEC Count_Customers @CustCount = @Count OUTPUT;
PRINT 'The total number of customers = ' + CAST(@Count AS VARCHAR);
```

---

## 9️⃣ Example 7: Add New Invoice with Validation

```sql
CREATE PROCEDURE PRC_Add_Invoice
   @InvNum INT,
   @CusNum INT
AS
BEGIN
   IF EXISTS (SELECT * FROM Invoice WHERE INV_NUMBER = @InvNum)
      PRINT ' Invoice number already exists.';
   ELSE IF EXISTS (SELECT * FROM Customer WHERE CUS_CODE = @CusNum)
   BEGIN
      INSERT INTO Invoice (INV_NUMBER, CUS_CODE, INV_DATE)
      VALUES (@InvNum, @CusNum, GETDATE());
      PRINT ' Invoice added successfully.';
   END
   ELSE
      PRINT ' Invalid customer number.';
END;
GO

EXEC PRC_Add_Invoice 1015, 10011;
```

---

### Version 2 – Using TRY...CATCH

```sql
CREATE PROCEDURE PRC_Add_Invoice
   @InvNum INT,
   @CusNum INT
AS
BEGIN TRY
   INSERT INTO Invoice (INV_NUMBER, CUS_CODE, INV_DATE)
   VALUES (@InvNum, @CusNum, GETDATE());
   PRINT 'Invoice inserted successfully.';
END TRY
BEGIN CATCH
   PRINT ' Error: Invoice could not be added.';
END CATCH;
GO

EXEC PRC_Add_Invoice 1016, 10012;
```

---

## 🔟 Example 8: Add a New Customer Safely

```sql
CREATE PROCEDURE PRC_Add_Customer
   @CusCode INT,
   @LName VARCHAR(30),
   @FName VARCHAR(30),
   @Init CHAR(1),
   @AreaCode CHAR(5),
   @Phone VARCHAR(15)
AS
BEGIN TRY
   INSERT INTO Customer (CUS_CODE, CUS_LNAME, CUS_FNAME, CUS_INITIAL, CUS_AREACODE, CUS_PHONE)
   VALUES (@CusCode, @LName, @FName, @Init, @AreaCode, @Phone);

   PRINT ' Customer inserted successfully.';
END TRY
BEGIN CATCH
   PRINT ' Error inserting customer. Please check if the ID already exists.';
END CATCH;
GO
```

---

## 1️⃣1️⃣ Example 9: Delete Invoice by Number with Validation

```sql
CREATE PROCEDURE PRC_Inv_Delete
   @InvoiceNumber INT
AS
BEGIN
   IF EXISTS (SELECT * FROM Invoice WHERE INV_NUMBER = @InvoiceNumber)
   BEGIN
      IF EXISTS (SELECT * FROM Line WHERE INV_NUMBER = @InvoiceNumber)
         PRINT ' Cannot delete invoice: referenced in Line table.';
      ELSE
      BEGIN
         DELETE FROM Invoice WHERE INV_NUMBER = @InvoiceNumber;
         PRINT ' Invoice deleted successfully.';
      END
   END
   ELSE
      PRINT ' No invoice found with that number.';
END;
GO

EXEC PRC_Inv_Delete 1009;
```

---

## 1️⃣2️⃣ Advantages of Stored Procedures

| Advantage | Description |
|------------|-------------|
| **Security** | Limits direct access to tables — users can execute predefined logic. |
| **Performance** | Executes faster due to precompilation and reduced network traffic. |
| **Maintainability** | Business logic stored centrally and easier to update. |
| **Reusability** | Procedures can be called from multiple applications or scripts. |
| **Error Handling** | TRY...CATCH ensures clean error management. |

---

## 1️⃣3️⃣ Functions vs. Procedures – Key Comparison

| Feature | Function | Procedure |
|----------|-----------|-----------|
| Must return a value | ✅ Yes | ❌ Optional |
| Parameters | Input only | Input / Output |
| Can modify database | ❌ No | ✅ Yes |
| Supports TRY...CATCH | ❌ No | ✅ Yes |
| Use in SELECT | ✅ Yes | ❌ No |
| Can call each other | Function → Function | Procedure → Function (✅) |
| Compilation | Executes each call | Precompiled (cached) |

---

✅ **End of Module 13 – Stored Procedures (T-SQL)**  

