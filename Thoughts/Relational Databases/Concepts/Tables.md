# Tables (Relations)

In a relational database, a **table** (also known as a **relation**) is the fundamental structure for storing data. It organizes data into a grid-like format, consisting of rows and columns. Each table represents a specific entity or concept in the real world, such as "Customers," "Products," "Orders," or "Employees."

## Structure of a Table

A table is characterized by:

1.  **Columns (Attributes)**: These define the type of data that the table will hold. Each column has a unique name within the table and a specific data type (e.g., integer, text, date). Columns represent the attributes or properties of the entity.
2.  **Rows (Tuples/Records)**: Each row in a table represents a single, complete record or instance of the entity. For example, in a "Customers" table, each row would represent one specific customer.
3.  **Cells**: The intersection of a row and a column forms a cell, which contains a single data value.

## Example

Consider a `Customers` table:

| CustomerID (PK) | FirstName | LastName | Email                 |
| :-------------- | :-------- | :------- | :-------------------- |
| 1               | Alice     | Smith    | alice.s@example.com   |
| 2               | Bob       | Johnson  | bob.j@example.com     |
| 3               | Charlie   | Brown    | charlie.b@example.com |

In this example:

*   The table name is `Customers`.
*   `CustomerID`, `FirstName`, `LastName`, and `Email` are the **columns**.
*   Each horizontal entry (e.g., `1 | Alice | Smith | alice.s@example.com`) is a **row**.
*   The value `Alice` is stored in a **cell** at the intersection of the `FirstName` column and the first row.
*   `CustomerID` is likely a [[Primary Key|Primary Key]], uniquely identifying each customer.

## Properties of Tables

*   **Unique Name**: Each table within a database must have a unique name.
*   **Ordered Columns**: The order of columns is defined when the table is created.
*   **Unordered Rows**: The logical order of rows in a table is not guaranteed unless an `ORDER BY` clause is used in a query.
*   **Homogeneous Columns**: All values in a given column must be of the same data type.
*   **Atomic Values**: Each cell should contain a single, indivisible value.

## Relationship to other Concepts

*   **[[Primary Key]]**: A column or set of columns that uniquely identifies each row in a table.
*   **[[Foreign Key]]**: A column or set of columns that establishes a link between data in two tables.
*   **[[One-to-Many.md|Relationships]]**: Tables are connected to each other through these keys to form relationships (e.g., one-to-one, one-to-many, many-to-many).
*   **[[First Normal Form (1NF)|Normalization]]**: The process of organizing the columns and tables of a relational database to minimize data redundancy and improve data integrity.

Understanding tables is crucial as they are the building blocks upon which all other relational database concepts are based.
