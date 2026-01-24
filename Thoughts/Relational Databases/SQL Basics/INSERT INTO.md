# INSERT INTO Statement

The `INSERT INTO` statement in SQL is a Data Manipulation Language (DML) command used to add new [[Rows|rows]] of data into an existing [[Tables|table]].

## Syntax

There are two main ways to use the `INSERT INTO` statement:

### 1. Specifying both column names and values (Recommended)

This is the most common and recommended method because it makes the SQL statement more robust and readable. You explicitly list the columns you want to insert data into and then provide the corresponding values.

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

*   **`table_name`**: The name of the table you want to insert data into.
*   **`(column1, column2, column3, ...)`**: A comma-separated list of the columns you are providing values for.
*   **`VALUES (value1, value2, value3, ...)`**: A comma-separated list of the values to be inserted. The order and data type of these values must match the order and data type of the columns specified in the column list.

### 2. Adding values for all columns (Order Dependent)

This method does not specify the column names. You must provide values for *all* columns in the table, and the order of the values must exactly match the order of the columns as they were defined in the `CREATE TABLE` statement. This method is less robust as schema changes (e.g., adding a new column) can break the statement.

```sql
INSERT INTO table_name
VALUES (value1, value2, value3, ...);
```

## Examples

Let's assume we have a `Customers` table created as follows:

```sql
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY AUTO_INCREMENT, -- AUTO_INCREMENT for MySQL, IDENTITY for SQL Server, SERIAL for PostgreSQL
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    Email VARCHAR(100) UNIQUE,
    RegistrationDate DATE DEFAULT CURRENT_DATE
);
```

### Example 1: Inserting a single row (specifying columns)

```sql
INSERT INTO Customers (FirstName, LastName, Email)
VALUES ('Alice', 'Smith', 'alice.s@example.com');
```
*   `CustomerID` will be automatically generated.
*   `RegistrationDate` will use the default `CURRENT_DATE`.

### Example 2: Inserting a single row (all columns, order dependent)

*Note: This assumes the database automatically handles `CustomerID` with `AUTO_INCREMENT` or similar. If `CustomerID` was not auto-incrementing, you would need to provide a value.*

```sql
INSERT INTO Customers
VALUES (NULL, 'Bob', 'Johnson', 'bob.j@example.com', '2023-10-26');
```
*   `NULL` is used for `CustomerID` to allow the `AUTO_INCREMENT` to generate a value. If `CustomerID` was not `AUTO_INCREMENT`, this would likely fail or insert `NULL` if the column allowed it.
*   The values provided (`'Bob'`, `'Johnson'`, `'bob.j@example.com'`, `'2023-10-26'`) must correspond exactly to the order of columns (`CustomerID`, `FirstName`, `LastName`, `Email`, `RegistrationDate`).

### Example 3: Inserting multiple rows (standard syntax)

Some SQL databases allow inserting multiple rows in a single `INSERT INTO` statement using multiple `VALUES` clauses:

```sql
INSERT INTO Customers (FirstName, LastName, Email)
VALUES
    ('Charlie', 'Brown', 'charlie.b@example.com'),
    ('Diana', 'Prince', 'diana.p@example.com');
```

### Example 4: Inserting data from another table

You can also insert data into a table by selecting it from another table using a `SELECT` statement:

```sql
INSERT INTO Employees_Archive (EmployeeID, FirstName, LastName)
SELECT EmployeeID, FirstName, LastName
FROM Employees
WHERE Status = 'Retired';
```
This query inserts all `Retired` employees from the `Employees` table into the `Employees_Archive` table.

## Important Considerations

*   **Data Types**: Ensure that the values you provide match the data types of the columns. Incorrect data types will result in an error.
*   **Constraints**: Values must adhere to any constraints defined on the columns (e.g., `NOT NULL`, `UNIQUE`, `PRIMARY KEY`, `FOREIGN KEY`, `CHECK`). Violating a constraint will cause the `INSERT` operation to fail.
*   **Order of Columns**: When explicitly listing columns, the order of values in the `VALUES` clause must match the order of columns in the column list.
*   **Auto-incrementing/Default Values**: If a column has `AUTO_INCREMENT` (or similar for other DBs) or a `DEFAULT` value, you can often omit it from the column list, and the database will automatically handle it. For `AUTO_INCREMENT` columns, you might explicitly provide `NULL` as a value if you are providing values for all columns but still want the DB to generate the ID.

The `INSERT INTO` statement is fundamental for populating your database with data, making it available for retrieval and manipulation.
