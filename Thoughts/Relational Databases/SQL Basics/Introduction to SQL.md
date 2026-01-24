# Introduction to SQL

**SQL (Structured Query Language)** is the standard language for managing and manipulating relational databases. It is used to communicate with a relational database management system (RDBMS) to perform tasks such as storing, retrieving, modifying, and deleting data.

## History

Developed at IBM in the early 1970s, originally called SEQUEL (Structured English Query Language), it was designed to manipulate and retrieve data stored in IBM's original relational database management system, System R. SQL became an ANSI (American National Standards Institute) standard in 1986 and an ISO (International Organization for Standardization) standard in 1987, ensuring portability across various database systems.

## What Can SQL Do?

*   **Query databases**: Retrieve data from a database.
*   **Insert records**: Add new data into a database.
*   **Update records**: Modify existing data in a database.
*   **Delete records**: Remove data from a database.
*   **Create new databases**: Define new database structures.
*   **Create new tables**: Define the structure of new tables.
*   **Create stored procedures**: Define callable blocks of SQL code.
*   **Create views**: Define virtual tables.
*   **Set permissions**: Control access to tables, procedures, and views.

## Categories of SQL Commands

SQL commands are broadly categorized into several types:

1.  **Data Definition Language (DDL)**: Used to define and modify the database schema.
    *   `CREATE`: To create databases, tables, views, indexes, functions, procedures, and triggers.
    *   `ALTER`: To modify the structure of existing database objects.
    *   `DROP`: To delete databases, tables, views, and other objects.
    *   `TRUNCATE`: To remove all records from a table, including space allocated for the records, but without removing the table structure itself.
    *   `RENAME`: To rename an existing database object.

2.  **Data Manipulation Language (DML)**: Used for managing data within schema objects.
    *   `SELECT`: To retrieve data from a database.
    *   `INSERT`: To insert new data into a table.
    *   `UPDATE`: To modify existing data in a table.
    *   `DELETE`: To remove records from a table.

3.  **Data Control Language (DCL)**: Used for controlling access to data and the database.
    *   `GRANT`: To give users access privileges to the database.
    *   `REVOKE`: To remove user access privileges.

4.  **Transaction Control Language (TCL)**: Used to manage [[Transactions|transactions]] in the database.
    *   `COMMIT`: To save the work done in a transaction permanently.
    *   `ROLLBACK`: To restore the database to the last committed state (undo changes).
    *   `SAVEPOINT`: To set a point within a transaction to which you can later roll back.

## Basic SQL Syntax

SQL statements typically start with a keyword (like `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `CREATE`), followed by table names, column names, and conditions.

*   SQL keywords are not case-sensitive (e.g., `SELECT` is the same as `select`), but it is common practice to write them in uppercase for readability.
*   Most SQL statements end with a semicolon (`;`).

## Example (Conceptual)

To create a table:
```sql
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    Email VARCHAR(100) UNIQUE
);
```

To insert data:
```sql
INSERT INTO Customers (CustomerID, FirstName, LastName, Email)
VALUES (1, 'Alice', 'Smith', 'alice.s@example.com');
```

To retrieve data:
```sql
SELECT FirstName, LastName FROM Customers WHERE CustomerID = 1;
```

To update data:
```sql
UPDATE Customers
SET Email = 'alice.smith@example.com'
WHERE CustomerID = 1;
```

To delete data:
```sql
DELETE FROM Customers WHERE CustomerID = 1;
```

SQL is a powerful and versatile language that remains the cornerstone of relational database management. Understanding its core commands and principles is essential for anyone working with databases.

## Next Steps in SQL Basics

*   [[CREATE TABLE]]
*   [[INSERT INTO]]
*   [[SELECT Statement]]
*   `UPDATE` (will not create a separate page, covered in DML description)
*   `DELETE` (will not create a separate page, covered in DML description)
