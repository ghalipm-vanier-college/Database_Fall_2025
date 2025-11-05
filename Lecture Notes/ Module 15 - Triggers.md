
# Module 15 - Triggers  

---

## Learning Objectives

- Understand what **triggers** are and how they work in SQL Server.
- Differentiate between **AFTER**, **FOR**, and **INSTEAD OF** triggers.
- Use **INSERTED** and **DELETED** virtual tables in triggers.
- Create, modify, and drop triggers.
- Recognize the pros, cons, and use cases of triggers.

---

## 1️⃣ What is a Trigger?

A **trigger** is a named T-SQL block that executes **automatically** when a specified **event** occurs in the database.

Triggers can be fired:
- After **DML operations** (`INSERT`, `UPDATE`, `DELETE`)
- On **DDL operations** (`CREATE`, `ALTER`, `DROP`)
- On **Database events** (login, logout, errors, startup/shutdown)

> 💡 Triggers enforce business rules, automate actions, or maintain data integrity.

---

## 2️⃣ Basic Structure of a Trigger

A trigger includes:
| Part | Description |
|------|--------------|
| **Timing** | Defines when the trigger runs (`FOR`/`AFTER`, or `INSTEAD OF`) |
| **Event** | Specifies what action fires the trigger (`INSERT`, `UPDATE`, `DELETE`) |
| **Action** | SQL statements executed inside the trigger |

### Syntax
```sql
CREATE [OR ALTER] TRIGGER trigger_name
ON table_name
{FOR | AFTER | INSTEAD OF} [INSERT, UPDATE, DELETE]
AS
BEGIN
    -- Trigger logic here
END;
````

---

## 3️⃣ Trigger Types

| Type               | Description                                                        | Typical Use                           |
| ------------------ | ------------------------------------------------------------------ | ------------------------------------- |
| **AFTER (or FOR)** | Executes **after** the triggering statement completes successfully | Most common for enforcing rules       |
| **INSTEAD OF**     | Executes **instead of** the triggering statement                   | Useful for views or custom validation |

---

## 4️⃣ Why Use Triggers?

✅ Enforce complex business rules not supported by constraints
✅ Prevent invalid transactions
✅ Maintain audit logs or history tables
✅ Automatically update derived or dependent data

---

## 5️⃣ Example 1 – Simple Insert Trigger

```sql
CREATE TRIGGER trg_CustomerInsert
ON Customer
AFTER INSERT
AS
BEGIN
    PRINT 'A new customer record has been added.';
END;
```

**Test:**

```sql
INSERT INTO Customer VALUES (1005, 'Evan', 'Taylor', 0);
```

---

## 6️⃣ Example 2 – Capitalize Vendor State

Automatically convert the vendor’s state to uppercase after any `INSERT` or `UPDATE`.

```sql
CREATE TRIGGER trg_CapitalizeState
ON Vendor
AFTER INSERT, UPDATE
AS
BEGIN
    UPDATE v
    SET v.V_State = UPPER(v.V_State)
    FROM Vendor v
    INNER JOIN inserted i ON v.V_CODE = i.V_CODE;
END;
```

---

## 7️⃣ Virtual Tables in Triggers

SQL Server provides two **temporary (virtual)** tables inside triggers:

| Table        | Contains                                  |
| ------------ | ----------------------------------------- |
| **INSERTED** | New data values (after INSERT or UPDATE)  |
| **DELETED**  | Old data values (before DELETE or UPDATE) |

> 💡 Use these to reference affected rows inside your trigger logic.

---

## 8️⃣ Example 3 – Update Customer Balance After Insert

```sql
CREATE TRIGGER trg_UpdateCustomerBalance
ON InvoiceLine
AFTER INSERT
AS
BEGIN
    DECLARE @CustCode INT;

    SELECT @CustCode = CUS_CODE
    FROM Invoice
    WHERE INV_NUMBER = (SELECT INV_NUMBER FROM inserted);

    UPDATE Customer
    SET CUS_BALANCE = CUS_BALANCE + (SELECT LINE_TOTAL FROM inserted)
    WHERE CUS_CODE = @CustCode;
END;
```

---

## 9️⃣ Example 4 – Update Product Quantity on Sale

Reduce product stock when a sale (new line item) is added.

```sql
CREATE TRIGGER trg_UpdateProductStock
ON InvoiceLine
AFTER INSERT
AS
BEGIN
    UPDATE Product
    SET P_QOH = P_QOH - i.LINE_UNITS
    FROM Product p
    INNER JOIN inserted i ON p.P_CODE = i.P_CODE;
END;
```

---

## 🔟 Example 5 – Product Reorder Trigger

Set `P_REORDER = 1` if quantity falls below minimum.

```sql
CREATE OR ALTER TRIGGER trg_ProductReorder
ON Product
AFTER INSERT, UPDATE
AS
BEGIN
    UPDATE Product
    SET P_REORDER =
        CASE WHEN P_QOH <= P_MIN THEN 1 ELSE 0 END
    FROM Product p
    INNER JOIN inserted i ON p.P_CODE = i.P_CODE;
END;
```

**Test:**

```sql
UPDATE Product SET P_QOH = 4 WHERE P_CODE = 'P100';
SELECT P_CODE, P_QOH, P_MIN, P_REORDER FROM Product;
```

---

## 1️⃣1️⃣ Managing Triggers

| Action            | Command                                       |
| ----------------- | --------------------------------------------- |
| Drop a trigger    | `DROP TRIGGER trigger_name;`                  |
| Disable a trigger | `DISABLE TRIGGER trigger_name ON table_name;` |
| Enable a trigger  | `ENABLE TRIGGER trigger_name ON table_name;`  |
| View all triggers | `SELECT * FROM sys.triggers;`                 |

---

## 1️⃣2️⃣ Advantages & Disadvantages

### ✅ Advantages

* Automates repetitive tasks
* Enforces complex integrity rules
* Improves data consistency

### ⚠️ Disadvantages

* Performance overhead (especially nested triggers)
* Harder to debug and maintain
* May cause **cascading updates** unintentionally

---

## 1️⃣3️⃣ Triggers vs. Stored Procedures

| Feature               | Trigger                         | Stored Procedure                    |
| --------------------- | ------------------------------- | ----------------------------------- |
| **Execution**         | Auto-executed when event occurs | Manually executed with `EXEC`       |
| **Input Parameters**  | Not allowed                     | Allowed                             |
| **Return Value**      | Cannot return                   | Can return                          |
| **Execution Control** | Fires automatically             | Developer controlled                |
| **Nested Calls**      | Cannot call directly            | Can be nested or called recursively |

---

## 1️⃣4️⃣ Key Takeaways

* **Triggers** are powerful tools for enforcing rules and automating data changes.
* Always test triggers thoroughly to avoid **cascading or hidden side effects**.
* Prefer **set-based logic** over cursors inside triggers.
* Use them **judiciously** — too many triggers can make debugging difficult.

---

##  Practice Exercises

🔹 Use AFTER for `post-action` auditing and cascading updates of `tables`only.
🔹 Use INSTEAD OF when you need to intercept, validate, or customize a change `before` it happens (especially for views).

---

✅ **End of Module 15 – Triggers in SQL Server**

---

```

---
