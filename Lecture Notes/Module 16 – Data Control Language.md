
---

````markdown
# Lecture 16 – Data Control Language (DCL)

---

##  Learning Objectives

- Understand what **Data Control Language (DCL)** is and why it’s used.
- Create and manage **users, logins, and roles** in SQL Server.
- Use **GRANT**, **REVOKE**, and **DENY** statements.
- Control database access and privileges securely.

---

## 1️⃣ What is DCL?

**Data Control Language (DCL)** manages **access and privileges** in a SQL Server database.

It allows database administrators to:
- Control who can perform operations like `SELECT`, `INSERT`, `UPDATE`, `DELETE`.
- Assign or remove permissions on specific database objects.
- Protect sensitive data from unauthorized users.

> 💡 DCL prevents other users from altering or deleting data they shouldn’t.

---

## 2️⃣ Core Security Concepts

| Concept | Description |
|----------|--------------|
| **User/Login** | Individual account for accessing SQL Server |
| **Rights/Privileges** | Permissions granted to perform certain operations |
| **Groups/Roles** | Collections of users sharing the same privileges |

### Example
- **User/Login:** `John`  
- **Group/Role:** `FinanceDept`  
- **Privilege:** `SELECT`, `UPDATE` on `Customer`

---

## 3️⃣ The System Administrator (SA)

- `sa` = **System Administrator** account.
- Has **all privileges** and can modify any object.
- Should be used **only when necessary**.

> ⚠️ Always log out after using `sa` to avoid critical data loss.

---

## 4️⃣ Authentication Modes

### 🔹 Windows Authentication
- Uses Windows credentials for access.
- Convenient, but **not secure for applications** managing sensitive data.

### 🔹 SQL Server Authentication
- Requires explicit **username** and **password**.
- Safer for production environments.

> ✅ A **login** connects to SQL Server.  
> ✅ A **user** accesses a specific database.

---

## 5️⃣ Creating Logins and Users

### Step 1: Create a Login
```sql
CREATE LOGIN MyLogin WITH PASSWORD = 'PasswordCollege2025';
````

### Step 2: Map Login to a Database User

```sql
CREATE USER MyUser FOR LOGIN MyLogin;
```

Check current context:

```sql
SELECT SUSER_NAME(), USER_NAME();
```

---

## 6️⃣ Managing Permissions

SQL Server permissions can be:

* **Granted** → Allows access.
* **Revoked** → Removes access.
* **Denied** → Explicitly blocks access (overrides GRANT).

---

## 7️⃣ DCL Commands

### 🔸 GRANT

Allows a user to perform actions on a database object.

**Syntax:**

```sql
GRANT privilege_list ON object TO user [WITH GRANT OPTION];
```

**Example 1:**
Give user *Manager* full privileges on table *Staff*:

```sql
GRANT ALL ON Staff TO Manager;
```

**Example 2:**
Allow *Personnel* and *Director* to modify only the *salary* column:

```sql
GRANT SELECT, UPDATE (salary)
ON Staff
TO Personnel, Director;
```

**Example 3:**
Grant read access to everyone:

```sql
GRANT SELECT ON Branch TO PUBLIC;
```

---

### 🔸 REVOKE

Removes previously granted permissions.

**Syntax:**

```sql
REVOKE privilege_list ON object FROM user;
```

**Example 1:**
Remove *SELECT* from everyone:

```sql
REVOKE SELECT ON Branch FROM PUBLIC;
```

**Example 2:**
Revoke all privileges from *Director*:

```sql
REVOKE ALL PRIVILEGES ON Staff FROM Director;
```

---

### 🔸 GRANT/REVOKE EXECUTE on Procedures

```sql
GRANT EXECUTE ON ProcName TO UserName;
REVOKE EXECUTE ON ProcName FROM UserName;
```

---

## 8️⃣ Using Roles

### What is a Role?

* A **role** groups multiple users together.
* Privileges are granted to the **role**, not individual users.

**Advantages:**

* Easier management.
* Reflects organizational hierarchy (e.g., Departments, Teams).

**Example:**

```sql
CREATE ROLE Managers;
GRANT SELECT, INSERT, UPDATE, DELETE ON Customer TO Managers;
ALTER ROLE Managers ADD MEMBER User_1;
```

---

## 9️⃣ Dropping a User

```sql
DROP USER user_name;
```

View all users:

```sql
SELECT * FROM sys.database_principals WHERE type = 'S';
```

---

## 🔟 Summary – DCL in SQL Server

| Command         | Purpose                         |
| --------------- | ------------------------------- |
| **GRANT**       | Give access/privileges          |
| **REVOKE**      | Remove granted privileges       |
| **DENY**        | Explicitly block permission     |
| **CREATE ROLE** | Define group permissions        |
| **ALTER ROLE**  | Add or remove users from a role |

> **Principle of Least Privilege**:
> Always give users only the permissions they need.

---

---

✅ **End of Lecture 16 – Data Control Language (DCL)**

```

---
