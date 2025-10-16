
<!-- **Database Objects:** VIEWS, INDEX, SEQUENCE -->
<!--  **CREATE, ALTER, DROP VIEW** (Restrictions, WITH CHECK OPTION) -->
<!--  **CREATE, ALTER, DROP INDEX** -->
<!--  **CREATE, ALTER, DROP SEQUENCE** -->

#  Lecture: Database Objects – VIEWS, INDEX, and SEQUENCE

---

##  1. Database Objects Overview

A **Database Object** is any defined structure that stores or references data in a database.  
Examples include:
- **Tables** – store data.
- **Views** – virtual tables based on queries.
- **Indexes** – structures that speed up data retrieval.
- **Sequences** – generate numeric values automatically.
- **Synonyms, Triggers, Procedures** – other objects for advanced functionality.

---

## ️ 2. VIEWS

###  Definition
A **VIEW** is a **virtual table** that presents data from one or more base tables.  
It does **not store data physically**; instead, it stores a **query definition**.

### ✅ Benefits
- Simplifies complex queries.
- Provides **data security** (hide sensitive columns).
- Presents customized data to different users.
- Allows logical data independence.

---

### 🔹 CREATE VIEW
**Syntax:**
```sql
CREATE [OR REPLACE] VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
````

**Example:**

```sql
CREATE VIEW v_customer_orders AS
SELECT c.CustomerID, c.FirstName, c.LastName, o.OrderDate, o.TotalAmount
FROM Customers c
JOIN Orders o ON c.CustomerID = o.CustomerID
WHERE o.TotalAmount > 500;
```

✔ Creates a view showing only high-value orders.

---

### 🔹 WITH CHECK OPTION

Prevents modifications through the view that violate its condition.

```sql
CREATE VIEW v_toronto_customers AS
SELECT * FROM Customers
WHERE City = 'Toronto'
WITH CHECK OPTION;
```

✔ Ensures that only customers from **Toronto** can be inserted or updated through this view.

---

### 🔹 ALTER VIEW

Used to modify the view definition.

```sql
ALTER VIEW v_customer_orders AS
SELECT c.CustomerID, c.FirstName, o.TotalAmount
FROM Customers c
JOIN Orders o ON c.CustomerID = o.CustomerID
WHERE o.TotalAmount > 300;
```

---

### 🔹 DROP VIEW

Removes a view from the database.

```sql
DROP VIEW v_customer_orders;
```

---

### ⚠️ Restrictions on Views

* You **cannot** create indexes directly on views.
* DML operations (INSERT, UPDATE, DELETE) are allowed **only** if the view:

  * Refers to **a single base table**.
  * Contains **no GROUP BY, DISTINCT, or aggregate functions**.
  * Includes **all NOT NULL columns** from the base table.

---

## ⚡ 3. INDEXES

### 🔹 Definition

An **INDEX** is a database object that improves the **speed of data retrieval** by allowing quick lookups of rows in a table.

Think of it like a **book index** — it points to where specific data can be found.

---

### 🔹 Why Use Indexes

* Speed up search queries and joins.
* Improve performance of sorting and filtering.
* Reduce the need for full table scans.

---

### 🔹 Types of Indexes

| Type                    | Description                                                         |
| ----------------------- | ------------------------------------------------------------------- |
| **Primary Index**       | Automatically created for primary keys.                             |
| **Unique Index**        | Ensures all values in a column are unique.                          |
| **Clustered Index**     | Sorts and stores data physically in the table (only one per table). |
| **Non-Clustered Index** | Stores key values separately with pointers to actual data.          |
| **Composite Index**     | Includes multiple columns for multi-column queries.                 |

---

### 🔹 CREATE INDEX

**Syntax:**

```sql
CREATE [UNIQUE] INDEX index_name
ON table_name (column1 [, column2]...);
```

**Example:**

```sql
CREATE INDEX idx_customer_city ON Customers (City);
CREATE UNIQUE INDEX idx_product_sku ON Products (SKU);
CREATE INDEX idx_order_date_amount ON Orders (OrderDate, TotalAmount);
```
* After creating an index like "CREATE INDEX idx_customer_city ON Customers (City);",
* you don't "use" it in your SELECT statements in the same way you might reference a table or column.
* Instead, the SQL Server query optimizer automatically considers and potentially utilizes the index when executing queries that can benefit from it.
* How the index is used:
* When you execute a query that filters or sorts by the City column, the SQL Server query optimizer will evaluate whether using idx_customer_city would be more efficient than performing a full table scan.
* If it determines the index will improve performance, it will automatically incorporate it into the execution plan.
---

* To verify if an index 'idx_customer_city' is created, use:
```sql
EXEC sp_helpindex 'Customers';
```
you should see  'idx_customer_city' under 'index_name' column . 

### 🔹 ALTER INDEX (Rebuild or Rename)

**Purpose:** Rebuilds or reorganizes the index when a table undergoes many updates or deletes.

**Syntax:**

```sql
ALTER INDEX index_name ON table_name REBUILD;
```

**Example:**

```sql
ALTER INDEX idx_customer_city ON Customers REBUILD;
```

✔ Improves performance by compacting index data.

---

### 🔹 DROP INDEX

Removes an index when it is no longer useful.

```sql
DROP INDEX idx_customer_city;
```

---

### ⚙️ Index Guidelines

* Create indexes on columns frequently used in `WHERE`, `JOIN`, or `ORDER BY`.
* Avoid indexes on:

  * Small tables.
  * Columns rarely used in queries.
  * Tables with frequent inserts or updates.
* Monitor index performance and **drop unused ones**.

---

### 💡 Example Applications

| Domain         | Use Case                       | Example Index                                                  |
| -------------- | ------------------------------ | -------------------------------------------------------------- |
| **E-Commerce** | Searching for a product        | `CREATE INDEX idx_product_name ON Products (ProductName);`     |
| **Banking**    | Filtering transactions by date | `CREATE INDEX idx_txn_date ON Transactions (TransactionDate);` |
| **Healthcare** | Locating patient records       | `CREATE INDEX idx_patient_id ON Patients (PatientID);`         |
| **Inventory**  | Checking low-stock items       | `CREATE INDEX idx_stock_qty ON Inventory (StockQuantity);`     |

---

## 🔢 4. SEQUENCES

### 🔹 Definition

A **SEQUENCE** is a database object that generates **a series of numeric values**, often used for **auto-incrementing primary keys**.

---

### 🔹 CREATE SEQUENCE

**Syntax:**

```sql
CREATE SEQUENCE sequence_name
START WITH start_value
INCREMENT BY increment_value
MINVALUE min_value
MAXVALUE max_value
CYCLE | NOCYCLE;
```

**Example:**

```sql
CREATE SEQUENCE seq_order_id
START WITH 1001
INCREMENT BY 1
NOCYCLE;
```

✔ Generates unique order IDs starting from 1001.

---

### 🔹 Using SEQUENCE Values

```sql
INSERT INTO Orders (OrderID, CustomerID, ProductID, Quantity, TotalAmount)
VALUES (seq_order_id.NEXTVAL, 501, 101, 2, 1900.00);
```

* `NEXTVAL` → fetches the next number.
* `CURRVAL` → returns the most recently generated number in the session.

---

### 🔹 ALTER SEQUENCE

Used to modify properties like increment or maximum value.

```sql
ALTER SEQUENCE seq_order_id
INCREMENT BY 5
MAXVALUE 20000;
```

---

### 🔹 DROP SEQUENCE

Deletes a sequence permanently.

```sql
DROP SEQUENCE seq_order_id;
```

---

## 🧩 5. Summary Table

| Object       | Purpose                               | Common Commands                                      |
| ------------ | ------------------------------------- | ---------------------------------------------------- |
| **VIEW**     | Simplifies queries, provides security | `CREATE VIEW`, `ALTER VIEW`, `DROP VIEW`             |
| **INDEX**    | Speeds up data retrieval              | `CREATE INDEX`, `ALTER INDEX`, `DROP INDEX`          |
| **SEQUENCE** | Generates unique numeric values       | `CREATE SEQUENCE`, `ALTER SEQUENCE`, `DROP SEQUENCE` |

---

## 💻 6. Practice Exercises

1. Create a view showing all orders above $500, including customer names.
2. Add a clustered index on the primary key column of the `Orders` table.
3. Create a sequence starting from 1000 for generating `CustomerID`.
4. Alter the sequence to increase by 10.
5. Drop the index created in step 2.

---

## 🧠 Key Takeaways

* **Views** present data logically, not physically.
* **Indexes** enhance performance but require careful design.
* **Sequences** automate the generation of unique numeric keys.
* Regularly **evaluate** and **maintain** indexes and sequences to ensure database efficiency.

---

✅ **End of Lecture**

```

