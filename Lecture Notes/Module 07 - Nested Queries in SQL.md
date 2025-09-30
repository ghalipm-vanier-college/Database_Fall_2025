# Nested Queries in SQL

---

## What is a Nested (Sub)Query?
A **subquery** is a SELECT statement embedded within another SQL query (the outer query). Its result is used by the outer query for filtering, comparison, or computation.

---

## Where Are Subqueries Used?
- **WHERE** clause for filtering rows  
- **FROM** clause as a derived table (inline view)  
- **HAVING** clause for group filtering  
- Also in **INSERT**, **UPDATE**, and **DELETE** statements

---

## Subquery Syntax
```sql
SELECT ...
FROM ...
WHERE column operator (SELECT column FROM table WHERE ...);
```


- The subquery must be in parentheses.
- Single-row operators: `=`, `>`, `<`, `>=`, `<=`, `!=`
- Multiple-row operators: `IN`, `ANY`, `ALL`, `EXISTS`

---

## Single-Row vs Multi-Row Subqueries

- **Single-row:** Returns only one value (row)  
```sql
SELECT name FROM Staff
WHERE salary > (SELECT salary FROM Staff WHERE name = 'John');
```

- **Multi-row:** Returns several values (rows)  
```sql
SELECT name FROM Staff
WHERE branch_id IN (SELECT id FROM Branch WHERE city = 'London');
```


---

## Example: Who earns more than John?
```sql
SELECT name
FROM Staff
WHERE salary > (SELECT salary FROM Staff WHERE name = 'John');
```

- Inner subquery finds John's salary.
- Outer query finds others with higher salaries.

---

## Using IN, ANY, ALL
- **IN**: Value must exist in a set  
```sql
... WHERE branch_id IN (SELECT id FROM Branch WHERE city = 'London');
```

- **ANY**: True if the condition is true for *any* row  
```sql
... WHERE salary > ANY (SELECT salary FROM Staff WHERE branch_id = 3);
```

- **ALL**: True only if the condition is true for *all* rows  
```sql
... WHERE salary > ALL (SELECT salary FROM Staff WHERE branch_id = 3);
```


---

## EXISTS and NOT EXISTS
- **EXISTS**: True if the subquery returns at least one row  
```sql
SELECT name FROM Staff s
WHERE EXISTS (
SELECT * FROM Branch b
WHERE s.branch_id = b.id AND city = 'London'
);
```

- **NOT EXISTS**: True if subquery returns no rows

---

## Correlated vs Non-Correlated Subqueries
- **Non-correlated:** Independent; runs once  
- **Correlated:** Depends on outer row, runs for each outer row

**Example Correlated:**
```sql
SELECT s.name
FROM Staff s
WHERE salary > (
SELECT AVG(s2.salary)
FROM Staff s2
WHERE s2.branch_id = s.branch_id
);
```

*Finds staff earning above branch average.*

---

## Subquery Guidelines
- Enclose subqueries in parentheses.
- Subqueries can't use ORDER BY (except outermost SELECT).
- Place subqueries on the right side of the comparison.
- Most subqueries return a single column.

---

## Good Practices
- Prefer joins for multi-table result sets unless subqueries add clarity.
- Use correlated subqueries for row-by-row filtering or calculation.
- Use EXISTS/NOT EXISTS for presence checks where possible.

---

## Summary Table

| Type         | Operators     | Returns     | Example Use Case                  |
|:-------------|:-------------|:------------|:----------------------------------|
| Single-row   | =, >, <      | 1 value     | salary > (SELECT ...)             |
| Multi-row    | IN, ANY, ALL | many values | branch_id IN (SELECT ...)         |
| EXISTS/NOT   | EXISTS       | true/false  | EXISTS (SELECT ...)               |
| Correlated   | any          | varies      | salary > (SELECT AVG(...) ...)    |

---




