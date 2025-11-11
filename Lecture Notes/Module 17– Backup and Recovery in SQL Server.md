
# Lecture 18 – Backup and Recovery in SQL Server

---

##  Learning Objectives


- Understand the purpose of **transaction logs** and **database recovery models**.
- Explain how backups and restores work in SQL Server.
- Identify types of backups: **Full**, **Differential**, and **Transaction Log**.
- Perform a backup and restore operation in SQL Server Management Studio (SSMS).

---

## 1️⃣ SQL Server Database Files

| File Type | Extension | Purpose |
|------------|------------|----------|
| **Primary Data File** | `.MDF` | Stores all data objects (tables, views, stored procedures, etc.) |
| **Transaction Log File** | `.LDF` | Records all modifications made to the database |

---

## 2️⃣ What is a Transaction Log?

A **transaction log** records every change made to the database.

### It contains:
- Transaction ID  
- Date and Time of each update  
- Before and After image of data  
- Commit or rollback information  

### Purpose:
- Ensures data consistency  
- Supports **ROLLBACK** (undo) and **REDO** (reapply) operations  
- Enables **point-in-time recovery**

> 💡 The transaction log is written **sequentially** and used by SQL Server during recovery after crashes or rollbacks.

---

## 3️⃣ Why SQL Server Requires a Transaction Log

SQL Server uses the **transaction log** for:
- Rolling back incomplete transactions  
- Restoring committed transactions after system failure  
- Maintaining database integrity during crashes  

### Each entry records:
1. Transaction start and end  
2. Operations performed  
3. Old and new values  
4. Commit or rollback status  

---

## 4️⃣ Understanding the `fn_dblog()` Function

SQL Server has an internal function `fn_dblog()` to read the log file (for system use only).

```sql
SELECT [Current LSN], [Operation], [Transaction Name],
       [Transaction ID], [SPID], [Begin Time]
FROM fn_dblog(NULL, NULL);
````

> ⚠️ Use this only for troubleshooting; it’s not officially documented.

---

## 5️⃣ Database Recovery Models

A **Recovery Model** determines how SQL Server logs transactions and handles backups.

| Recovery Model  | Description                                   | Point-in-Time Recovery | Typical Use         |
| --------------- | --------------------------------------------- | ---------------------- | ------------------- |
| **Full**        | Logs all transactions until log backup occurs | ✅ Yes                  | Production systems  |
| **Simple**      | Logs cleared after each checkpoint            | ❌ No                   | Development systems |
| **Bulk-Logged** | Minimal logging for bulk operations           | ⚠️ Limited             | Large data imports  |

---

### 🔹 Full Recovery Model

* Keeps all transactions until a **log backup** occurs.
* Allows **point-in-time** recovery.
* Best for **critical data**.

### 🔹 Simple Recovery Model

* Reuses log space after transactions complete.
* No point-in-time recovery.
* Ideal for non-critical or test databases.

### 🔹 Bulk-Logged Recovery Model

* Reduces log size during **bulk operations** (e.g., data imports).
* Combines benefits of **Full** and **Simple** models.

---

## 6️⃣ Backup Strategies

### Why Backups are Essential

* Protects against data loss from corruption, hardware failure, or user error.
* Should be **automated and scheduled** (e.g., nightly or weekly).
* Backups must be **tested regularly** for integrity.

### General Strategy

| Type                         | Frequency | Description                         |
| ---------------------------- | --------- | ----------------------------------- |
| **Full Backup**              | Weekly    | Entire database                     |
| **Incremental/Differential** | Daily     | Only changes since last full backup |

---

## 7️⃣ Types of SQL Server Backups

| Backup Type         | Description                       | Use Case                      |
| ------------------- | --------------------------------- | ----------------------------- |
| **Full**            | Entire database                   | Baseline backup               |
| **Differential**    | Changes since last full backup    | Faster daily backups          |
| **Transaction Log** | Log records since last log backup | Enables point-in-time restore |

---

###  Full Backup Example

```sql
BACKUP DATABASE CollegeDB
TO DISK = 'C:\Backups\CollegeDB_Full.bak'
WITH FORMAT, NAME = 'Full Backup of CollegeDB';
```

###  Differential Backup Example

```sql
BACKUP DATABASE CollegeDB
TO DISK = 'C:\Backups\CollegeDB_Diff.bak'
WITH DIFFERENTIAL, NAME = 'Differential Backup';
```

###  Transaction Log Backup Example

```sql
BACKUP LOG CollegeDB
TO DISK = 'C:\Backups\CollegeDB_Log.trn'
WITH NAME = 'Transaction Log Backup';
```

---

## 8️⃣ Performing a Full Backup (SSMS)

1. **Right-click** the database → *Tasks* → *Back Up...*
2. Select **Backup Type: Full**
3. Choose **Destination: Disk**
4. Click **Add**, then specify backup file path
5. Under **Options**, select “Verify backup when finished”
6. Click **OK** to start backup

---

## 9️⃣ Restoring a Full Backup

1. Right-click the database → *Tasks* → *Restore → Database...*
2. Select the backup file (`.bak`)
3. Choose **Destination Database**
4. (Optional) Use **Timeline** to pick a point in time
5. Under **Options**, check “Overwrite existing database” if needed
6. Click **OK** to restore

---

## 🔟 Recovery Timeline and Point-in-Time Restore

In SSMS:

* Use the **Timeline** tool to choose a **specific restore point** before an error occurred.
* Ensure **Transaction Log Backups** are available for precise recovery.

---

## 1️⃣1️⃣ Summary

| Concept                 | Description                                          |
| ----------------------- | ---------------------------------------------------- |
| **Transaction Log**     | Records every database modification                  |
| **Full Backup**         | Complete copy of the database                        |
| **Differential Backup** | Changes since last full backup                       |
| **Log Backup**          | Records since last log backup                        |
| **Recovery Models**     | Define how SQL Server logs and restores transactions |

>  **Best Practice:**
> Combine **Full**, **Differential**, and **Log** backups for maximum protection and flexibility.

---

##  Practice Exercises

1. Perform a **Full Backup** of your database.
2. Create a **Differential Backup** after modifying some data.
3. Execute a **Transaction Log Backup**.
4. Restore a database to the **latest backup**.
5. Practice **Point-in-Time Recovery** using the **Timeline** tool.
6. Identify the current **Recovery Model** of your database.
7. Change it from **Simple** to **Full**, then perform a log backup.

---

✅ **End of Lecture 17 – Backup and Recovery in SQL Server**

```

---


