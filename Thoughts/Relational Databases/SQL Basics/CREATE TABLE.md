# CREATE TABLE Statement

The `CREATE TABLE` statement in SQL is a Data Definition Language (DDL) command used to define the structure of a new [[Tables|table]] in a database. It specifies the table name, the [[Columns|columns]] it will contain, their data types, and various constraints.

## Syntax

```sql
CREATE TABLE table_name (
    column1_name data_type [CONSTRAINT_1],
    column2_name data_type [CONSTRAINT_2],
    ...
    columnN_name data_type [CONSTRAINT_N],
    [TABLE_CONSTRAINT_1],
    [TABLE_CONSTRAINT_2],
    ...
);
```

### Explanation of Components

*   **`table_name`**: The name of the new table you want to create. This name must be unique within the database schema.
*   **`column_name`**: The name of a column in the table. Each column name must be unique within the table.
*   **`data_type`**: Specifies the type of data the column can hold (e.g., `INT`, `VARCHAR(255)`, `DATE`, `BOOLEAN`). The choice of data type is crucial for data integrity and storage efficiency.
*   **`[CONSTRAINT]`**: Optional keywords that define rules for the data in a column. These are known as **column-level constraints**.
    *   `NOT NULL`: Ensures that the column cannot store `NULL` values.
    *   `UNIQUE`: Ensures that all values in a column are different.
    *   `PRIMARY KEY`: A combination of `NOT NULL` and `UNIQUE`. Uniquely identifies each row in the table. A table can only have one primary key.
    *   `FOREIGN KEY`: Establishes a link between data in two tables.
    *   `DEFAULT value`: Provides a default value for a column when no value is specified during `INSERT`.
    *   `CHECK condition`: Ensures that all values in a column satisfy a specific condition.
*   **`[TABLE_CONSTRAINT]`**: Optional keywords that define rules for the entire table, or rules that involve multiple columns. These are known as **table-level constraints**.
    *   `PRIMARY KEY (column1, column2, ...)`: Defines a composite primary key using multiple columns.
    *   `FOREIGN KEY (column_name) REFERENCES referenced_table(referenced_column) [ON DELETE action] [ON UPDATE action]`: Defines a foreign key.
    *   `UNIQUE (column1, column2, ...)`: Defines a unique constraint across multiple columns.
    *   `CHECK (condition)`: Defines a check constraint that applies to the entire row.

## Common Data Types (Examples)

Data types can vary slightly between different RDBMS (e.g., MySQL, PostgreSQL, SQL Server, Oracle).

*   **Numeric Types**:
    *   `INT` / `INTEGER`: Whole numbers.
    *   `SMALLINT` / `BIGINT`: Smaller/larger whole numbers.
    *   `DECIMAL(p, s)` / `NUMERIC(p, s)`: Fixed-point numbers (p = total digits, s = digits after decimal).
    *   `FLOAT` / `REAL` / `DOUBLE PRECISION`: Floating-point numbers.
*   **String Types**:
    *   `VARCHAR(size)`: Variable-length string (max size).
    *   `CHAR(size)`: Fixed-length string.
    *   `TEXT` / `LONGTEXT`: For very long strings.
*   **Date and Time Types**:
    *   `DATE`: Stores dates (e.g., 'YYYY-MM-DD').
    *   `TIME`: Stores times (e.g., 'HH:MM:SS').
    *   `DATETIME` / `TIMESTAMP`: Stores date and time.
*   **Boolean Type**:
    *   `BOOLEAN` / `TINYINT(1)`: Stores `TRUE` or `FALSE`.

## Examples

### 1. Basic Table Creation

```sql
CREATE TABLE Products (
    ProductID INT PRIMARY KEY,
    ProductName VARCHAR(255) NOT NULL,
    Price DECIMAL(10, 2),
    StockQuantity INT DEFAULT 0
);
```
*   `ProductID` is an integer, acts as the primary key.
*   `ProductName` is a variable-length string, cannot be `NULL`.
*   `Price` is a decimal number with 10 total digits, 2 after the decimal.
*   `StockQuantity` is an integer, defaults to 0 if not provided.

### 2. Table with Foreign Key

```sql
CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    OrderDate DATE NOT NULL,
    CustomerID INT,
    TotalAmount DECIMAL(10, 2),
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);
```
*   `OrderID` is the primary key.
*   `OrderDate` cannot be `NULL`.
*   `CustomerID` is a foreign key, establishing a [[One-to-Many|one-to-many relationship]] with the `Customers` table.

### 3. Table with Composite Primary Key and Check Constraint

```sql
CREATE TABLE Enrollment (
    StudentID INT NOT NULL,
    CourseID INT NOT NULL,
    EnrollmentDate DATE,
    Grade CHAR(2) CHECK (Grade IN ('A', 'B', 'C', 'D', 'F')),
    PRIMARY KEY (StudentID, CourseID), -- Composite Primary Key
    FOREIGN KEY (StudentID) REFERENCES Students(StudentID),
    FOREIGN KEY (CourseID) REFERENCES Courses(CourseID)
);
```
*   `StudentID` and `CourseID` together form the composite primary key.
*   `Grade` has a `CHECK` constraint, ensuring only allowed values are entered.
*   Foreign keys link to `Students` and `Courses` tables, resolving a [[Many-to-Many|many-to-many relationship]].

The `CREATE TABLE` statement is foundational for designing and structuring your relational database. Careful consideration of column data types and constraints at this stage is crucial for database performance, integrity, and scalability.
