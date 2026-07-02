---
title: Databases — SQL
tags: [database, sql, sql-server, oracle, t-sql, relational]
related: [Entity Framework, CSharp, ASP-NET, Databases-NoSQL]
sources: [DataBase/SQL Server.md, DataBase/T-SQL.md, DataBase/SQL2016.md, DataBase/Databases Stanford.md, DataBase/LinqToSql.md, Oracle/12c Fundamentals.md, Oracle/Cheat Shheet.md, Oracle/Getting Productive with SQL Developer.md, Oracle/Oracle SQL Explorer.md]
updated: 2026-04-12
---

# Databases — SQL

Relational databases store data in tables with rows and columns. Relationships are defined by foreign keys. SQL (Structured Query Language) is the standard query language.

---

## Core SQL syntax

### DDL — Data Definition Language

```sql
-- Create a table
CREATE TABLE Users (
    Id          INT PRIMARY KEY IDENTITY(1,1),  -- auto-increment
    Email       VARCHAR(50) NOT NULL,
    CreatedAt   DATETIME DEFAULT GETDATE()
);

-- GUID primary key
CREATE TABLE Orders (
    Id UNIQUEIDENTIFIER PRIMARY KEY DEFAULT NEWID(),
    UserId INT FOREIGN KEY REFERENCES Users(Id)
);

-- Modify
ALTER TABLE Users ADD Name VARCHAR(100);

-- Drop
DROP TABLE Users;
```

### DML — Data Manipulation Language

```sql
-- Select
SELECT column1, column2
FROM table_name
WHERE condition
ORDER BY column1 ASC, column2 DESC;

-- Insert
INSERT INTO Users (Email, Name)
VALUES ('ken@example.com', 'Ken');

-- Update
UPDATE Users
SET Name = 'Ken Li'
WHERE Id = 1;

-- Delete
DELETE FROM Users
WHERE Id = 1;
```

---

## Joins

```sql
-- INNER JOIN — only matching rows from both tables
SELECT u.Name, o.OrderDate
FROM Users u
INNER JOIN Orders o ON u.Id = o.UserId;

-- LEFT JOIN — all rows from left, nulls for non-matching right
SELECT u.Name, o.OrderDate
FROM Users u
LEFT JOIN Orders o ON u.Id = o.UserId;

-- RIGHT JOIN — all rows from right, nulls for non-matching left
-- FULL JOIN — all rows from both sides
```

**Join types visualised:**
- `INNER` — intersection only
- `LEFT` — all of left table + matches from right
- `RIGHT` — all of right table + matches from left
- `FULL OUTER` — everything from both sides

---

## Aggregates and grouping

```sql
SELECT Category, COUNT(*), SUM(Price), AVG(Price), MAX(Price), MIN(Price)
FROM Products
GROUP BY Category
HAVING COUNT(*) > 5;   -- filter on aggregated result (not WHERE)
```

**GROUP BY rules:** Any column in SELECT that is not aggregated must appear in GROUP BY.

---

## Subqueries and CTEs

```sql
-- Subquery in WHERE
SELECT * FROM Users
WHERE Id IN (SELECT UserId FROM Orders WHERE Total > 100);

-- Common Table Expression (CTE) — named subquery
WITH HighValueOrders AS (
    SELECT UserId, SUM(Total) AS TotalSpend
    FROM Orders
    GROUP BY UserId
    HAVING SUM(Total) > 500
)
SELECT u.Name, h.TotalSpend
FROM Users u
JOIN HighValueOrders h ON u.Id = h.UserId;
```

---

## T-SQL (SQL Server specific)

```sql
-- Variables
DECLARE @userId INT = 1;
DECLARE @name NVARCHAR(100);

-- If/Else
IF @userId > 0
    PRINT 'Valid'
ELSE
    PRINT 'Invalid';

-- While loop
WHILE @userId < 10
BEGIN
    SET @userId = @userId + 1;
END

-- CASE expression
SELECT
    Name,
    CASE
        WHEN Score >= 90 THEN 'A'
        WHEN Score >= 80 THEN 'B'
        ELSE 'C'
    END AS Grade
FROM Students;

-- Stored procedure
CREATE PROCEDURE GetUserById @Id INT
AS
BEGIN
    SELECT * FROM Users WHERE Id = @Id;
END

-- Execute
EXEC GetUserById @Id = 1;
```

---

## SQL Server

Microsoft's relational database system.

**Tools:**
| Tool | Purpose |
|---|---|
| **SSMS** (SQL Server Management Studio) | GUI for queries, schema, monitoring |
| **SQL Server Profiler** | Trace all commands — useful for performance analysis |
| **Database Engine Tuning Advisor** | Recommends indexes based on profiler traces |
| **SQL Server Agent** | Schedule and automate jobs |
| **SSIS** (Integration Services) | ETL — move/transform data between systems |
| **SSAS** (Analysis Services) | OLAP cubes for analytics |
| **SSRS** (Reporting Services) | Report generation |

**Editions:** Enterprise, Standard, Developer (free for dev/test), Express (free, limited), Web.

**Editions feature scale:** Enterprise > Standard > Web > Express

---

## Oracle SQL

Oracle uses similar ANSI SQL with some differences:

```sql
-- Oracle uses SEQUENCE for auto-increment (older style)
CREATE SEQUENCE user_seq START WITH 1 INCREMENT BY 1;
INSERT INTO Users VALUES (user_seq.NEXTVAL, 'ken@example.com');

-- Oracle uses ROWNUM for top-N (instead of TOP/LIMIT)
SELECT * FROM Users WHERE ROWNUM <= 10;

-- NVL instead of ISNULL/COALESCE
SELECT NVL(Name, 'Unknown') FROM Users;

-- Dual — Oracle's dummy table for expressions
SELECT SYSDATE FROM DUAL;
```

**Oracle tools:** SQL Developer (IDE), SQL*Plus (CLI).

---

## Indexes

Indexes speed up reads but slow down writes and use disk space.

```sql
-- Create index
CREATE INDEX idx_users_email ON Users(Email);

-- Unique index (enforces uniqueness)
CREATE UNIQUE INDEX idx_users_email_unique ON Users(Email);

-- Composite index
CREATE INDEX idx_orders_user_date ON Orders(UserId, OrderDate);

-- Drop index
DROP INDEX idx_users_email ON Users;
```

**When to index:** Columns frequently used in `WHERE`, `JOIN`, or `ORDER BY` clauses. Don't over-index — every write updates all relevant indexes.

---

## Transactions

```sql
BEGIN TRANSACTION;
    UPDATE Accounts SET Balance = Balance - 100 WHERE Id = 1;
    UPDATE Accounts SET Balance = Balance + 100 WHERE Id = 2;
    -- both succeed
COMMIT;

-- or on failure
ROLLBACK;
```

**ACID properties:** Atomicity, Consistency, Isolation, Durability.

---

## See also
- [[Entity-Framework]] — ORM that generates SQL from C# code
- [[CSharp]] — LINQ generates SQL; `System.Data` for raw ADO.NET
- [[Databases-NoSQL]] — document/key-value alternatives
