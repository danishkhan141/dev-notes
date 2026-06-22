Below is a **professional MySQL interview preparation guide** for a **Java backend developer**. I am assuming **MySQL 8.x with InnoDB**, because InnoDB is the default MySQL storage engine in MySQL 8.4 and is the most common engine used in production systems. InnoDB supports transactions, row-level locking, indexes, foreign keys, and consistent reads. ([MySQL][1])

# Top 50 MySQL Interview Questions and Answers

## 1. What is MySQL?

**MySQL** is an open-source relational database management system. It stores data in tables using rows and columns and supports SQL for querying and managing data.

Example:

```sql
CREATE TABLE employee (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    salary DECIMAL(10,2)
);
```

In Java projects, MySQL is commonly used with **JDBC**, **Spring JDBC**, **Hibernate**, **JPA**, and **Spring Data JPA**.

---

## 2. What is the difference between SQL and MySQL?

**SQL** is a language used to interact with relational databases.

**MySQL** is a database product that uses SQL.

Example:

```sql
SELECT * FROM employee;
```

This is SQL. When executed on a MySQL database, MySQL processes it.

---

## 3. What are the main MySQL data types?

Important MySQL data types are:

| Category  | Examples                                      |
| --------- | --------------------------------------------- |
| Numeric   | `INT`, `BIGINT`, `DECIMAL`, `FLOAT`, `DOUBLE` |
| String    | `VARCHAR`, `CHAR`, `TEXT`                     |
| Date/Time | `DATE`, `TIME`, `DATETIME`, `TIMESTAMP`       |
| Boolean   | `BOOLEAN` / `TINYINT(1)`                      |
| JSON      | `JSON`                                        |

Example:

```sql
CREATE TABLE orders (
    id BIGINT PRIMARY KEY,
    amount DECIMAL(10,2),
    order_date DATETIME,
    status VARCHAR(30)
);
```

For money-related values, prefer `DECIMAL`, not `FLOAT`, because `DECIMAL` gives exact precision.

---

## 4. What is the difference between `CHAR` and `VARCHAR`?

`CHAR` has fixed length.
`VARCHAR` has variable length.

Example:

```sql
code CHAR(5)
name VARCHAR(100)
```

If value is always fixed, such as country code or status code, `CHAR` can be used. For names, emails, addresses, etc., `VARCHAR` is better.

---

## 5. What is the difference between `DATETIME` and `TIMESTAMP`?

`DATETIME` stores date and time as given.

`TIMESTAMP` is time-zone aware and is generally stored in UTC internally, then converted based on session time zone.

Example:

```sql
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
```

For audit columns like `created_at` and `updated_at`, `TIMESTAMP` is commonly used.

---

## 6. What is a primary key?

A **primary key** uniquely identifies each row in a table.

Example:

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    email VARCHAR(100)
);
```

A primary key should be:

* Unique
* Not null
* Stable
* Indexed automatically

In InnoDB, the primary key is usually used as the clustered index, meaning row data is stored around that key structure. ([MySQL][2])

---

## 7. What is a foreign key?

A **foreign key** creates a relationship between two tables.

Example:

```sql
CREATE TABLE orders (
    id BIGINT PRIMARY KEY,
    user_id BIGINT,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

This means `orders.user_id` must exist in `users.id`.

Foreign keys help maintain **referential integrity**.

---

## 8. What is the difference between primary key and unique key?

| Primary Key                       | Unique Key                              |
| --------------------------------- | --------------------------------------- |
| Only one primary key per table    | Multiple unique keys allowed            |
| Cannot contain null               | Can allow null depending on DB behavior |
| Identifies row                    | Ensures uniqueness                      |
| Usually clustered index in InnoDB | Secondary index                         |

Example:

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    email VARCHAR(100) UNIQUE
);
```

Here, `id` identifies the row, while `email` must be unique.

---

## 9. What is `AUTO_INCREMENT`?

`AUTO_INCREMENT` automatically generates numeric values for a column.

Example:

```sql
CREATE TABLE users (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100)
);
```

When you insert a row:

```sql
INSERT INTO users(name) VALUES ('Danish');
```

MySQL automatically generates the `id`.

---

## 10. What is normalization?

**Normalization** is the process of designing tables to reduce duplicate data and improve data consistency.

Example of bad design:

```text
order_id | user_name | user_email | product_name
```

Better design:

```text
users
orders
products
order_items
```

Normalization helps avoid update anomalies, insert anomalies, and delete anomalies.

---

## 11. What is denormalization?

**Denormalization** means intentionally storing duplicate data to improve read performance.

Example:

```sql
orders table:
id, user_id, user_name, total_amount
```

Here, `user_name` may already exist in the `users` table, but we store it in `orders` to avoid joins in reporting queries.

Use denormalization carefully because it increases data consistency responsibility.

---

## 12. What are DDL, DML, DCL, and TCL?

| Type | Meaning                      | Examples                               |
| ---- | ---------------------------- | -------------------------------------- |
| DDL  | Data Definition Language     | `CREATE`, `ALTER`, `DROP`              |
| DML  | Data Manipulation Language   | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| DCL  | Data Control Language        | `GRANT`, `REVOKE`                      |
| TCL  | Transaction Control Language | `COMMIT`, `ROLLBACK`                   |

Example:

```sql
CREATE TABLE employee (...); -- DDL
INSERT INTO employee VALUES (...); -- DML
COMMIT; -- TCL
```

---

## 13. What is the difference between `DELETE`, `TRUNCATE`, and `DROP`?

| Command    | Meaning                            |
| ---------- | ---------------------------------- |
| `DELETE`   | Deletes selected rows              |
| `TRUNCATE` | Removes all rows quickly           |
| `DROP`     | Deletes the table structure itself |

Example:

```sql
DELETE FROM users WHERE id = 10;

TRUNCATE TABLE users;

DROP TABLE users;
```

Use `DELETE` when you need conditions. Use `TRUNCATE` when you want to empty the table. Use `DROP` when the table is no longer needed.

---

## 14. What is the difference between `WHERE` and `HAVING`?

`WHERE` filters rows before grouping.

`HAVING` filters groups after `GROUP BY`.

Example:

```sql
SELECT department_id, COUNT(*) 
FROM employee
WHERE status = 'ACTIVE'
GROUP BY department_id
HAVING COUNT(*) > 5;
```

Here:

* `WHERE` filters active employees first.
* `HAVING` filters departments having more than 5 active employees.

---

## 15. What is the difference between `GROUP BY` and `ORDER BY`?

`GROUP BY` groups rows.

`ORDER BY` sorts rows.

Example:

```sql
SELECT department_id, COUNT(*)
FROM employee
GROUP BY department_id
ORDER BY COUNT(*) DESC;
```

This groups employees by department and sorts departments by employee count.

---

## 16. What are aggregate functions?

Aggregate functions perform calculations on multiple rows.

Common examples:

```sql
COUNT()
SUM()
AVG()
MIN()
MAX()
```

Example:

```sql
SELECT department_id, AVG(salary)
FROM employee
GROUP BY department_id;
```

This gives average salary per department.

---

## 17. What is a join?

A **join** combines data from multiple tables.

Example:

```sql
SELECT u.name, o.id, o.total_amount
FROM users u
JOIN orders o ON u.id = o.user_id;
```

This returns users with their orders.

---

## 18. Explain different types of joins.

| Join Type    | Meaning                                                     |
| ------------ | ----------------------------------------------------------- |
| `INNER JOIN` | Matching records from both tables                           |
| `LEFT JOIN`  | All records from left table and matching records from right |
| `RIGHT JOIN` | All records from right table and matching records from left |
| `CROSS JOIN` | Cartesian product                                           |

Example:

```sql
SELECT u.name, o.id
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

This returns all users, even those who have no orders.

---

## 19. What is the difference between `INNER JOIN` and `LEFT JOIN`?

`INNER JOIN` returns only matching rows.

`LEFT JOIN` returns all rows from the left table and matching rows from the right table.

Example:

```sql
-- Only users having orders
SELECT *
FROM users u
INNER JOIN orders o ON u.id = o.user_id;

-- All users, even without orders
SELECT *
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

---

## 20. What is a self join?

A **self join** means joining a table with itself.

Example: employee-manager relationship.

```sql
SELECT e.name AS employee, m.name AS manager
FROM employee e
LEFT JOIN employee m ON e.manager_id = m.id;
```

Here, the `employee` table is used twice.

---

## 21. What is a subquery?

A **subquery** is a query inside another query.

Example:

```sql
SELECT name, salary
FROM employee
WHERE salary > (
    SELECT AVG(salary)
    FROM employee
);
```

This returns employees earning more than the average salary.

---

## 22. What is the difference between subquery and join?

A **join** combines tables directly.

A **subquery** calculates intermediate data used by the outer query.

Example using subquery:

```sql
SELECT *
FROM employee
WHERE department_id IN (
    SELECT id FROM department WHERE location = 'Pune'
);
```

Example using join:

```sql
SELECT e.*
FROM employee e
JOIN department d ON e.department_id = d.id
WHERE d.location = 'Pune';
```

In many cases, joins are easier to optimize and read, but subqueries are useful when the logic is naturally step-by-step.

---

## 23. What is an index?

An **index** improves data lookup speed.

Without index, MySQL may scan the entire table. With index, it can directly search relevant records.

Example:

```sql
CREATE INDEX idx_employee_email ON employee(email);
```

Query:

```sql
SELECT * FROM employee WHERE email = 'abc@gmail.com';
```

MySQL commonly uses B-tree indexes for fast lookup, range search, `BETWEEN`, `IN`, and comparison operators. ([MySQL][3])

---

## 24. What are the disadvantages of indexes?

Indexes improve read performance but have costs:

* Slower `INSERT`
* Slower `UPDATE`
* Slower `DELETE`
* Extra storage
* Too many indexes confuse optimization and increase maintenance

Example: If you index every column unnecessarily, write operations become slower.

Good rule: create indexes based on real query patterns, especially columns used in:

```sql
WHERE
JOIN
ORDER BY
GROUP BY
```

---

## 25. What is a composite index?

A **composite index** is an index on multiple columns.

Example:

```sql
CREATE INDEX idx_user_status_created 
ON orders(user_id, status, created_at);
```

Useful query:

```sql
SELECT *
FROM orders
WHERE user_id = 10
AND status = 'PAID'
ORDER BY created_at DESC;
```

Column order matters. The leftmost column should usually be the most commonly filtered column.

---

## 26. What is the leftmost prefix rule?

For a composite index:

```sql
CREATE INDEX idx_orders_user_status_date
ON orders(user_id, status, created_at);
```

This index can be useful for:

```sql
WHERE user_id = 10
```

```sql
WHERE user_id = 10 AND status = 'PAID'
```

```sql
WHERE user_id = 10 AND status = 'PAID' AND created_at > '2026-01-01'
```

But it may not be fully useful for:

```sql
WHERE status = 'PAID'
```

because `user_id` is skipped.

---

## 27. What is a clustered index in MySQL?

In InnoDB, the clustered index stores the actual row data. Usually, the primary key acts as the clustered index. ([MySQL][2])

Example:

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    name VARCHAR(100)
);
```

Here, `id` is the clustered index.

Interview point: choosing a good primary key matters because secondary indexes internally refer to the primary key.

---

## 28. What is a secondary index?

A **secondary index** is any index other than the clustered index.

Example:

```sql
CREATE INDEX idx_users_email ON users(email);
```

If you search by email:

```sql
SELECT * FROM users WHERE email = 'abc@gmail.com';
```

MySQL first uses the secondary index, then uses the primary key reference to fetch the full row.

---

## 29. What is a covering index?

A **covering index** means the index contains all columns required by the query.

Example:

```sql
CREATE INDEX idx_order_status_amount 
ON orders(status, total_amount);
```

Query:

```sql
SELECT status, total_amount
FROM orders
WHERE status = 'PAID';
```

Here, MySQL may answer using only the index without reading the full table row.

This improves performance.

---

## 30. What is `EXPLAIN` in MySQL?

`EXPLAIN` shows how MySQL plans to execute a query.

Example:

```sql
EXPLAIN
SELECT *
FROM orders
WHERE user_id = 10;
```

Important columns:

| Column          | Meaning                |
| --------------- | ---------------------- |
| `type`          | Join/access type       |
| `possible_keys` | Indexes MySQL can use  |
| `key`           | Index actually used    |
| `rows`          | Estimated rows scanned |
| `Extra`         | Additional info        |

For performance tuning, `EXPLAIN` is one of the most important tools.

---

## 31. How do you optimize a slow query?

Professional answer:

1. Check query using `EXPLAIN`.
2. Verify indexes.
3. Avoid `SELECT *`.
4. Avoid functions on indexed columns.
5. Use pagination carefully.
6. Optimize joins.
7. Avoid unnecessary subqueries.
8. Check data volume and cardinality.
9. Use covering indexes where useful.
10. Monitor slow query log.

Bad query:

```sql
SELECT * FROM orders WHERE YEAR(created_at) = 2026;
```

Better query:

```sql
SELECT id, total_amount
FROM orders
WHERE created_at >= '2026-01-01'
AND created_at < '2027-01-01';
```

Why better? The second query can use an index on `created_at`.

---

## 32. Why should we avoid `SELECT *`?

`SELECT *` fetches all columns, even unnecessary ones.

Problems:

* More network traffic
* More memory usage
* May prevent covering index usage
* Breaks code if schema changes unexpectedly

Better:

```sql
SELECT id, name, email
FROM users
WHERE id = 10;
```

---

## 33. What is a transaction?

A transaction is a group of SQL operations executed as one unit.

Example:

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 1000 WHERE id = 1;
UPDATE accounts SET balance = balance + 1000 WHERE id = 2;

COMMIT;
```

If something fails:

```sql
ROLLBACK;
```

Transactions follow ACID properties.

---

## 34. What are ACID properties?

| Property    | Meaning                                   |
| ----------- | ----------------------------------------- |
| Atomicity   | All operations succeed or all fail        |
| Consistency | Database remains valid                    |
| Isolation   | Transactions do not interfere incorrectly |
| Durability  | Once committed, data is saved             |

Example: Money transfer.

If debit succeeds but credit fails, transaction should rollback.

---

## 35. What are MySQL isolation levels?

InnoDB supports four standard transaction isolation levels: `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ`, and `SERIALIZABLE`. ([MySQL][4])

| Isolation Level    | Meaning                                        |
| ------------------ | ---------------------------------------------- |
| `READ UNCOMMITTED` | Can read uncommitted data                      |
| `READ COMMITTED`   | Reads only committed data                      |
| `REPEATABLE READ`  | Same row gives same result in same transaction |
| `SERIALIZABLE`     | Highest isolation, lowest concurrency          |

MySQL InnoDB default is commonly `REPEATABLE READ`.

Example:

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
START TRANSACTION;
```

---

## 36. What are dirty read, non-repeatable read, and phantom read?

| Problem             | Meaning                                                     |
| ------------------- | ----------------------------------------------------------- |
| Dirty read          | Reading uncommitted data                                    |
| Non-repeatable read | Same row gives different result in same transaction         |
| Phantom read        | Same query returns new rows inserted by another transaction |

Example of phantom read:

Transaction A:

```sql
SELECT COUNT(*) FROM orders WHERE status = 'PAID';
```

Transaction B inserts a new `PAID` order and commits.

Transaction A runs same query again and sees a different count.

---

## 37. What is locking in MySQL?

Locking prevents multiple transactions from corrupting data.

Types:

| Lock             | Meaning             |
| ---------------- | ------------------- |
| Shared lock      | Read lock           |
| Exclusive lock   | Write lock          |
| Row-level lock   | Locks selected rows |
| Table-level lock | Locks full table    |

InnoDB performs row-level locking and supports consistent non-locking reads by default. ([MySQL][5])

Example:

```sql
SELECT * FROM accounts
WHERE id = 1
FOR UPDATE;
```

This locks the selected row for update.

---

## 38. What is a deadlock?

A **deadlock** happens when two or more transactions wait for each other’s locks and none can continue. MySQL documentation defines this situation as multiple transactions being unable to proceed because each holds a lock needed by another. ([MySQL][6])

Example:

Transaction 1:

```sql
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
```

Transaction 2:

```sql
UPDATE accounts SET balance = balance - 100 WHERE id = 2;
UPDATE accounts SET balance = balance + 100 WHERE id = 1;
```

Both transactions lock rows in different order. This can cause deadlock.

Prevention:

* Always access tables/rows in same order
* Keep transactions short
* Add proper indexes
* Retry transaction if deadlock occurs

---

## 39. What is MVCC?

**MVCC** means Multi-Version Concurrency Control.

It allows readers and writers to work concurrently by maintaining versions of rows.

Benefit:

* Reads do not block writes in many cases
* Writes do not block normal reads in many cases
* Better concurrency

InnoDB uses consistent reads by default, which is an important part of MVCC behavior. ([MySQL][5])

---

## 40. What is the difference between optimistic and pessimistic locking?

**Optimistic locking** assumes conflict is rare.

Usually implemented using a version column.

Example:

```sql
UPDATE product
SET stock = stock - 1,
    version = version + 1
WHERE id = 101
AND version = 5;
```

If update count is `0`, someone else updated the row.

**Pessimistic locking** locks the row before update.

Example:

```sql
SELECT * FROM product
WHERE id = 101
FOR UPDATE;
```

In Java JPA:

```java
@Version
private Integer version;
```

This enables optimistic locking in Hibernate/JPA.

---

## 41. What is the difference between `UNION` and `UNION ALL`?

`UNION` removes duplicates.

`UNION ALL` keeps duplicates and is faster.

Example:

```sql
SELECT email FROM customers
UNION
SELECT email FROM vendors;
```

```sql
SELECT email FROM customers
UNION ALL
SELECT email FROM vendors;
```

Use `UNION ALL` when duplicates are acceptable or already impossible.

---

## 42. What is a view?

A **view** is a virtual table based on a SQL query.

Example:

```sql
CREATE VIEW active_users AS
SELECT id, name, email
FROM users
WHERE status = 'ACTIVE';
```

Usage:

```sql
SELECT * FROM active_users;
```

Benefits:

* Simplifies complex queries
* Improves readability
* Can restrict access to selected columns

---

## 43. What is a stored procedure?

A **stored procedure** is a saved SQL program in the database.

Example:

```sql
DELIMITER //

CREATE PROCEDURE GetUserOrders(IN userId BIGINT)
BEGIN
    SELECT *
    FROM orders
    WHERE user_id = userId;
END //

DELIMITER ;
```

Call:

```sql
CALL GetUserOrders(10);
```

Stored procedures are useful for database-heavy logic, but in modern microservices, business logic is usually kept in the application layer.

---

## 44. What is a trigger?

A **trigger** automatically runs when an event occurs on a table.

Example:

```sql
CREATE TRIGGER before_user_insert
BEFORE INSERT ON users
FOR EACH ROW
SET NEW.created_at = NOW();
```

Triggers can be useful for audit logic, but overusing them can make application behavior hard to understand.

---

## 45. What is the difference between stored procedure and function?

| Stored Procedure                | Function                   |
| ------------------------------- | -------------------------- |
| Called using `CALL`             | Used inside SQL expression |
| May return multiple result sets | Usually returns one value  |
| Used for operations             | Used for calculation       |
| Can have IN/OUT parameters      | Returns a value            |

Function example:

```sql
SELECT UPPER(name) FROM users;
```

Stored procedure example:

```sql
CALL GetUserOrders(10);
```

---

## 46. What is pagination in MySQL?

Pagination means fetching data page by page.

Example:

```sql
SELECT *
FROM orders
ORDER BY created_at DESC
LIMIT 10 OFFSET 20;
```

This means:

* Page size = 10
* Skip first 20 rows
* Fetch next 10 rows

For large data, deep offset pagination can become slow.

Better approach: keyset pagination.

```sql
SELECT *
FROM orders
WHERE created_at < '2026-06-01 10:00:00'
ORDER BY created_at DESC
LIMIT 10;
```

This is useful for infinite scrolling.

---

## 47. How do you find duplicate records?

Example: duplicate emails.

```sql
SELECT email, COUNT(*)
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

To prevent future duplicates:

```sql
ALTER TABLE users
ADD CONSTRAINT uk_users_email UNIQUE(email);
```

In Java applications, also handle duplicate key exceptions properly.

---

## 48. How do you get the second highest salary?

Approach 1:

```sql
SELECT MAX(salary)
FROM employee
WHERE salary < (
    SELECT MAX(salary)
    FROM employee
);
```

Approach 2:

```sql
SELECT DISTINCT salary
FROM employee
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

For interview, explain that `DISTINCT` is important if multiple employees have the same highest salary.

---

## 49. How do you connect MySQL with Java using JDBC?

Example:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class MySqlJdbcExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/companydb";
        String username = "root";
        String password = "root";

        String sql = "SELECT id, name, email FROM users WHERE status = ?";

        try (
            Connection connection = DriverManager.getConnection(url, username, password);
            PreparedStatement ps = connection.prepareStatement(sql)
        ) {
            ps.setString(1, "ACTIVE");

            try (ResultSet rs = ps.executeQuery()) {
                while (rs.next()) {
                    System.out.println(
                        rs.getLong("id") + " - " +
                        rs.getString("name") + " - " +
                        rs.getString("email")
                    );
                }
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

Important interview points:

* Use `PreparedStatement`
* Avoid SQL injection
* Close resources
* Use connection pooling in real projects
* In Spring Boot, usually use Spring Data JPA or JdbcTemplate

---

## 50. How do you handle transactions in Java with MySQL?

Using plain JDBC:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

public class TransactionExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/bankdb";
        String username = "root";
        String password = "root";

        String debitSql = "UPDATE accounts SET balance = balance - ? WHERE id = ?";
        String creditSql = "UPDATE accounts SET balance = balance + ? WHERE id = ?";

        try (Connection connection = DriverManager.getConnection(url, username, password)) {

            connection.setAutoCommit(false);

            try (
                PreparedStatement debitStmt = connection.prepareStatement(debitSql);
                PreparedStatement creditStmt = connection.prepareStatement(creditSql)
            ) {
                debitStmt.setBigDecimal(1, new java.math.BigDecimal("1000"));
                debitStmt.setLong(2, 1);
                debitStmt.executeUpdate();

                creditStmt.setBigDecimal(1, new java.math.BigDecimal("1000"));
                creditStmt.setLong(2, 2);
                creditStmt.executeUpdate();

                connection.commit();
                System.out.println("Transaction successful");

            } catch (Exception e) {
                connection.rollback();
                System.out.println("Transaction rolled back");
                e.printStackTrace();
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

In Spring Boot, we usually use:

```java
@Transactional
public void transferMoney(Long fromAccount, Long toAccount, BigDecimal amount) {
    accountRepository.debit(fromAccount, amount);
    accountRepository.credit(toAccount, amount);
}
```

Interview explanation:

`@Transactional` ensures that if any exception occurs, the full transaction is rolled back.

---

# Important MySQL Queries You Should Practice

## Create table

```sql
CREATE TABLE employee (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    salary DECIMAL(10,2),
    department_id BIGINT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Insert data

```sql
INSERT INTO employee(name, email, salary, department_id)
VALUES ('Danish', 'danish@example.com', 90000, 1);
```

## Update data

```sql
UPDATE employee
SET salary = 95000
WHERE id = 1;
```

## Delete data

```sql
DELETE FROM employee
WHERE id = 1;
```

## Join query

```sql
SELECT e.name, d.name AS department_name
FROM employee e
JOIN department d ON e.department_id = d.id;
```

## Group by query

```sql
SELECT department_id, COUNT(*) AS total_employees
FROM employee
GROUP BY department_id;
```

## Index creation

```sql
CREATE INDEX idx_employee_department
ON employee(department_id);
```

---

# High-Value Interview Topics for Your 7-Year Java Backend Profile

For your experience level, focus more deeply on these:

| Area                          | Depth Required |
| ----------------------------- | -------------- |
| Joins                         | Strong         |
| Indexing                      | Very strong    |
| Transactions                  | Very strong    |
| Isolation levels              | Strong         |
| Deadlocks                     | Strong         |
| Query optimization            | Very strong    |
| `EXPLAIN` plan                | Strong         |
| JDBC/JPA transaction handling | Very strong    |
| Pagination                    | Strong         |
| Schema design                 | Strong         |

For product-based company interviews, they may not ask only syntax. They may ask real scenarios like:

> “An API is slow because one MySQL query is taking 5 seconds. How will you debug?”

Professional answer:

1. Check query and API logs.
2. Run `EXPLAIN`.
3. Check whether indexes are used.
4. Avoid `SELECT *`.
5. Check join conditions.
6. Check table size.
7. Add proper composite/covering index.
8. Check slow query log.
9. Check transaction locks.
10. Validate improvement with real data.

---

# Final Revision Checklist

Before interview, make sure you can confidently explain:

* Primary key vs unique key
* Inner join vs left join
* `WHERE` vs `HAVING`
* Index and composite index
* Clustered vs secondary index
* `EXPLAIN`
* ACID properties
* Isolation levels
* Deadlocks
* JDBC transaction handling
* Spring Boot `@Transactional`
* Pagination using `LIMIT OFFSET`
* Query optimization steps

These 50 questions are enough to cover most MySQL interview rounds for Java backend roles.

[1]: https://dev.mysql.com/doc/refman/8.4/en/innodb-introduction.html?utm_source=chatgpt.com "MySQL 8.4 Reference Manual :: 17.1 Introduction to InnoDB"
[2]: https://dev.mysql.com/doc/refman/8.4/en/innodb-index-types.html?utm_source=chatgpt.com "17.6.2.1 Clustered and Secondary Indexes"
[3]: https://dev.mysql.com/doc/refman/8.4/en/column-indexes.html?utm_source=chatgpt.com "MySQL 8.4 Reference Manual :: 10.3.5 Column Indexes"
[4]: https://dev.mysql.com/doc/refman/8.4/en/innodb-transaction-isolation-levels.html?utm_source=chatgpt.com "17.7.2.1 Transaction Isolation Levels"
[5]: https://dev.mysql.com/doc/refman/8.4/en/innodb-transaction-model.html?utm_source=chatgpt.com "17.7.2 InnoDB Transaction Model"
[6]: https://dev.mysql.com/doc/refman/8.4/en/innodb-deadlocks.html?utm_source=chatgpt.com "MySQL 8.4 Reference Manual :: 17.7.5 Deadlocks in InnoDB"
