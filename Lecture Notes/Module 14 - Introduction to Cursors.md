#  Module 14 – Introduction to Cursors  
### SQL Server – T-SQL Programming

---

##  Learning Objectives

- Understand what a **cursor** is and why it’s used.
- Declare, open, fetch, close, and deallocate cursors.
- Use **`@@FETCH_STATUS`** to control cursor loops.
- Perform row-by-row operations using cursors.
- Recognize the **advantages**, **disadvantages**, and **best practices** of cursor usage.

---

## 1️⃣ What is a Cursor?

SQL normally operates on **sets of data** (set-based processing).  
For example, a `SELECT` query retrieves a **result set** — multiple rows at once.

However, sometimes you need to process **one row at a time** —  
for instance, applying different logic per row or invoking a conditional update.

➡️ In such cases, **cursors** are used.

### Definition
A **cursor** is a T-SQL database object used to:
- Retrieve data **row by row** from a result set.
- Perform **row-level calculations or updates**.

---

## 2️⃣ Why Use Cursors?

| Scenario | Example |
|-----------|----------|
| **Row-by-row logic required** | Adjusting balances or applying different discounts per customer |
| **Sequential operations** | Processing transactions that depend on previous results |
| **Complex conditional updates** | Updating records based on computed or lookup values |

---

## 3️⃣ Cursor Lifecycle in SQL Server

| Step | Command | Description |
|------|----------|-------------|
| 1️⃣ Declare | `DECLARE cursor_name CURSOR FOR SELECT ...` | Define the cursor and its query |
| 2️⃣ Open | `OPEN cursor_name;` | Execute the query and populate the cursor |
| 3️⃣ Fetch | `FETCH NEXT FROM cursor_name INTO variables;` | Retrieve a single row into variables |
| 4️⃣ Process | `WHILE @@FETCH_STATUS = 0 ...` | Loop through rows until all are processed |
| 5️⃣ Close | `CLOSE cursor_name;` | Release the result set but keep definition |
| 6️⃣ Deallocate | `DEALLOCATE cursor_name;` | Remove cursor definition and free memory |

---

## 4️⃣ Example – Basic Cursor Syntax

```sql
DECLARE @vendor_code INT, @vendor_name VARCHAR(50);

DECLARE vendor_cursor CURSOR FOR
SELECT V_CODE, V_NAME FROM Vendor;

OPEN vendor_cursor;

FETCH NEXT FROM vendor_cursor INTO @vendor_code, @vendor_name;

WHILE @@FETCH_STATUS = 0
BEGIN
    PRINT 'Vendor Code: ' + CAST(@vendor_code AS VARCHAR(10)) + 
          ', Vendor Name: ' + @vendor_name;
    FETCH NEXT FROM vendor_cursor INTO @vendor_code, @vendor_name;
END;

CLOSE vendor_cursor;
DEALLOCATE vendor_cursor;
```
### Set-Based Equivalent (No Cursor Needed)

* Option 1 – Using a CASE Expression (Preferred)
  
This returns the same logical output in a single query:
```sql
SELECT 
    CUS_FNAME + ' ' + CUS_LNAME AS CustomerName,
    CASE 
        WHEN CUS_BALANCE < 200 THEN 'Bonus Eligible'
        ELSE 'Review Account'
    END AS Status
FROM Customer;
```
---

## 5️⃣ Understanding `@@FETCH_STATUS`

| Return Value | Meaning |
|---------------|----------|
| **0** | Fetch successful |
| **-1** | Beyond last row or result set empty |
| **-2** | The row no longer exists or was deleted |

Used in `WHILE` loops to check if more rows remain.

---

## 6️⃣ Example – Update Customer Balances

**Task:** Reduce each customer’s balance by 10% if their balance exceeds \$500.

```sql
DECLARE @CUS_CODE INT, @CUS_BALANCE DECIMAL(10,2);

DECLARE CUSTOMER_CURSOR CURSOR FOR
SELECT CUS_CODE, CUS_BALANCE FROM Customer WHERE CUS_BALANCE > 500;

OPEN CUSTOMER_CURSOR;
FETCH NEXT FROM CUSTOMER_CURSOR INTO @CUS_CODE, @CUS_BALANCE;

WHILE @@FETCH_STATUS = 0
BEGIN
   UPDATE Customer
   SET CUS_BALANCE = @CUS_BALANCE * 0.9
   WHERE CUS_CODE = @CUS_CODE;

   FETCH NEXT FROM CUSTOMER_CURSOR INTO @CUS_CODE, @CUS_BALANCE;
END;

CLOSE CUSTOMER_CURSOR;
DEALLOCATE CUSTOMER_CURSOR;
```

---

## 7️⃣ Example – Apply Discount by Condition

**Task:**  
If an invoice subtotal > 500 → 15% discount; otherwise 5%.  
Update total after applying discount.

```sql
DECLARE @INV_NUM INT, @SUB DECIMAL(10,2), @DISCOUNT_RATE DECIMAL(4,2);

DECLARE INVOICE_CURSOR CURSOR FOR
SELECT INV_NUMBER, INV_SUBTOTAL FROM Invoice;

OPEN INVOICE_CURSOR;
FETCH NEXT FROM INVOICE_CURSOR INTO @INV_NUM, @SUB;

WHILE @@FETCH_STATUS = 0
BEGIN
   IF @SUB > 500 
       SET @DISCOUNT_RATE = 0.15;
   ELSE 
       SET @DISCOUNT_RATE = 0.05;

   UPDATE Invoice
   SET INV_TOTAL = @SUB * (1 - @DISCOUNT_RATE)
   WHERE INV_NUMBER = @INV_NUM;

   PRINT 'Invoice: ' + CAST(@INV_NUM AS VARCHAR(10)) +
         ' | Discount: ' + CAST(@DISCOUNT_RATE*100 AS VARCHAR(5)) + '%';

   FETCH NEXT FROM INVOICE_CURSOR INTO @INV_NUM, @SUB;
END;

CLOSE INVOICE_CURSOR;
DEALLOCATE INVOICE_CURSOR;
```

---

## 8️⃣ Example – Vendor Inventory Summary

**Task:** Print vendors whose total product quantity exceeds 100.

```sql
DECLARE @V_CODE INT, @TOTAL_QTY INT;

DECLARE VENDOR_CURSOR CURSOR FOR
SELECT V_CODE, SUM(P_QOH)
FROM Product
GROUP BY V_CODE
HAVING SUM(P_QOH) > 100;

OPEN VENDOR_CURSOR;
FETCH NEXT FROM VENDOR_CURSOR INTO @V_CODE, @TOTAL_QTY;

WHILE @@FETCH_STATUS = 0
BEGIN
   PRINT 'Vendor ' + CAST(@V_CODE AS VARCHAR(10)) + 
         ' has total stock: ' + CAST(@TOTAL_QTY AS VARCHAR(10));
   FETCH NEXT FROM VENDOR_CURSOR INTO @V_CODE, @TOTAL_QTY;
END;

CLOSE VENDOR_CURSOR;
DEALLOCATE VENDOR_CURSOR;
```

---

## 9️⃣ Example – Handle NULL Vendors

**Task:**  
Find all products with missing vendor codes and assign a default vendor (99999).

```sql
DECLARE @P_CODE VARCHAR(15);

DECLARE NULL_VENDOR_CURSOR CURSOR FOR
SELECT P_CODE FROM Product WHERE V_CODE IS NULL;

OPEN NULL_VENDOR_CURSOR;
FETCH NEXT FROM NULL_VENDOR_CURSOR INTO @P_CODE;

WHILE @@FETCH_STATUS = 0
BEGIN
   UPDATE Product
   SET V_CODE = 99999
   WHERE P_CODE = @P_CODE;

   PRINT 'Product ' + @P_CODE + ' assigned to default vendor 99999.';
   FETCH NEXT FROM NULL_VENDOR_CURSOR INTO @P_CODE;
END;

CLOSE NULL_VENDOR_CURSOR;
DEALLOCATE NULL_VENDOR_CURSOR;
```

---

## 🔟 Example – Employee Bonus Check

**Task:**  
Check how long each employee has worked, and if ≤ 10 years, print a bonus suggestion.

```sql
DECLARE @EmployeeName NVARCHAR(100);
DECLARE @EnterDate DATE;
DECLARE @YearsWorked INT;

DECLARE EmployeeCursor CURSOR FOR
SELECT E.EmployeeName, W.Enter_Date
FROM Works_On W
JOIN Employees E ON W.EmployeeID = E.EmployeeID;

OPEN EmployeeCursor;
FETCH NEXT FROM EmployeeCursor INTO @EmployeeName, @EnterDate;

WHILE @@FETCH_STATUS = 0
BEGIN
   SET @YearsWorked = DATEDIFF(YEAR, @EnterDate, GETDATE());

   IF @YearsWorked <= 10
      PRINT 'Consider a Bonus for: ' + @EmployeeName;

   FETCH NEXT FROM EmployeeCursor INTO @EmployeeName, @EnterDate;
END;

CLOSE EmployeeCursor;
DEALLOCATE EmployeeCursor;
```

---

## Summary

### ✅ Advantages of Cursors
- Handle **row-level logic** when set-based operations can’t.
- Useful for **conditional**, **iterative**, or **dependent updates**.
- Allow integration with procedural T-SQL logic (loops, conditions, printing).

### ⚠️ Disadvantages
- **Slow and resource-intensive** for large datasets.
- **Complex to debug** compared to set-based SQL.
- Should be used **only when necessary**.

### 💡 Best Practices
- Prefer **set-based SQL** (e.g., `UPDATE`, `JOIN`, `CASE`) when possible.
- Limit rows fetched by cursors.
- Always:
  - `CLOSE` and `DEALLOCATE` cursors.
  - Use `SET NOCOUNT ON;` to suppress extra “(n rows affected)” messages.

---

✅ **End of Lecture 14 – Introduction to Cursors**
