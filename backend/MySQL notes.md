# Complete SQL Revision Guide
## 1. SQL Fundamentals
### What is SQL?
**SQL (Structured Query Language)** - A language used to access and manipulate data in relational databases.
**RDBMS (Relational Database Management System)** - Software that enables implementation of relational models (e.g., MySQL, MS SQL, Oracle, IBM DB2).
### SQL vs MySQL
- **SQL**: Query language used to perform CRUD operations
- **MySQL**: An RDBMS that stores, manages, and administrates databases using SQL
### CRUD Operations
- **CREATE**: Insert new tuples into relations
- **READ**: Read existing data from relations
- **UPDATE**: Modify already inserted data
- **DELETE**: Delete specific rows or multiple rows
---
## 2. SQL Data Types
### String Types
| Data Type | Description | Range |
|-----------|-------------|-------|
| CHAR | Fixed-length string | 0-255 characters |
| VARCHAR | Variable-length string | 0-255 characters |
| TINYTEXT | Text string | 0-255 characters |
| TEXT | Text string | 0-65,535 characters |
| MEDIUMTEXT | Text string | 0-16,777,215 characters |
| LONGTEXT | Text string | 0-4,294,967,295 characters |
| BLOB | Binary Large Object | 0-65,535 bytes |
| MEDIUMBLOB | Binary Large Object | 0-16,777,215 bytes |
| LONGBLOB | Binary Large Object | 0-4,294,967,295 bytes |
### Numeric Types
| Data Type | Description | Range |
|-----------|-------------|-------|
| TINYINT | Integer | -128 to 127 |
| SMALLINT | Integer | -32,768 to 32,767 |
| MEDIUMINT | Integer | -8,388,608 to 8,388,607 |
| INT | Integer | -2,147,483,648 to 2,147,483,647 |
| BIGINT | Integer | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |
| FLOAT | Decimal | Precision to 23 digits |
| DOUBLE | Decimal | 24 to 53 digits |
| DECIMAL | Decimal stored as string | User-defined precision |
### Date & Time Types
| Data Type | Format |
|-----------|--------|
| DATE | YYYY-MM-DD |
| DATETIME | YYYY-MM-DD HH:MM:SS |
| TIMESTAMP | YYYYMMDDHHMMSS |
| TIME | HH:MM:SS |
### Other Types
- **ENUM**: One of preset values
- **SET**: One or many of preset values
- **BOOLEAN**: 0 or 1
- **BIT(n)**: Store values in bits (n up to 64)
**Note**: Variable-length data types (VARCHAR) are preferred as they occupy space equal to actual data size. Values can also be UNSIGNED (e.g., INT UNSIGNED).
---
## 3. Types of SQL Commands
### DDL (Data Definition Language)
Defines relation schema
- CREATE
- ALTER TABLE
- DROP
- TRUNCATE
- RENAME
### DRL/DQL (Data Retrieval/Query Language)
Retrieves data from tables
- SELECT
### DML (Data Manipulation Language)
Modifies data in the database
- INSERT
- UPDATE
- DELETE
### DCL (Data Control Language)
Grants or revokes user authorities
- GRANT
- REVOKE
### TCL (Transaction Control Language)
Manages database transactions
- START TRANSACTION
- COMMIT
- ROLLBACK
- SAVEPOINT
---
## 4. Managing Databases (DDL)
### Create Database
```sql
CREATE DATABASE IF NOT EXISTS db_name;
```
### Use Database
```sql
USE db_name;
```
Makes it possible to switch between databases and specifies which database subsequent commands will execute on.
### Drop Database
```sql
DROP DATABASE IF EXISTS db_name;
```
### Show Databases
```sql
SHOW DATABASES;
```
Lists all databases in the server.
### Show Tables
```sql
SHOW TABLES;
```
Lists all tables in the selected database.
---
## 5. Creating Tables
### Basic Syntax
```sql
CREATE TABLE table_name (
    column1 datatype constraints,
    column2 datatype constraints,
    column3 datatype constraints,
    ...
);
```
### Example
```sql
CREATE TABLE customer (
    id INT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(1024) UNIQUE,
    age INT,
    CONSTRAINT age_check CHECK (age > 12)
);
```
---
## 6. Constraints
### PRIMARY KEY
- **Definition**: Unique identifier for each record; NOT NULL and UNIQUE
- **Limit**: Only one per table
```sql
CREATE TABLE student (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);
```
### FOREIGN KEY
- **Definition**: References PRIMARY KEY of another table
- **Limit**: A table can have multiple foreign keys
```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    delivery_date DATE,
    order_placed_date DATE,
    cust_id INT,
    FOREIGN KEY (cust_id) REFERENCES customer(id)
);
```
### UNIQUE
- **Definition**: Ensures all values in column are unique
- **Features**: Can be NULL; table can have multiple UNIQUE attributes
```sql
CREATE TABLE customer (
    id INT PRIMARY KEY,
    email VARCHAR(1024) UNIQUE
);
```
### CHECK
- **Definition**: Ensures values in column satisfy specific condition
```sql
CREATE TABLE customer (
    id INT PRIMARY KEY,
    age INT,
    CONSTRAINT age_check CHECK (age > 12)
);
```
Note: Constraint name ("age_check") is optional; MySQL auto-generates if omitted.
### DEFAULT
- **Definition**: Sets default value for column
```sql
CREATE TABLE account (
    id INT PRIMARY KEY,
    saving_rate DOUBLE NOT NULL DEFAULT 4.25
);
```
### Special Cases
- An attribute can be both PRIMARY KEY and FOREIGN KEY in the same table
---
## 7. ALTER Operations (DDL)
### ADD Column
```sql
ALTER TABLE table_name
ADD new_col_name datatype
ADD new_col_name_2 datatype;
```
**Example:**
```sql
ALTER TABLE customer ADD age INT NOT NULL;
```
### MODIFY Column (Change Datatype)
```sql
ALTER TABLE table_name
MODIFY col_name col_datatype;
```
**Example:**
```sql
ALTER TABLE customer MODIFY name CHAR(1024);
```
### CHANGE COLUMN (Rename Column)
```sql
ALTER TABLE table_name
CHANGE COLUMN old_col_name new_col_name new_col_datatype;
```
**Example:**
```sql
ALTER TABLE customer
CHANGE COLUMN name customer_name VARCHAR(1024);
```
### DROP COLUMN
```sql
ALTER TABLE table_name
DROP COLUMN col_name;
```
**Example:**
```sql
ALTER TABLE customer DROP COLUMN middle_name;
```
### RENAME Table
```sql
ALTER TABLE table_name
RENAME TO new_table_name;
```
**Example:**
```sql
ALTER TABLE customer RENAME TO customer_details;
```
---
## 8. Data Retrieval Language (DRL)
### Basic SELECT
```sql
SELECT column1, column2 FROM table_name;
```
**Execution Order**: RIGHT to LEFT (FROM → SELECT)
### SELECT All Columns
```sql
SELECT * FROM table_name;
```
### DUAL Tables
- Dummy tables created by MySQL for operations without user-defined tables
```sql
SELECT 55 + 11;
SELECT NOW();
SELECT UCASE('hello');
```
---
## 9. Filtering Data
### WHERE Clause
Reduces rows based on conditions.
```sql
SELECT * FROM customer WHERE age > 18;
```
### BETWEEN
Both values are **inclusive**.
```sql
SELECT * FROM customer WHERE age BETWEEN 0 AND 100;
```
### IN
Reduces multiple OR conditions.
```sql
SELECT * FROM officers
WHERE officer_name IN ('Lakshay', 'Maharana Pratap', 'Deepika');
```
### AND / OR / NOT
**AND:**
```sql
SELECT * FROM customer WHERE age > 18 AND country = 'India';
```
**OR:**
```sql
SELECT * FROM customer WHERE age < 18 OR age > 65;
```
**NOT:**
```sql
SELECT * FROM customer WHERE id NOT IN (1, 2, 3, 4);
```
### IS NULL
```sql
SELECT * FROM customer WHERE prime_status IS NULL;
```
---
## 10. Pattern Searching (Wildcards)
### Wildcards
- **%**: Any number of characters (0 to n) - similar to * in regex
- **_**: Exactly one character
### LIKE Operator
```sql
SELECT * FROM customer WHERE name LIKE '%p_';
```
This finds names ending with 'p' followed by any single character.
**More Examples:**
```sql
-- Names starting with 'A'
SELECT * FROM customer WHERE name LIKE 'A%';
-- Names containing 'son'
SELECT * FROM customer WHERE name LIKE '%son%';
-- Names with exactly 5 characters
SELECT * FROM customer WHERE name LIKE '_____';
```
---
## 11. Sorting Data
### ORDER BY
Sorts retrieved data.
**Ascending (Default):**
```sql
SELECT * FROM customer ORDER BY name;
-- OR
SELECT * FROM customer ORDER BY name ASC;
```
**Descending:**
```sql
SELECT * FROM customer ORDER BY name DESC;
```
**Multiple Columns:**
```sql
SELECT * FROM customer ORDER BY country ASC, name DESC;
```
---
## 12. Grouping Data
### GROUP BY
Collects data from multiple records and groups results by one or more columns.
```sql
SELECT country, COUNT(cust_id)
FROM customer
GROUP BY country;
```
**Important Rule**: All column names in SELECT (except aggregate functions) must appear in GROUP BY.
```sql
SELECT c1, c2, c3, COUNT(*)
FROM sample_table
WHERE condition
GROUP BY c1, c2, c3;
```
### Aggregate Functions
Use with GROUP BY to perform calculations:
**COUNT()** - Count rows
```sql
SELECT country, COUNT(*) FROM customer GROUP BY country;
```
**SUM()** - Sum values
```sql
SELECT category, SUM(price) FROM products GROUP BY category;
```
**AVG()** - Average values
```sql
SELECT category, AVG(price) FROM products GROUP BY category;
```
**MIN()** - Minimum value
```sql
SELECT category, MIN(price) FROM products GROUP BY category;
```
**MAX()** - Maximum value
```sql
SELECT category, MAX(price) FROM products GROUP BY category;
```
---
## 13. DISTINCT
### Find Unique Values
```sql
SELECT DISTINCT(col_name) FROM table_name;
```
**Alternative with GROUP BY:**
```sql
SELECT col_name FROM table_name GROUP BY col_name;
```
Note: SQL recognizes that GROUP BY without aggregation means DISTINCT.
---
## 14. HAVING Clause
### Definition
Filters groups created by GROUP BY based on conditions (similar to WHERE but for groups).
```sql
SELECT COUNT(cust_id), country
FROM customer
GROUP BY country
HAVING COUNT(cust_id) > 50;
```
### WHERE vs HAVING
| WHERE | HAVING |
|-------|--------|
| Filters rows before grouping | Filters groups after grouping |
| Used before GROUP BY | Used after GROUP BY |
| Can be used without GROUP BY | Requires GROUP BY |
| Works with SELECT, UPDATE, DELETE | Only works with SELECT |
| Cannot use aggregate functions | Can use aggregate functions |
**Example showing both:**
```sql
SELECT category, COUNT(*), AVG(price)
FROM products
WHERE price > 100
GROUP BY category
HAVING COUNT(*) > 5;
```
---
## 15. Data Manipulation Language (DML)
### INSERT
**Insert Single Row:**
```sql
INSERT INTO table_name (col1, col2, col3)
VALUES (v1, v2, v3);
```
**Insert Multiple Rows:**
```sql
INSERT INTO table_name (col1, col2, col3)
VALUES
    (v1, v2, v3),
    (val1, val2, val3),
    (value1, value2, value3);
```
**Example:**
```sql
INSERT INTO customer (id, name, email, age)
VALUES
    (1, 'John Doe', 'john@email.com', 25),
    (2, 'Jane Smith', 'jane@email.com', 30);
```
### UPDATE
**Update Specific Row:**
```sql
UPDATE table_name
SET col1 = 1, col2 = 'abc'
WHERE id = 1;
```
**Update Multiple Rows:**
```sql
UPDATE student SET standard = standard + 1;
```
**Example:**
```sql
UPDATE customer
SET age = 26, email = 'newemail@email.com'
WHERE id = 1;
```
### ON UPDATE CASCADE
Automatically updates foreign key when referenced primary key is updated.
```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    cust_id INT,
    FOREIGN KEY (cust_id) REFERENCES customer(id) ON UPDATE CASCADE
);
```
### DELETE
**Delete Specific Row:**
```sql
DELETE FROM table_name WHERE id = 1;
```
**Delete All Rows:**
```sql
DELETE FROM table_name;
```
### DELETE CASCADE
Automatically deletes child records when parent record is deleted.
```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    delivery_date DATE,
    cust_id INT,
    FOREIGN KEY (cust_id) REFERENCES customer(id) ON DELETE CASCADE
);
```
### ON DELETE SET NULL
Sets foreign key to NULL when parent record is deleted.
```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    delivery_date DATE,
    cust_id INT,
    FOREIGN KEY (cust_id) REFERENCES customer(id) ON DELETE SET NULL
);
```
### REPLACE
Works as UPDATE for existing records and INSERT for new records.
**Method 1:**
```sql
REPLACE INTO student (id, class) VALUES (4, 3);
```
**Method 2:**
```sql
REPLACE INTO table_name SET col1 = val1, col2 = val2;
```
---
## 16. Joining Tables
### Aliases (AS)
Gives temporary names to tables or columns for a query.
```sql
SELECT col_name AS alias_name FROM table_name;
SELECT col1, col2 FROM table_name AS alias_name;
```
**Example:**
```sql
SELECT c.name, o.order_date
FROM customer AS c, orders AS o
WHERE c.id = o.cust_id;
```
### INNER JOIN
Returns records with matching values in both tables.
**Syntax:**
```sql
SELECT column_list
FROM table1
INNER JOIN table2 ON condition1
INNER JOIN table3 ON condition2;
```
**Example:**
```sql
SELECT customers.name, orders.order_date
FROM customers
INNER JOIN orders ON customers.id = orders.cust_id;
```
### LEFT JOIN (LEFT OUTER JOIN)
Returns all records from left table and matched records from right table. Unmatched right records are NULL.
**Syntax:**
```sql
SELECT columns
FROM table1
LEFT JOIN table2 ON join_condition;
```
**Example:**
```sql
SELECT customers.name, orders.order_date
FROM customers
LEFT JOIN orders ON customers.id = orders.cust_id;
```
### RIGHT JOIN (RIGHT OUTER JOIN)
Returns all records from right table and matched records from left table. Unmatched left records are NULL.
**Syntax:**
```sql
SELECT columns
FROM table1
RIGHT JOIN table2 ON join_condition;
```
**Example:**
```sql
SELECT customers.name, orders.order_date
FROM customers
RIGHT JOIN orders ON customers.id = orders.cust_id;
```
### FULL JOIN (FULL OUTER JOIN)
Returns all records when there is a match in either left or right table. **Emulated in MySQL using UNION.**
**Syntax:**
```sql
SELECT columns
FROM table1 AS t1
LEFT JOIN table2 AS t2 ON t1.id = t2.id
UNION
SELECT columns
FROM table1 AS t1
RIGHT JOIN table2 AS t2 ON t1.id = t2.id;
```
**Note**: Use UNION ALL to include duplicates; UNION gives only unique values.
### CROSS JOIN
Returns Cartesian product of both tables (all possible combinations).
**Syntax:**
```sql
SELECT column_list
FROM table1
CROSS JOIN table2;
```
**Example:**
```sql
-- If table1 has 10 rows and table2 has 5 rows, result has 50 rows
SELECT * FROM colors CROSS JOIN sizes;
```
**Note**: Rarely used in practical applications.
### SELF JOIN
Joins a table to itself. **Emulated using INNER JOIN.**
**Syntax:**
```sql
SELECT columns
FROM table AS t1
INNER JOIN table AS t2 ON t1.id = t2.id;
```
**Example:**
```sql
SELECT e1.name AS Employee, e2.name AS Manager
FROM employees AS e1
INNER JOIN employees AS e2 ON e1.manager_id = e2.id;
```
### Join Without JOIN Keyword
```sql
SELECT * FROM table1, table2 WHERE condition;
```
**Example:**
```sql
SELECT artist_name, album_name, year_recorded
FROM artist, album
WHERE artist.id = album.artist_id;
```
---
## 17. JOIN vs SET Operations
| Feature | JOIN | SET Operations |
|---------|------|----------------|
| Combination Type | Based on matching condition | Combination of result sets |
| Direction | Column-wise (horizontal) | Row-wise (vertical) |
| Data Types | Can be different | Must be same for corresponding columns |
| Row Type | Can generate distinct or duplicate | Generates distinct rows only |
| Column Count | May or may not be same | Must be same from each table |
---
## 18. Set Operations
### UNION
Combines results from multiple SELECT statements (distinct rows only).
**Syntax:**
```sql
SELECT * FROM table1
UNION
SELECT * FROM table2;
```
**Requirements:**
- Number of columns must be same
- Order of columns must be same
- Data types must be compatible
**Example:**
```sql
SELECT name, email FROM customers
UNION
SELECT name, email FROM suppliers;
```
**UNION ALL** - Includes duplicates:
```sql
SELECT name FROM customers
UNION ALL
SELECT name FROM suppliers;
```
### INTERSECT (Emulated)
Returns common values from tables.
**Syntax:**
```sql
SELECT DISTINCT column_list
FROM table1
INNER JOIN table2 USING(join_column);
```
**Example:**
```sql
SELECT DISTINCT *
FROM table1
INNER JOIN table2 USING(id);
```
### MINUS (Emulated)
Returns rows from first table that don't exist in second table.
**Syntax:**
```sql
SELECT column_list
FROM table1
LEFT JOIN table2 ON condition
WHERE table2.column_name IS NULL;
```
**Example:**
```sql
SELECT id
FROM table1
LEFT JOIN table2 USING(id)
WHERE table2.id IS NULL;
```
---
## 19. Subqueries
### Definition
**Subquery**: A query nested inside another query (outer query depends on inner query).
- Alternative to joins
- Also called nested queries
### Basic Syntax
```sql
SELECT column_list
FROM table_name
WHERE column_name OPERATOR (
    SELECT column_list
    FROM table_name
    WHERE condition
);
```
**Example:**
```sql
SELECT * FROM employees
WHERE salary > (
    SELECT AVG(salary) FROM employees
);
```
### Subquery in WHERE Clause
```sql
SELECT * FROM products
WHERE category_id IN (
    SELECT id FROM categories WHERE name = 'Electronics'
);
```
### Subquery in FROM Clause
```sql
SELECT MAX(rating)
FROM (
    SELECT * FROM movie WHERE country = 'India'
) AS temp;
```
### Subquery in SELECT Clause
```sql
SELECT
    name,
    (SELECT COUNT(*) FROM orders WHERE orders.cust_id = customers.id) AS order_count
FROM customers;
```
### Derived Subquery
```sql
SELECT column_list
FROM (
    SELECT column_list
    FROM table_name
    WHERE condition
) AS new_table_name;
```
### Correlated Subquery
Inner query executes once for each row processed by outer query (inner query driven by outer query).
**Example:**
```sql
SELECT e1.name, e1.salary
FROM employees e1
WHERE salary > (
    SELECT AVG(salary)
    FROM employees e2
    WHERE e2.department = e1.department
);
```
---
## 20. JOIN vs Subqueries
| Feature | JOIN | Subquery |
|---------|------|----------|
| Speed | Faster | Slower |
| Calculation | DBMS handles calculation | User responsible for calculation |
| Complexity | Complex, difficult to implement | Easier to understand |
| Optimization | Choosing optimal join is difficult | Easier to write |
---
## 21. MySQL Views
### Definition
**View**: A virtual table with no actual data storage. Contents based on base table(s) through a query.
**Characteristics:**
- Contains rows and columns like real tables
- Does not store data independently
- Changes in underlying table(s) reflect in view
- Built on top of other tables or views
### CREATE VIEW
```sql
CREATE VIEW view_name AS
SELECT columns
FROM tables
WHERE conditions;
```
**Example:**
```sql
CREATE VIEW customer_orders AS
SELECT c.name, c.email, o.order_date, o.total
FROM customers c
INNER JOIN orders o ON c.id = o.cust_id;
```
**View with Join:**
```sql
CREATE VIEW trainer_info AS
SELECT c.course_name, c.trainer, t.email
FROM courses c, contact t
WHERE c.id = t.id;
```
### ALTER VIEW
```sql
ALTER VIEW view_name AS
SELECT columns
FROM tables
WHERE conditions;
```
### DROP VIEW
```sql
DROP VIEW IF EXISTS view_name;
```
### Using Views
```sql
-- Query a view like a regular table
SELECT * FROM customer_orders WHERE order_date > '2024-01-01';
```
---
## 22. DROP vs TRUNCATE vs DELETE
| Command | Type | Description | Rollback | Speed |
|---------|------|-------------|----------|-------|
| DROP | DDL | Removes entire table structure | Cannot rollback | Fast |
| TRUNCATE | DDL | Removes all rows, keeps structure | Cannot rollback | Fast |
| DELETE | DML | Removes specific rows | Can rollback | Slower |
**Examples:**
```sql
-- DROP: Removes table completely
DROP TABLE customers;
-- TRUNCATE: Removes all data, keeps structure
TRUNCATE TABLE customers;
-- DELETE: Removes specific rows
DELETE FROM customers WHERE id = 1;
DELETE FROM customers;  -- Removes all rows but slower than TRUNCATE
```
---
## 23. Important Notes
### Import/Export
- Table schema can be imported/exported from files (.csv or .json)
### Query Execution Best Practices
1. Use appropriate indexes for frequently queried columns
2. Use EXPLAIN to analyze query performance
3. Avoid SELECT * in production; specify needed columns
4. Use LIMIT for large result sets
5. Properly index foreign key columns
### NULL Handling
- NULL is not equal to NULL (use IS NULL or IS NOT NULL)
- Aggregate functions ignore NULL values
- COALESCE() can provide default values for NULL
```sql
SELECT COALESCE(column_name, 'default_value') FROM table_name;
```
---
## Quick Reference Card
### Query Order of Execution
```sql
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
```
### Common Query Template
```sql
SELECT column_list
FROM table_name
WHERE conditions
GROUP BY columns
HAVING group_conditions
ORDER BY columns ASC|DESC
LIMIT number;
```
### Constraint Summary
- **PRIMARY KEY**: Unique, NOT NULL, one per table
- **FOREIGN KEY**: References another table's PK, multiple allowed
- **UNIQUE**: Unique values, can be NULL, multiple allowed
- **CHECK**: Validates data against condition
- **DEFAULT**: Sets default value
- **NOT NULL**: Ensures column cannot be NULL
---
## Study Tips
1. **Practice DDL**: Create, alter, and drop tables regularly
2. **Master Joins**: Understand difference between INNER, LEFT, RIGHT, FULL
3. **Aggregate Functions**: Practice with GROUP BY and HAVING
4. **Subqueries vs Joins**: Know when to use each
5. **Constraints**: Understand CASCADE options for FK
6. **Set Operations**: Remember UNION removes duplicates
7. **Views**: Useful for simplifying complex queries
8. **NULL Handling**: Always consider NULL in WHERE conditions