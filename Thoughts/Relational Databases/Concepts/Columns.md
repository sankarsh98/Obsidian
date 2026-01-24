# Columns (Attributes)

In the context of relational databases, a **column** (also known as an **attribute** or **field**) represents a single, specific characteristic or property of the entity that a table describes. Each column in a table has a unique name and a defined data type that dictates what kind of information it can store.

## Characteristics of Columns

1.  **Unique Name**: Within a given table, each column must have a unique name (e.g., `FirstName`, `Email`, `OrderDate`).
2.  **Data Type**: Every column is assigned a data type (e.g., `INT` for integers, `VARCHAR` for variable-length strings, `DATE` for dates, `BOOLEAN` for true/false). This ensures data integrity and efficient storage.
3.  **Domain**: The set of all possible values that a column can hold, as defined by its data type and any constraints.
4.  **Order**: The columns in a table have a defined order, which is established when the table is created.
5.  **Constraints**: Columns can have constraints applied to them to enforce data integrity rules. Common constraints include:
    *   **NOT NULL**: Ensures that a column cannot have a `NULL` value.
    *   **UNIQUE**: Ensures that all values in a column are different.
    *   **PRIMARY KEY**: A special type of unique and not null constraint that uniquely identifies each row in a table.
    *   **FOREIGN KEY**: Establishes a link to a primary key in another table.
    *   **CHECK**: Ensures that all values in a column satisfy a specific condition.
    *   **DEFAULT**: Provides a default value for a column if no value is explicitly specified.

## Example

Consider the `Customers` table again:

| CustomerID (PK) | FirstName | LastName | Email                 |
| :-------------- | :-------- | :------- | :-------------------- |
| 1               | Alice     | Smith    | alice.s@example.com   |
| 2               | Bob       | Johnson  | bob.j@example.com     |
| 3               | Charlie   | Brown    | charlie.b@example.com |

In this table:

*   `CustomerID` is a column of type `INT` (integer), likely with `PRIMARY KEY` and `NOT NULL` constraints.
*   `FirstName` is a column of type `VARCHAR` (variable-length string), likely with a `NOT NULL` constraint.
*   `LastName` is a column of type `VARCHAR`, also likely with a `NOT NULL` constraint.
*   `Email` is a column of type `VARCHAR`, possibly with a `UNIQUE` constraint to prevent duplicate email addresses.

Each column defines a specific piece of information we want to store about a customer.

## Importance

Columns are crucial because they:

*   **Structure Data**: They provide a clear structure for what data is stored about each entity.
*   **Enforce Data Integrity**: Through data types and constraints, they help maintain the accuracy and consistency of the data.
*   **Facilitate Queries**: SQL queries rely heavily on column names to select, filter, and manipulate specific pieces of information.

Understanding columns, their data types, and associated constraints is fundamental to designing and working with relational databases.
