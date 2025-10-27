
#  Module 12 – User-Defined Functions (UDFs)
### SQL Server – T-SQL Programming

---

## Learning Objectives

- Understand what **User-Defined Functions (UDFs)** are in SQL Server.
- Differentiate between **scalar** and **table-valued** functions.
- Create, alter, and drop UDFs.
- Use UDFs in SQL queries to simplify logic and improve code reusability.

---

## 1️⃣ Introduction to Functions in SQL Server

Like in programming languages, **functions in SQL Server**:
- Accept **parameters**.
- Perform **actions or calculations**.
- Return a **value or result set**.

### 🧩 Benefits of Using UDFs
- ✅ Code reusability  
- ✅ Simplifies complex SQL queries  
- ✅ Improves query performance  
- ✅ Centralizes business logic  

---

## 2️⃣ Types of Functions in SQL Server

| Type | Description | Example |
|------|--------------|----------|
| **System Functions** | Built-in functions provided by SQL Server | `LEN()`, `GETDATE()`, `ROUND()` |
| **User-Defined Functions (UDFs)** | Created by users to perform custom logic | `dbo.GetInventoryStock()` |

---

## 3️⃣ Categories of User-Defined Functions (UDFs)

| Type | Returns | Example Use Case |
|------|----------|------------------|
| **Scalar Function** | A single value (e.g., string, number) | Compute a discount or tax |
| **Table-Valued Function** | A table (set of rows) | Return all products above a price threshold |

---

## 4️⃣ Scalar Functions

A **Scalar Function** returns **one value** and can be used inside queries, views, or other functions.

### 🧩 Syntax
```sql
CREATE [OR ALTER] FUNCTION function_name 
( [ @parameter_name data_type [, ...n] ] )
RETURNS data_type
AS
BEGIN
   -- statements
   RETURN value;
END;
```

### 🟢 Example 1: Compare Two Numbers
```sql
CREATE FUNCTION dbo.is_greater (@a INT, @b INT)
RETURNS VARCHAR(30)
AS
BEGIN
   RETURN (
      'A is ' +
      CASE
         WHEN @a > @b THEN 'greater than '
         WHEN @a < @b THEN 'smaller than '
         ELSE 'equal to '
      END + 'B'
   );
END;
```

**Test:**
```sql
SELECT dbo.is_greater(10, 7) AS Result;
```

**Output:**
```
A is greater than B
```

---

### 🟢 Example 2: Get Product Inventory Stock
```sql
CREATE FUNCTION dbo.GetInventoryStock (@ProductID VARCHAR(10))
RETURNS INT
AS
BEGIN
   DECLARE @ret INT;
   SELECT @ret = P_QOH FROM Product WHERE P_CODE = @ProductID;

   IF @ret IS NULL SET @ret = 0;
   RETURN @ret;
END;
```

**Usage:**
```sql
SELECT P_CODE, dbo.GetInventoryStock(P_CODE) AS CurrentStock
FROM Product
WHERE MONTH(P_INDATE) BETWEEN 9 AND 11;
```

---

### 🟢 Example 3: Net Price Calculation
```sql
CREATE FUNCTION dbo.udfNetPrice (@price NUMERIC(10,2), @discount NUMERIC(4,2))
RETURNS NUMERIC(10,2)
AS
BEGIN
   RETURN @price * (1 - @discount);
END;
```

**Usage:**
```sql
SELECT P_CODE, dbo.udfNetPrice(P_PRICE, P_DISCOUNT) AS NetPrice
FROM Product;
```

---

## 5️⃣ Scalar Function Rules

| Rule | Description |
|------|--------------|
| Must include a `RETURN` statement | Required |
| Accepts one or more parameters | Optional |
| Cannot modify database data | Read-only |
| Can contain IF, CASE, or loops | Allowed |
| Can call other functions | Yes |

---

## 6️⃣ Table-Valued Functions (TVFs)

A **Table-Valued Function** returns a table result and can be used like a view or subquery.

### 🧩 Syntax
```sql
CREATE [OR ALTER] FUNCTION function_name ( [@param data_type] )
RETURNS TABLE
AS
RETURN (
   SELECT columns
   FROM table_name
   WHERE condition
);
```

---

### 🟢 Example 1: Products Above a Given Price
```sql
CREATE FUNCTION dbo.ListWithPrice (@ProductPrice NUMERIC(8,2))
RETURNS TABLE
AS
RETURN
(
   SELECT *
   FROM Product
   WHERE P_PRICE > @ProductPrice
);
```

**Usage:**
```sql
SELECT * FROM dbo.ListWithPrice(50);
```

---

### 🟢 Example 2: Customers with Invoices
```sql
CREATE FUNCTION dbo.GetCustomersWithInvoices()
RETURNS TABLE
AS
RETURN
(
   SELECT DISTINCT C.CUS_FNAME, C.CUS_LNAME
   FROM CUSTOMER C
   JOIN INVOICE I ON C.CUS_CODE = I.CUS_CODE
);
```

**Usage:**
```sql
SELECT * FROM dbo.GetCustomersWithInvoices();
```

---

## 7️⃣ More Practice Examples

### 🟡 Example 1: Count Invoices by Customer
```sql
CREATE FUNCTION dbo.GetInvoiceCountByCustomer (@cust_number INT)
RETURNS INT
AS
BEGIN
   DECLARE @InvoiceCount INT;
   SELECT @InvoiceCount = COUNT(*) FROM INVOICE WHERE CUS_CODE = @cust_number;
   RETURN @InvoiceCount;
END;
```

**Test:**
```sql
SELECT dbo.GetInvoiceCountByCustomer(10012) AS TotalInvoices;
```

---

### 🟡 Example 2: Number of Products per Vendor
```sql
CREATE FUNCTION dbo.NumberOfProducts (@V_CODE INT)
RETURNS INT
AS
BEGIN
   DECLARE @VPCOUNT INT;
   SELECT @VPCOUNT = COUNT(P_CODE) FROM PRODUCT WHERE V_CODE = @V_CODE;
   RETURN @VPCOUNT;
END;
```

**Usage:**
```sql
SELECT V_CODE, dbo.NumberOfProducts(V_CODE) AS ProductCount
FROM PRODUCT
GROUP BY V_CODE;
```

---

### 🟡 Example 3: Check Product Stock Level
```sql
CREATE FUNCTION dbo.UDF_CheckStockLevel (@P_CODE VARCHAR(20))
RETURNS VARCHAR(15)
AS
BEGIN
   DECLARE @QOH INT, @MIN INT, @STATUS VARCHAR(15);
   SELECT @QOH = P_QOH, @MIN = P_MIN FROM PRODUCT WHERE P_CODE = @P_CODE;

   IF @QOH > @MIN
      SET @STATUS = 'In Stock';
   ELSE
      SET @STATUS = 'Out of Stock';

   RETURN @STATUS;
END;
```

**Test:**
```sql
SELECT P_CODE, dbo.UDF_CheckStockLevel(P_CODE) AS StockStatus
FROM Product;
```

---

### 🟡 Example 4: Invoice Total with Null Handling
```sql
CREATE FUNCTION dbo.UDF_InvoiceTotal (@subtotal NUMERIC(8,2), @tax NUMERIC(8,2))
RETURNS NUMERIC(8,2)
AS
BEGIN
   RETURN (ISNULL(@subtotal,0) *(1+ ISNULL(@tax,0)));
END;
```

**Usage:**
```sql
SELECT dbo.UDF_InvoiceTotal(100, 0.15) AS TotalAmount;
SELECT dbo.UDF_InvoiceTotal(NULL, 0.15);
```

---

## 8️⃣ Altering and Dropping Functions

### 🔧 Modify an Existing Function
```sql
ALTER FUNCTION dbo.udfNetPrice (@price NUMERIC(10,2), @discount NUMERIC(4,2))
RETURNS NUMERIC(10,2)
AS
BEGIN
   RETURN ROUND(@price * (1 - @discount), 2);
END;
```

### ❌ Drop a Function
```sql
DROP FUNCTION dbo.udfNetPrice;
DROP FUNCTION dbo.ListWithPrice;
```

---

## 9️⃣ Key Takeaways

| Concept | Description |
|----------|-------------|
| UDF | Custom reusable SQL logic created by users |
| Scalar Function | Returns a single value |
| Table-Valued Function | Returns a table result |
| Benefits | Code reusability, clarity, consistency |
| Common Uses | Discounts, stock checks, calculations |

---

## 🔟 Practice Exercises

1. Create a scalar function `dbo.GetFullName(@FirstName, @LastName)` that returns a concatenated name.
2. Create a function `dbo.CheckDiscount(@ProductCode)` that returns `"Discounted"` if the discount > 0, otherwise `"Regular Price"`.
3. Define a function that calculates `TotalPrice = Price * Quantity` and returns it as numeric.
4. Create a table-valued function that returns all customers with total purchases > 5000.
5. Alter the `dbo.udfNetPrice` function to round results to two decimals.
6. Drop both scalar and table-valued functions you created.

---

✅ **End of User-Defined Functions in SQL Server (UDFs)**


---

