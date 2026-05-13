# MariaDB Comprehensive Cheatsheet

> **Complete MariaDB cheatsheet covering installation, user management, CRUD, joins, indexes, stored procedures, triggers, transactions, backup/restore, performance tuning, JSON functions, security, and more. Includes practical examples and quick reference for developers and DBAs.**

## Table of Contents

1. [Installation & Basic Setup](#1-installation--basic-setup)
2. [Connecting to MariaDB](#2-connecting-to-mariadb)
3. [User & Permission Management](#3-user--permission-management)
4. [Database Operations](#4-database-operations)
5. [Table Operations](#5-table-operations)
6. [Data Types](#6-data-types)
7. [CRUD Operations](#7-crud-operations)
8. [Querying & Filtering](#8-querying--filtering)
9. [Joins](#9-joins)
10. [Indexes](#10-indexes)
11. [Constraints](#11-constraints)
12. [Views](#12-views)
13. [Stored Procedures & Functions](#13-stored-procedures--functions)
14. [Triggers](#14-triggers)
15. [Transactions](#15-transactions)
16. [Backup & Restore](#16-backup--restore)
17. [Performance Tuning](#17-performance-tuning)
18. [Monitoring & Diagnostics](#18-monitoring--diagnostics)
19. [Full-Text Search](#19-full-text-search)
20. [JSON Functions](#20-json-functions)
21. [Common Functions](#21-common-functions)
22. [Import/Export Data](#22-importexport-data)
23. [Security Best Practices](#23-security-best-practices)

---

## 1. Installation & Basic Setup

### Linux (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install mariadb-server mariadb-client
sudo mysql_secure_installation
```

### Linux (RHEL/CentOS)
```bash
sudo yum install mariadb-server mariadb
sudo systemctl start mariadb
sudo mysql_secure_installation
```

### Windows
Download from [mariadb.org](https://mariadb.org/download/)

### Start/Stop/Enable Service
```bash
sudo systemctl start mariadb
sudo systemctl stop mariadb
sudo systemctl restart mariadb
sudo systemctl enable mariadb
sudo systemctl status mariadb
```

---

## 2. Connecting to MariaDB

### Local Connection
```bash
mysql -u username -p
mysql -u root -p
```

### Remote Connection
```bash
mysql -h hostname -u username -p
mysql -h 192.168.1.100 -P 3306 -u user -p
```

### With Database Selected
```bash
mysql -u username -p database_name
```

### Execute SQL File
```bash
mysql -u username -p database_name < file.sql
```

### Show Connection Info
```sql
SELECT USER(), CURRENT_USER(), DATABASE(), VERSION();
```

---

## 3. User & Permission Management

### Create Users
```sql
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
CREATE USER 'username'@'%' IDENTIFIED BY 'password';
CREATE USER 'username'@'192.168.1.%' IDENTIFIED BY 'password';
```

### Drop Users
```sql
DROP USER 'username'@'localhost';
```

### Grant Privileges
```sql
-- All privileges on all databases
GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost';

-- Specific database
GRANT ALL PRIVILEGES ON database_name.* TO 'username'@'localhost';

-- Specific table
GRANT SELECT, INSERT, UPDATE ON database_name.table_name TO 'username'@'localhost';

-- Read-only access
GRANT SELECT ON *.* TO 'readonly'@'%';

-- With grant option
GRANT ALL ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;
```

### Revoke Privileges
```sql
REVOKE INSERT, UPDATE ON database_name.* FROM 'username'@'localhost';
REVOKE ALL PRIVILEGES ON *.* FROM 'username'@'localhost';
```

### Show Privileges
```sql
SHOW GRANTS FOR 'username'@'localhost';
SHOW GRANTS FOR CURRENT_USER();
```

### Manage Passwords
```sql
SET PASSWORD FOR 'username'@'localhost' = PASSWORD('newpass');
ALTER USER 'username'@'localhost' IDENTIFIED BY 'newpass';
```

### Flush Privileges
```sql
FLUSH PRIVILEGES;
```

---

## 4. Database Operations

### Create Database
```sql
CREATE DATABASE database_name;
CREATE DATABASE IF NOT EXISTS database_name;
CREATE DATABASE database_name CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### Show Databases
```sql
SHOW DATABASES;
SHOW CREATE DATABASE database_name;
```

### Use Database
```sql
USE database_name;
```

### Alter Database
```sql
ALTER DATABASE database_name CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

### Drop Database
```sql
DROP DATABASE database_name;
DROP DATABASE IF EXISTS database_name;
```

---

## 5. Table Operations

### Create Table
```sql
CREATE TABLE table_name (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    age INT,
    email VARCHAR(255) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- With foreign key
CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    amount DECIMAL(10,2),
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

### Show Tables
```sql
SHOW TABLES;
SHOW FULL TABLES;
SHOW TABLE STATUS;
```

### Show Table Structure
```sql
DESCRIBE table_name;
DESC table_name;
SHOW COLUMNS FROM table_name;
SHOW CREATE TABLE table_name;
```

### Alter Table
```sql
-- Add column
ALTER TABLE table_name ADD column_name DATATYPE;
ALTER TABLE table_name ADD column_name DATATYPE AFTER existing_column;

-- Drop column
ALTER TABLE table_name DROP COLUMN column_name;

-- Modify column
ALTER TABLE table_name MODIFY column_name NEW_DATATYPE;
ALTER TABLE table_name CHANGE old_name new_name DATATYPE;

-- Rename table
ALTER TABLE table_name RENAME TO new_name;
RENAME TABLE table_name TO new_name;

-- Add index
ALTER TABLE table_name ADD INDEX index_name (column_name);
ALTER TABLE table_name ADD UNIQUE INDEX index_name (column_name);

-- Drop index
ALTER TABLE table_name DROP INDEX index_name;
```

### Drop Table
```sql
DROP TABLE table_name;
DROP TABLE IF EXISTS table_name;
TRUNCATE TABLE table_name;  -- Removes all data but keeps structure
```

### Copy Table
```sql
-- Copy structure only
CREATE TABLE new_table LIKE old_table;

-- Copy structure and data
CREATE TABLE new_table AS SELECT * FROM old_table;
```

---

## 6. Data Types

### Numeric Types
| Type | Range/Size |
|------|-------------|
| `TINYINT` | -128 to 127 |
| `SMALLINT` | -32,768 to 32,767 |
| `MEDIUMINT` | -8,388,608 to 8,388,607 |
| `INT` | -2.1B to 2.1B |
| `BIGINT` | -9.22e18 to 9.22e18 |
| `DECIMAL(M,D)` | Exact decimal |
| `FLOAT` | Approx (4 bytes) |
| `DOUBLE` | Approx (8 bytes) |
| `BOOLEAN` / `BOOL` | 0 or 1 |

### String Types
| Type | Description |
|------|-------------|
| `CHAR(n)` | Fixed length (0-255) |
| `VARCHAR(n)` | Variable length (0-65,535) |
| `TINYTEXT` | Max 255 chars |
| `TEXT` | Max 65,535 chars |
| `MEDIUMTEXT` | Max 16,777,215 chars |
| `LONGTEXT` | Max 4GB |
| `BLOB` | Binary data |
| `ENUM('a','b')` | Enumeration |
| `SET('a','b')` | Set of values |

### Date/Time Types
| Type | Format | Range |
|------|--------|-------|
| `DATE` | YYYY-MM-DD | 1000-01-01 to 9999-12-31 |
| `TIME` | HH:MM:SS | -838:59:59 to 838:59:59 |
| `DATETIME` | YYYY-MM-DD HH:MM:SS | 1000-01-01 to 9999-12-31 |
| `TIMESTAMP` | YYYY-MM-DD HH:MM:SS | 1970-01-01 to 2038-01-19 |
| `YEAR` | YYYY | 1901 to 2155 |

### JSON Type
```sql
JSON  -- Valid JSON data
```

### Spatial Types
```sql
POINT, LINESTRING, POLYGON, GEOMETRY, MULTIPOINT, etc.
```

---

## 7. CRUD Operations

### INSERT
```sql
-- Single row
INSERT INTO table_name (col1, col2) VALUES (value1, value2);

-- Multiple rows
INSERT INTO table_name (col1, col2) VALUES 
    (value1, value2),
    (value3, value4);

-- Insert from another table
INSERT INTO table1 (col1, col2) SELECT col1, col2 FROM table2 WHERE condition;

-- Insert with ON DUPLICATE KEY UPDATE
INSERT INTO users (id, name) VALUES (1, 'John') 
ON DUPLICATE KEY UPDATE name = 'John';

-- Insert IGNORE (skip errors)
INSERT IGNORE INTO users (id, name) VALUES (1, 'John');
```

### SELECT
```sql
-- Basic
SELECT * FROM table_name;
SELECT col1, col2 FROM table_name;
SELECT DISTINCT col1 FROM table_name;

-- With conditions
SELECT * FROM table_name WHERE condition;

-- Limit and offset
SELECT * FROM table_name LIMIT 10;
SELECT * FROM table_name LIMIT 10 OFFSET 20;
SELECT * FROM table_name LIMIT 20, 10;  -- OFFSET 20, LIMIT 10

-- Ordering
SELECT * FROM table_name ORDER BY col1 ASC, col2 DESC;

-- Grouping
SELECT col1, COUNT(*) FROM table_name GROUP BY col1;
SELECT col1, AVG(col2) FROM table_name GROUP BY col1 HAVING AVG(col2) > 10;
```

### UPDATE
```sql
UPDATE table_name SET col1 = value1 WHERE condition;
UPDATE table_name SET col1 = col1 + 1 WHERE condition;
UPDATE table_name SET col1 = value1, col2 = value2 WHERE condition;
UPDATE table_name SET col1 = value1 ORDER BY col2 LIMIT 10;
```

### DELETE
```sql
DELETE FROM table_name WHERE condition;
DELETE FROM table_name WHERE condition ORDER BY col1 LIMIT 10;
DELETE FROM table_name;  -- Deletes all rows (slow)
TRUNCATE table_name;     -- Deletes all rows (fast, resets auto-increment)
```

### REPLACE
```sql
-- Deletes and inserts (if key exists)
REPLACE INTO table_name (id, name) VALUES (1, 'John');
```

---

## 8. Querying & Filtering

### WHERE Clause Operators
```sql
-- Comparison
WHERE col = value
WHERE col <> value
WHERE col != value
WHERE col > value
WHERE col < value
WHERE col >= value
WHERE col <= value

-- Range
WHERE col BETWEEN value1 AND value2
WHERE col NOT BETWEEN value1 AND value2

-- List
WHERE col IN (value1, value2, value3)
WHERE col NOT IN (value1, value2, value3)

-- Null
WHERE col IS NULL
WHERE col IS NOT NULL

-- Pattern matching
WHERE col LIKE 'pattern'   -- % = any chars, _ = single char
WHERE col LIKE 'John%'     -- Starts with John
WHERE col LIKE '%son'      -- Ends with son
WHERE col LIKE '%oh%'      -- Contains oh
WHERE col LIKE '_ohn'      -- Second character o, third h, fourth n

-- Regular expressions
WHERE col REGEXP '^[0-9]+$'

-- Logical
WHERE condition1 AND condition2
WHERE condition1 OR condition2
WHERE NOT condition
```

### Aggregation Functions
```sql
COUNT(*)           -- Count all rows
COUNT(DISTINCT col) -- Count distinct values
SUM(col)           -- Sum of values
AVG(col)           -- Average
MIN(col)           -- Minimum
MAX(col)           -- Maximum
GROUP_CONCAT(col)  -- Concatenate values
STDDEV(col)        -- Standard deviation
VARIANCE(col)      -- Variance
```

### GROUP BY & HAVING
```sql
SELECT 
    department,
    COUNT(*) as emp_count,
    AVG(salary) as avg_salary
FROM employees
WHERE status = 'active'
GROUP BY department
HAVING COUNT(*) > 5
ORDER BY avg_salary DESC;
```

### Subqueries
```sql
-- In WHERE
SELECT * FROM employees 
WHERE salary > (SELECT AVG(salary) FROM employees);

-- In FROM
SELECT dept_id, avg_salary 
FROM (SELECT dept_id, AVG(salary) as avg_salary FROM employees GROUP BY dept_id) as sub;

-- EXISTS / NOT EXISTS
SELECT * FROM departments d 
WHERE EXISTS (SELECT 1 FROM employees e WHERE e.dept_id = d.id);
```

### UNION
```sql
SELECT col1, col2 FROM table1
UNION                                   -- Removes duplicates
SELECT col1, col2 FROM table2;

SELECT col1, col2 FROM table1
UNION ALL                               -- Keeps duplicates
SELECT col1, col2 FROM table2;
```

### CASE Statement
```sql
SELECT 
    name,
    CASE 
        WHEN age < 18 THEN 'Minor'
        WHEN age BETWEEN 18 AND 65 THEN 'Adult'
        ELSE 'Senior'
    END as age_group
FROM users;
```

---

## 9. Joins

### INNER JOIN
```sql
SELECT * FROM table1 
INNER JOIN table2 ON table1.key = table2.key;

-- Shorthand
SELECT * FROM table1 t1, table2 t2 WHERE t1.key = t2.key;
```

### LEFT JOIN (LEFT OUTER JOIN)
```sql
SELECT * FROM table1 
LEFT JOIN table2 ON table1.key = table2.key;
```

### RIGHT JOIN (RIGHT OUTER JOIN)
```sql
SELECT * FROM table1 
RIGHT JOIN table2 ON table1.key = table2.key;
```

### FULL OUTER JOIN (simulated)
```sql
SELECT * FROM table1 
LEFT JOIN table2 ON table1.key = table2.key
UNION
SELECT * FROM table1 
RIGHT JOIN table2 ON table1.key = table2.key;
```

### CROSS JOIN (Cartesian product)
```sql
SELECT * FROM table1 CROSS JOIN table2;
```

### SELF JOIN
```sql
SELECT e1.name as employee, e2.name as manager 
FROM employees e1 
LEFT JOIN employees e2 ON e1.manager_id = e2.id;
```

### Multiple Joins
```sql
SELECT * 
FROM orders o
JOIN customers c ON o.customer_id = c.id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id;
```

### Natural Join (same column names)
```sql
SELECT * FROM table1 NATURAL JOIN table2;
```

---

## 10. Indexes

### Create Indexes
```sql
-- Basic index
CREATE INDEX idx_name ON table_name(column_name);

-- Unique index
CREATE UNIQUE INDEX idx_unique ON table_name(column_name);

-- Composite index
CREATE INDEX idx_composite ON table_name(col1, col2);

-- Full-text index
CREATE FULLTEXT INDEX idx_ft ON table_name(text_column);

-- Descending index
CREATE INDEX idx_desc ON table_name(column_name DESC);

-- Index with length (for TEXT/BLOB)
CREATE INDEX idx_prefix ON table_name(text_column(20));

-- On table creation
CREATE TABLE table_name (
    id INT,
    name VARCHAR(100),
    INDEX idx_name (name)
);
```

### Drop Indexes
```sql
DROP INDEX idx_name ON table_name;
ALTER TABLE table_name DROP INDEX idx_name;
```

### Show Indexes
```sql
SHOW INDEX FROM table_name;
SHOW INDEX FROM table_name FROM database_name;
```

### Index Types
```sql
-- BTREE (default)
CREATE INDEX idx_btree ON table_name(col) USING BTREE;

-- HASH (only for MEMORY tables)
CREATE INDEX idx_hash ON table_name(col) USING HASH;
```

### Indexing Best Practices
- Index columns used in WHERE, JOIN, ORDER BY, GROUP BY
- Index foreign key columns
- Consider column cardinality (higher is better for indexes)
- Avoid indexing frequently updated columns
- Use composite indexes for multiple conditions
- Prefix indexes for long text columns
- Don't over-index (slows INSERT/UPDATE/DELETE)

---

## 11. Constraints

### Types of Constraints

```sql
-- PRIMARY KEY
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(255)
);

-- Composite PRIMARY KEY
CREATE TABLE orders_products (
    order_id INT,
    product_id INT,
    PRIMARY KEY (order_id, product_id)
);

-- FOREIGN KEY
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id)
        ON DELETE CASCADE
        ON UPDATE RESTRICT
);

-- Action options:
-- ON DELETE: CASCADE, SET NULL, RESTRICT, NO ACTION, SET DEFAULT
-- ON UPDATE: CASCADE, SET NULL, RESTRICT, NO ACTION, SET DEFAULT

-- UNIQUE
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(255) UNIQUE
);
ALTER TABLE users ADD CONSTRAINT unique_email UNIQUE(email);

-- NOT NULL
CREATE TABLE users (
    name VARCHAR(100) NOT NULL
);

-- CHECK (MariaDB 10.2+)
CREATE TABLE products (
    price DECIMAL(10,2) CHECK (price > 0),
    quantity INT CHECK (quantity >= 0)
);
ALTER TABLE products ADD CONSTRAINT chk_price CHECK (price > 0);

-- DEFAULT
CREATE TABLE users (
    status VARCHAR(20) DEFAULT 'active',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Add/Drop Constraints
```sql
-- Add PRIMARY KEY
ALTER TABLE table_name ADD PRIMARY KEY (column);

-- Drop PRIMARY KEY
ALTER TABLE table_name DROP PRIMARY KEY;

-- Add FOREIGN KEY
ALTER TABLE table_name 
ADD CONSTRAINT fk_name FOREIGN KEY (col) REFERENCES other_table(col);

-- Drop FOREIGN KEY
ALTER TABLE table_name DROP FOREIGN KEY fk_name;

-- Add UNIQUE constraint
ALTER TABLE table_name ADD UNIQUE (column);
ALTER TABLE table_name ADD CONSTRAINT unique_name UNIQUE (column);

-- Drop UNIQUE
ALTER TABLE table_name DROP INDEX unique_name;

-- Add CHECK constraint
ALTER TABLE table_name ADD CONSTRAINT chk_name CHECK (condition);

-- Drop CHECK
ALTER TABLE table_name DROP CONSTRAINT chk_name;
```

---

## 12. Views

### Create View
```sql
-- Basic view
CREATE VIEW view_name AS
SELECT col1, col2 FROM table_name WHERE condition;

-- With check option
CREATE VIEW active_users AS
SELECT * FROM users WHERE status = 'active'
WITH CHECK OPTION;

-- Or replace
CREATE OR REPLACE VIEW view_name AS SELECT ...;

-- Temporary view (session only)
CREATE TEMPORARY VIEW temp_view AS SELECT ...;
```

### Show Views
```sql
SHOW FULL TABLES WHERE table_type = 'VIEW';
SHOW CREATE VIEW view_name;
```

### Alter View
```sql
ALTER VIEW view_name AS SELECT ...;
```

### Drop View
```sql
DROP VIEW view_name;
DROP VIEW IF EXISTS view_name;
```

### Updateable Views
Views are updateable if:
- No aggregate functions (SUM, COUNT, etc.)
- No DISTINCT, GROUP BY, HAVING
- No UNION
- No subqueries in SELECT
- Single table (unless using INSTEAD OF triggers)

```sql
-- This view is updateable
CREATE VIEW simple_view AS 
SELECT id, name, email FROM users WHERE active = 1;

-- Can perform DML
INSERT INTO simple_view (id, name, email) VALUES (1, 'John', 'john@email.com');
UPDATE simple_view SET name = 'Jane' WHERE id = 1;
DELETE FROM simple_view WHERE id = 1;
```

### WITH CHECK OPTION
Prevents updates that would exclude the row from the view.

---

## 13. Stored Procedures & Functions

### Stored Procedures

```sql
-- Create procedure
DELIMITER //
CREATE PROCEDURE GetUsers(IN min_age INT, OUT user_count INT)
BEGIN
    SELECT * FROM users WHERE age >= min_age;
    SET user_count = ROW_COUNT();
END //
DELIMITER ;

-- Call procedure
CALL GetUsers(18, @count);
SELECT @count;

-- Procedure with INOUT parameter
DELIMITER //
CREATE PROCEDURE UpdateCounter(INOUT counter INT, IN increment INT)
BEGIN
    SET counter = counter + increment;
END //
DELIMITER ;

-- Drop procedure
DROP PROCEDURE IF EXISTS GetUsers;
```

### Functions (returns a value)

```sql
DELIMITER //
CREATE FUNCTION CalculateBonus(salary DECIMAL(10,2), rating INT)
RETURNS DECIMAL(10,2)
DETERMINISTIC
READS SQL DATA
BEGIN
    DECLARE bonus DECIMAL(10,2);
    SET bonus = salary * (rating / 100);
    RETURN bonus;
END //
DELIMITER ;

-- Use function
SELECT name, salary, CalculateBonus(salary, 5) as bonus FROM employees;

-- Drop function
DROP FUNCTION IF EXISTS CalculateBonus;
```

### Procedure/Function Characteristics
- `DETERMINISTIC` - Always returns same result for same input
- `NOT DETERMINISTIC` - May return different results
- `READS SQL DATA` - Reads data but doesn't modify
- `MODIFIES SQL DATA` - Inserts/updates/deletes
- `NO SQL` - No SQL statements
- `CONTAINS SQL` - Contains SQL (default)

### Flow Control

```sql
-- IF-THEN-ELSE
IF condition THEN
    statements;
ELSEIF condition2 THEN
    statements;
ELSE
    statements;
END IF;

-- CASE
CASE 
    WHEN condition THEN statements;
    WHEN condition2 THEN statements;
    ELSE statements;
END CASE;

-- LOOP
loop_label: LOOP
    IF condition THEN
        LEAVE loop_label;
    END IF;
    statements;
END LOOP;

-- WHILE
WHILE condition DO
    statements;
END WHILE;

-- REPEAT
REPEAT
    statements;
UNTIL condition END REPEAT;

-- ITERATE (continue)
-- LEAVE (break)
```

### Variables
```sql
-- Declare local variable
DECLARE var_name DATATYPE DEFAULT value;

-- Set variable
SET var_name = value;
SELECT col INTO var_name FROM table WHERE condition;
```

### Cursors
```sql
DECLARE cursor_name CURSOR FOR SELECT ...;
DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;

OPEN cursor_name;
FETCH cursor_name INTO variables;
CLOSE cursor_name;
```

---

## 14. Triggers

### Create Trigger

```sql
DELIMITER //
CREATE TRIGGER trigger_name
{BEFORE | AFTER} {INSERT | UPDATE | DELETE}
ON table_name FOR EACH ROW
BEGIN
    -- Trigger body
    IF NEW.age < 0 THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Age cannot be negative';
    END IF;
END //
DELIMITER ;
```

### Trigger Examples

```sql
-- Before insert (validate data)
CREATE TRIGGER check_age_before_insert
BEFORE INSERT ON users
FOR EACH ROW
BEGIN
    IF NEW.age < 0 OR NEW.age > 150 THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Invalid age';
    END IF;
END;

-- Before update (audit)
CREATE TRIGGER audit_user_update
BEFORE UPDATE ON users
FOR EACH ROW
BEGIN
    INSERT INTO users_audit (user_id, old_name, new_name, changed_at)
    VALUES (OLD.id, OLD.name, NEW.name, NOW());
END;

-- After delete (cleanup)
CREATE TRIGGER cleanup_after_delete
AFTER DELETE ON users
FOR EACH ROW
BEGIN
    DELETE FROM user_sessions WHERE user_id = OLD.id;
    DELETE FROM user_preferences WHERE user_id = OLD.id;
END;
```

### Accessing Values
- `NEW.column` - New value (for INSERT and UPDATE)
- `OLD.column` - Old value (for UPDATE and DELETE)

### Drop Trigger
```sql
DROP TRIGGER trigger_name;
DROP TRIGGER IF EXISTS trigger_name;
```

### Show Triggers
```sql
SHOW TRIGGERS;
SHOW TRIGGERS FROM database_name;
SHOW CREATE TRIGGER trigger_name;
```

---

## 15. Transactions

### Transaction Commands
```sql
START TRANSACTION;
-- or
BEGIN;

-- Perform DML operations
INSERT INTO accounts (id, balance) VALUES (1, 1000);
UPDATE accounts SET balance = balance - 500 WHERE id = 1;

-- Commit (make permanent)
COMMIT;

-- Rollback (undo changes)
ROLLBACK;

-- Savepoint
SAVEPOINT savepoint_name;
ROLLBACK TO SAVEPOINT savepoint_name;
RELEASE SAVEPOINT savepoint_name;
```

### Transaction Example
```sql
START TRANSACTION;

INSERT INTO orders (user_id, total) VALUES (1, 250.00);
SET @order_id = LAST_INSERT_ID();

INSERT INTO order_items (order_id, product_id, quantity) 
VALUES (@order_id, 100, 2);

UPDATE products SET stock = stock - 2 WHERE id = 100;

-- Check if everything succeeded
IF ROW_COUNT() > 0 THEN
    COMMIT;
ELSE
    ROLLBACK;
END IF;
```

### Isolation Levels
```sql
-- Read Uncommitted (dirty reads possible)
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;

-- Read Committed (default in MariaDB)
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- Repeatable Read (standard InnoDB)
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- Serializable (highest isolation)
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

-- View current level
SELECT @@tx_isolation;
```

### Locking
```sql
-- Lock tables manually
LOCK TABLES table_name READ;   -- Shared lock
LOCK TABLES table_name WRITE;  -- Exclusive lock

-- Row-level lock (SELECT ... FOR UPDATE)
START TRANSACTION;
SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;

-- Shared lock (SELECT ... LOCK IN SHARE MODE)
SELECT * FROM accounts LOCK IN SHARE MODE;

-- Unlock tables
UNLOCK TABLES;
```

---

## 16. Backup & Restore

### Backup (mysqldump)

```bash
# Backup single database
mysqldump -u username -p database_name > backup.sql

# Backup multiple databases
mysqldump -u username -p --databases db1 db2 db3 > backup.sql

# Backup all databases
mysqldump -u username -p --all-databases > all_backup.sql

# Backup specific tables
mysqldump -u username -p database_name table1 table2 > backup.sql

# Backup with structure only (no data)
mysqldump -u username -p --no-data database_name > structure.sql

# Backup with data only (no structure)
mysqldump -u username -p --no-create-info database_name > data.sql

# Backup with compression
mysqldump -u username -p database_name | gzip > backup.sql.gz

# Backup with timestamp
mysqldump -u username -p database_name > backup_$(date +%Y%m%d_%H%M%S).sql

# Backup with options
mysqldump -u username -p \
  --single-transaction \    # Consistent backup for InnoDB
  --routines \              # Include stored procedures
  --triggers \              # Include triggers
  --events \                # Include events
  database_name > backup.sql
```

### Restore

```bash
# Restore database
mysql -u username -p database_name < backup.sql

# Restore from compressed backup
gunzip < backup.sql.gz | mysql -u username -p database_name

# Restore all databases
mysql -u username -p < all_backup.sql

# Restore with progress
pv backup.sql | mysql -u username -p database_name
```

### MariaDB Backup (mariadb-backup)
```bash
# Full backup
mariadb-backup --backup --target-dir=/backup/full \
  --user=root --password=password

# Prepare backup
mariadb-backup --prepare --target-dir=/backup/full

# Restore
mariadb-backup --copy-back --target-dir=/backup/full
```

### Selective Backup with Queries
```sql
-- Export to CSV
SELECT * INTO OUTFILE '/tmp/backup.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
FROM table_name;

-- Generate INSERT statements
SELECT CONCAT('INSERT INTO users VALUES(', id, ', ''', name, ''');') 
FROM users
INTO OUTFILE '/tmp/inserts.sql';
```

---

## 17. Performance Tuning

### Query Analysis

```sql
-- EXPLAIN (query execution plan)
EXPLAIN SELECT * FROM users WHERE age > 18;
EXPLAIN EXTENDED SELECT ...;
EXPLAIN FORMAT=JSON SELECT ...;

-- ANALYZE (MariaDB 10.1+)
ANALYZE SELECT * FROM users WHERE age > 18;

-- Show warnings after EXPLAIN
SHOW WARNINGS;

-- Profile query (deprecated but still works)
SET profiling = 1;
SELECT ...;
SHOW PROFILES;
SHOW PROFILE FOR QUERY 1;
```

### System Variables (my.cnf / my.ini)

```ini
[mysqld]
# Buffer sizes
innodb_buffer_pool_size = 1G          # InnoDB cache (70-80% of RAM for dedicated)
innodb_log_file_size = 256M           # Transaction log size
innodb_log_buffer_size = 16M          # Log buffer
query_cache_size = 0                  # Disabled in MariaDB 10.1+ (removed in 10.5)
key_buffer_size = 256M                # MyISAM index cache
tmp_table_size = 64M                  # Temp table size
max_heap_table_size = 64M             # MEMORY table size

# Connection settings
max_connections = 200                 # Max concurrent connections
thread_cache_size = 50                # Thread cache
table_open_cache = 2000               # Table cache

# Timeouts
wait_timeout = 28800                  # Connection idle timeout
interactive_timeout = 28800
connect_timeout = 10
```

### View/Change System Variables
```sql
-- Show all variables
SHOW VARIABLES;
SHOW VARIABLES LIKE '%buffer%';

-- Show status
SHOW STATUS;
SHOW STATUS LIKE '%conn%';
SHOW ENGINE INNODB STATUS\G

-- Set variable (session)
SET SESSION sort_buffer_size = 2M;

-- Set variable (global - needs SUPER privilege)
SET GLOBAL max_connections = 300;
```

### Slow Query Log
```sql
-- Enable slow query log
SET GLOBAL slow_query_log = ON;
SET GLOBAL long_query_time = 2;  -- Log queries taking >2 seconds
SET GLOBAL slow_query_log_file = '/var/log/mysql/slow.log';

-- Log queries without indexes
SET GLOBAL log_queries_not_using_indexes = ON;
```

### Optimization Tips

1. **SELECT optimization**
   - Only select needed columns, avoid `SELECT *`
   - Use `LIMIT` for pagination
   - Avoid `SELECT DISTINCT` when not needed

2. **WHERE clause optimization**
   - Index columns used in WHERE
   - Avoid functions on indexed columns: `WHERE YEAR(date) = 2023` → `WHERE date BETWEEN '2023-01-01' AND '2023-12-31'`
   - Use `EXISTS` instead of `IN` for subqueries when possible

3. **JOIN optimization**
   - Join smallest table first
   - Ensure join columns are indexed
   - Use `EXPLAIN` to verify join order

4. **INSERT optimization**
   - Use bulk inserts: `INSERT INTO ... VALUES (1),(2),(3)`
   - Use `LOAD DATA INFILE` for large imports
   - Temporarily disable indexes: `ALTER TABLE ... DISABLE KEYS`

5. **Schema optimization**
   - Use appropriate data types (smaller is faster)
   - Normalize data (but not too much)
   - Denormalize for heavy read loads
   - Use partitions for very large tables

### Partitioning
```sql
-- Range partition
CREATE TABLE sales (
    id INT,
    sale_date DATE,
    amount DECIMAL(10,2)
) PARTITION BY RANGE (YEAR(sale_date)) (
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);

-- List partition
PARTITION BY LIST (region) (
    PARTITION p_north VALUES IN ('NY', 'NJ', 'CT'),
    PARTITION p_south VALUES IN ('FL', 'GA', 'AL')
);

-- Hash partition
PARTITION BY HASH (id) PARTITIONS 4;
```

---

## 18. Monitoring & Diagnostics

### Process List
```sql
-- Show running processes
SHOW PROCESSLIST;
SHOW FULL PROCESSLIST;

-- Kill process
KILL process_id;
KILL CONNECTION process_id;
KILL QUERY process_id;  -- Kill only the query, not connection
```

### Database Status
```sql
-- Server status
STATUS;
\s

-- Database size
SELECT 
    table_schema AS 'Database',
    ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS 'Size (MB)'
FROM information_schema.tables
GROUP BY table_schema;

-- Table size
SELECT 
    table_name,
    ROUND(((data_length + index_length) / 1024 / 1024), 2) AS 'Size (MB)'
FROM information_schema.tables
WHERE table_schema = 'database_name'
ORDER BY (data_length + index_length) DESC;

-- Storage engines
SHOW ENGINES;

-- Table status
SHOW TABLE STATUS FROM database_name;
SHOW TABLE STATUS LIKE 'users'\G
```

### Performance Schema (MariaDB 10.5+)
```sql
-- Enable Performance Schema
SET GLOBAL performance_schema = ON;

-- Check waits
SELECT * FROM performance_schema.events_waits_summary_global_by_event_name;
```

### Useful Information Schema Queries
```sql
-- All tables with their columns
SELECT * FROM information_schema.columns WHERE table_schema = 'database_name';

-- All indexes
SELECT * FROM information_schema.statistics WHERE table_schema = 'database_name';

-- Foreign keys
SELECT * FROM information_schema.key_column_usage WHERE referenced_table_name IS NOT NULL;

-- Table constraints
SELECT * FROM information_schema.table_constraints WHERE table_schema = 'database_name';
```

### Log Files
```sql
-- Error log location
SHOW VARIABLES LIKE 'log_error';

-- General query log
SET GLOBAL general_log = ON;
SET GLOBAL general_log_file = '/var/log/mysql/mariadb.log';
```

---

## 19. Full-Text Search

### Create Full-Text Index
```sql
-- On table creation
CREATE TABLE articles (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(200),
    content TEXT,
    FULLTEXT(title, content)
);

-- Add to existing table
ALTER TABLE articles ADD FULLTEXT INDEX ft_index (title, content);
CREATE FULLTEXT INDEX ft_index ON articles(title, content);
```

### Natural Language Search
```sql
-- Basic full-text search
SELECT * FROM articles 
WHERE MATCH(title, content) AGAINST('database optimization');

-- With relevance score
SELECT *, MATCH(title, content) AGAINST('database optimization') AS relevance
FROM articles 
WHERE MATCH(title, content) AGAINST('database optimization')
ORDER BY relevance DESC;
```

### Boolean Mode Search
```sql
-- Operators: + (must have), - (must not), > (increase weight), < (decrease weight), ~ (negate), * (wildcard), " " (phrase)
SELECT * FROM articles 
WHERE MATCH(title, content) AGAINST('+database -mysql' IN BOOLEAN MODE);

SELECT * FROM articles 
WHERE MATCH(title, content) AGAINST('>database <sql' IN BOOLEAN MODE);

SELECT * FROM articles 
WHERE MATCH(title, content) AGAINST('"database optimization"' IN BOOLEAN MODE);

SELECT * FROM articles 
WHERE MATCH(title, content) AGAINST('optimiz*' IN BOOLEAN MODE);  -- Prefix search
```

### Query Expansion
```sql
-- Adds relevant words from found documents
SELECT * FROM articles 
WHERE MATCH(title, content) AGAINST('database' WITH QUERY EXPANSION);
```

### Full-Text Settings
```sql
-- Minimum word length (default: 4)
SHOW VARIABLES LIKE 'ft_min_word_len';

-- Stopwords (common words ignored)
SHOW VARIABLES LIKE 'ft_stopword_file';
```

---

## 20. JSON Functions

### Create JSON Data
```sql
-- JSON type column
CREATE TABLE users (
    id INT PRIMARY KEY,
    preferences JSON
);

-- Insert JSON
INSERT INTO users VALUES (1, '{"theme": "dark", "notifications": true}');
INSERT INTO users VALUES (2, JSON_OBJECT('theme', 'light', 'lang', 'en'));

-- JSON_ARRAY
SELECT JSON_ARRAY(1, 2, 3, 'text');  -- [1, 2, 3, "text"]
```

### Extract JSON Data
```sql
-- JSON_EXTRACT
SELECT JSON_EXTRACT(preferences, '$.theme') FROM users;
SELECT preferences->'$.theme' FROM users;  -- Shorthand

-- JSON_UNQUOTE (remove quotes)
SELECT JSON_UNQUOTE(JSON_EXTRACT(preferences, '$.theme')) FROM users;
SELECT preferences->>'$.theme' FROM users;  -- Shorthand without quotes

-- Extract array elements
SELECT JSON_EXTRACT(preferences, '$[0]') FROM table;
```

### JSON Modification
```sql
-- JSON_SET (add or update)
UPDATE users SET preferences = JSON_SET(preferences, '$.font_size', 14) WHERE id = 1;

-- JSON_INSERT (only add if not exists)
UPDATE users SET preferences = JSON_INSERT(preferences, '$.language', 'en') WHERE id = 1;

-- JSON_REPLACE (only update if exists)
UPDATE users SET preferences = JSON_REPLACE(preferences, '$.theme', 'light') WHERE id = 1;

-- JSON_REMOVE
UPDATE users SET preferences = JSON_REMOVE(preferences, '$.notifications') WHERE id = 1;
```

### JSON Functions
```sql
-- JSON_TYPE (data type of JSON value)
SELECT JSON_TYPE(JSON_EXTRACT(preferences, '$.theme')) FROM users;

-- JSON_LENGTH
SELECT JSON_LENGTH(preferences) FROM users;

-- JSON_KEYS (get keys)
SELECT JSON_KEYS(preferences) FROM users;

-- JSON_CONTAINS
SELECT * FROM users WHERE JSON_CONTAINS(preferences, '"dark"', '$.theme');

-- JSON_OVERLAPS (any value matches)
SELECT * FROM users WHERE JSON_OVERLAPS(preferences, '["dark", "light"]');

-- JSON_DEPTH (nesting depth)
SELECT JSON_DEPTH(preferences) FROM users;

-- JSON_VALID (check if valid JSON)
SELECT JSON_VALID('{"key": "value"}');  -- 1
```

### Create indexes on JSON
```sql
-- Generated column for JSON value
ALTER TABLE users ADD COLUMN theme VARCHAR(50) 
AS (JSON_UNQUOTE(preferences->'$.theme')) STORED;

CREATE INDEX idx_theme ON users(theme);
```

---

## 21. Common Functions

### String Functions
```sql
CONCAT(str1, str2)                    -- Concatenate
CONCAT_WS(sep, str1, str2)            -- Concatenate with separator
LENGTH(str)                           -- Length in bytes
CHAR_LENGTH(str)                      -- Length in characters
UPPER(str) / LOWER(str)              -- Case conversion
TRIM(str) / LTRIM(str) / RTRIM(str)  -- Remove whitespace
SUBSTRING(str, pos, len)              -- Extract substring
LEFT(str, len) / RIGHT(str, len)     -- Extract from left/right
REPLACE(str, from, to)                -- Replace substring
REVERSE(str)                          -- Reverse string
LOCATE(substr, str)                   -- Find position
INSTR(str, substr)                    -- Find position
LPAD(str, len, pad) / RPAD(str, len, pad)  -- Pad string
REPEAT(str, count)                    -- Repeat string
REVERSE(str)                          -- Reverse
FORMAT(num, decimals)                 -- Format number with commas
```

### Numeric Functions
```sql
ABS(n)                                -- Absolute value
CEIL(n) / FLOOR(n)                    -- Round up/down
ROUND(n, decimals)                    -- Round
TRUNCATE(n, decimals)                 -- Truncate
POW(base, exp) / POWER(base, exp)    -- Exponentiation
SQRT(n)                               -- Square root
MOD(n, m)                             -- Modulo (remainder)
RAND()                                -- Random number 0-1
RAND(seed)                            -- Seed for repeatable random
GREATEST(a,b,c)                       -- Largest value
LEAST(a,b,c)                          -- Smallest value
SIGN(n)                               -- Sign (-1, 0, 1)
PI()                                  -- Pi value
```

### Date/Time Functions
```sql
NOW()                                 -- Current date and time
CURDATE() / CURRENT_DATE             -- Current date
CURTIME() / CURRENT_TIME             -- Current time
UTC_TIMESTAMP()                       -- UTC timestamp

DATE(date)                            -- Extract date part
TIME(datetime)                        -- Extract time part
YEAR(date) / MONTH(date) / DAY(date) -- Extract parts
HOUR(time) / MINUTE(time) / SECOND(time)

DAYNAME(date)                         -- Monday, Tuesday...
MONTHNAME(date)                       -- January, February...
DAYOFWEEK(date)                       -- 1=Sunday, 7=Saturday
DAYOFYEAR(date)                       -- 1-366
WEEK(date)                            -- Week number

DATE_ADD(date, INTERVAL 1 DAY)       -- Add interval
DATE_SUB(date, INTERVAL 1 MONTH)
ADDDATE(date, INTERVAL 1 YEAR)
SUBDATE(date, INTERVAL 1 HOUR)

DATEDIFF(date1, date2)               -- Days difference
TIMEDIFF(time1, time2)               -- Time difference
TIMESTAMPDIFF(unit, start, end)      -- Difference in specified unit
TIMESTAMPDIFF(MINUTE, start, end)

DATE_FORMAT(date, format)            -- Format date
STR_TO_DATE(str, format)             -- Parse date string

-- Format specifiers: %Y (4-digit year), %y (2-digit), %m (month), %d (day)
-- %H (24-hour), %h (12-hour), %i (minutes), %s (seconds), %p (AM/PM)

LAST_DAY(date)                        -- Last day of month
UNIX_TIMESTAMP()                      -- Unix timestamp
FROM_UNIXTIME(timestamp)              -- Convert unix to datetime
```

### Aggregate Functions
```sql
COUNT(*) / COUNT(col)                -- Count rows
SUM(col)                              -- Sum
AVG(col)                              -- Average
MIN(col) / MAX(col)                  -- Min/Max
GROUP_CONCAT(col SEPARATOR ',')       -- Concatenate values
STDDEV(col) / STDDEV_POP(col)        -- Standard deviation
VARIANCE(col) / VAR_POP(col)         -- Variance
BIT_AND(col) / BIT_OR(col)           -- Bitwise operations
```

### Conversion Functions
```sql
CAST(expr AS type)                    -- Convert type
CONVERT(expr, type)                  -- Convert type
-- Types: CHAR, DATE, DATETIME, TIME, DECIMAL, SIGNED, UNSIGNED, BINARY

CAST('123' AS SIGNED)                -- String to integer
CAST('2023-01-01' AS DATE)           -- String to date

CONVERT(123, CHAR)                    -- Number to string
```

### Conditional Functions
```sql
IF(condition, true_value, false_value)
IFNULL(expr, default)                -- Return default if expr is NULL
NULLIF(expr1, expr2)                 -- NULL if equal, else expr1
COALESCE(val1, val2, ...)            -- First non-NULL value

CASE 
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ELSE default
END
```

### Other Useful Functions
```sql
-- UUID generation
UUID()                                -- Generate UUID v1
UUID_SHORT()                          -- Generate 64-bit UUID

-- User/connection info
USER() / CURRENT_USER()              -- Current user
DATABASE()                            -- Current database
CONNECTION_ID()                       -- Connection ID

-- Last insert ID
LAST_INSERT_ID()                      -- Last auto-increment value

-- Encryption
MD5(str)                              -- MD5 hash
SHA1(str) / SHA2(str, bits)          -- SHA hashes
PASSWORD(str)                         -- Password hash (deprecated)

-- System information
VERSION()                             -- MariaDB version
DATABASE()                            -- Current database
SLEEP(seconds)                        -- Sleep (returns 0)
BENCHMARK(count, expr)               -- Benchmark expression
```

---

## 22. Import/Export Data

### Export to CSV
```sql
-- Using SELECT INTO OUTFILE
SELECT * INTO OUTFILE '/tmp/data.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
FROM table_name;

-- With specific columns
SELECT id, name, email 
INTO OUTFILE '/tmp/users.csv'
FIELDS TERMINATED BY ','
OPTIONALLY ENCLOSED BY '"'
ESCAPED BY '\\'
LINES TERMINATED BY '\n'
FROM users WHERE active = 1;

-- Column headers (workaround)
SELECT 'id', 'name', 'email'
UNION ALL
SELECT id, name, email
FROM users
INTO OUTFILE '/tmp/users_with_headers.csv'
FIELDS TERMINATED BY ','
ENCLOSED BY '"';
```

### Import from CSV
```sql
-- LOAD DATA INFILE
LOAD DATA INFILE '/tmp/data.csv'
INTO TABLE table_name
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;  -- Skip header row

-- With specific columns
LOAD DATA INFILE '/tmp/data.csv'
INTO TABLE users (id, name, email)
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n';

-- Set default values for missing columns
LOAD DATA INFILE '/tmp/data.csv'
INTO TABLE users
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
SET created_at = NOW();
```

### Export to other formats
```bash
# XML
mysqldump -u user -p --xml database_name table_name > data.xml

# Tab-separated
mysql -u user -p -e "SELECT * FROM table" database > data.tsv

# HTML
mysql -u user -p -H -e "SELECT * FROM table" database > data.html
```

### Using mysql command-line for export
```bash
# CSV with column headers
mysql -u user -p database -e "SELECT * FROM table" -B | sed 's/\t/,/g' > data.csv

# Without headers
mysql -u user -p database -N -e "SELECT * FROM table" -B | sed 's/\t/,/g' > data.csv
```

---

## 23. Security Best Practices

### User Management Security
```sql
-- Remove anonymous users
DELETE FROM mysql.user WHERE User = '';
FLUSH PRIVILEGES;

-- Remove test database
DROP DATABASE IF EXISTS test;
DELETE FROM mysql.db WHERE Db = 'test' OR Db = 'test\\_%';

-- Strong password policies
INSTALL SONAME 'simple_password_check';
SET GLOBAL simple_password_check_minimal_length = 8;
SET GLOBAL simple_password_check_digits = 1;
SET GLOBAL simple_password_check_letters = 1;
```

### SSL/TLS Configuration
```ini
[mysqld]
ssl-ca = /etc/mysql/ca-cert.pem
ssl-cert = /etc/mysql/server-cert.pem
ssl-key = /etc/mysql/server-key.pem
require_secure_transport = ON
```

```sql
-- Require SSL for user
GRANT ALL ON *.* TO 'user'@'%' REQUIRE SSL;

-- Check SSL status
SHOW VARIABLES LIKE 'have_ssl';
SHOW STATUS LIKE 'Ssl_cipher';
```

### SQL Injection Prevention
- Use prepared statements
```sql
-- Prepared statement
PREPARE stmt FROM 'SELECT * FROM users WHERE id = ?';
SET @id = 1;
EXECUTE stmt USING @id;
DEALLOCATE PREPARE stmt;
```

- Use parameterized queries in application code
- Validate input data types
- Escape special characters with `mysql_real_escape_string()`

### Audit Logging
```sql
-- Install audit plugin
INSTALL SONAME 'server_audit';

-- Configure audit logging
SET GLOBAL server_audit_logging = ON;
SET GLOBAL server_audit_events = 'CONNECT,QUERY,TABLE';
SET GLOBAL server_audit_file_path = '/var/log/mysql/audit.log';
```

### Backup Security
- Encrypt backups
```bash
mysqldump -u user -p database | gpg -c > backup.sql.gpg
```
- Use secure backup storage
- Regular restore tests
- Backup encryption at rest

### General Security Tips
1. Run MariaDB as non-root user (typically `mysql`)
2. Bind to localhost only: `bind-address = 127.0.0.1` in my.cnf
3. Change default port from 3306
4. Disable `LOAD DATA LOCAL INFILE`: `local-infile=0`
5. Remove `FILE` privilege from non-admin users
6. Use `--skip-show-database` to restrict database listing
7. Enable `log_warnings` to detect attacks
8. Regularly update MariaDB version
9. Monitor failed login attempts
10. Use network firewall rules
11. Implement connection rate limiting
12. Regular security audits

---

## Quick Reference

### Most Common Commands
```sql
-- Connect
mysql -u root -p

-- Show everything
SHOW DATABASES;
SHOW TABLES;
SHOW COLUMNS FROM table;
SHOW INDEX FROM table;

-- Database
CREATE DATABASE db;
USE db;
DROP DATABASE db;

-- Table
CREATE TABLE t (id INT, name VARCHAR(50));
DROP TABLE t;
TRUNCATE TABLE t;
ALTER TABLE t ADD COLUMN email VARCHAR(255);

-- Data
INSERT INTO t VALUES (1, 'John');
SELECT * FROM t WHERE id = 1;
UPDATE t SET name = 'Jane' WHERE id = 1;
DELETE FROM t WHERE id = 1;

-- User
CREATE USER 'user'@'localhost' IDENTIFIED BY 'pass';
GRANT ALL ON *.* TO 'user'@'localhost';
FLUSH PRIVILEGES;
DROP USER 'user'@'localhost';

-- Backup/Restore
mysqldump db > backup.sql
mysql db < backup.sql
```

### Useful System Commands
```bash
# Service management
sudo systemctl status mariadb
sudo systemctl restart mariadb

# Log viewing
sudo tail -f /var/log/mysql/error.log
sudo tail -f /var/log/mysql/slow.log

# Configuration
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf

# Find version
mysql --version
```

---
