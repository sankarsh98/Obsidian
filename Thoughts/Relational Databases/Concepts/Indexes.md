# Indexes

**Indexes** are special lookup [[Tables|tables]] that the database search engine can use to speed up data retrieval. Think of an index like the index in a textbook: instead of reading the entire book to find a topic, you can go to the index, find the topic, and it tells you exactly on which page(s) to find the information.

In a relational database, an index is created on one or more [[Columns|columns]] of a table, providing a quick way to look up rows based on the values in those columns.

## How Indexes Work

When an index is created on a column (or columns), the database typically builds a data structure, most commonly a **B-tree** (or B+ tree). This structure stores the indexed column's values in a sorted order, along with pointers to the actual data rows in the table.

When you execute a query that filters or sorts data based on an indexed column, the database can use the index to quickly locate the relevant rows without scanning the entire table.

## Types of Indexes

1.  **Clustered Index**:
    *   Determines the physical order of data rows in the table.
    *   A table can have only one clustered index because the data rows themselves can only be stored in one physical order.
    *   Often created automatically on the [[Primary Key]] of a table.
    *   Provides very fast retrieval for queries that involve range searches or ordered data access.
    *   Example: A phone book sorted by last name is like a clustered index; the data (names, numbers) is physically ordered.

2.  **Non-Clustered Index**:
    *   Does not determine the physical order of data rows.
    *   Stores the indexed column values and pointers to the actual data rows (which are physically ordered by the clustered index, if one exists).
    *   A table can have multiple non-clustered indexes.
    *   Useful for speeding up queries on frequently searched non-primary key columns.
    *   Example: A book's index is non-clustered; it points to page numbers, but the book's content (the actual data) is physically ordered by pages.

## Advantages of Using Indexes

*   **Faster Data Retrieval**: Significantly speeds up `SELECT` queries, especially with `WHERE` clauses, `JOIN` conditions, and `ORDER BY` clauses.
*   **Unique Constraints**: Indexes are internally used to enforce `UNIQUE` and `PRIMARY KEY` constraints, ensuring that values in those columns are unique.
*   **Improved Sorting**: Queries with `ORDER BY` clauses can use indexes to return sorted results faster.

## Disadvantages of Using Indexes

*   **Increased Storage Space**: Indexes consume disk space, potentially a significant amount for large tables with many indexes.
*   **Slower Write Operations**: `INSERT`, `UPDATE`, and `DELETE` operations become slower because the database needs to update not only the table data but also all associated indexes.
*   **Maintenance Overhead**: Indexes need to be rebuilt or reorganized periodically to maintain optimal performance, especially after many data modifications.

## When to Use Indexes

*   **Primary Key Columns**: Always index primary key columns (most RDBMS do this automatically).
*   **Foreign Key Columns**: Index foreign key columns to speed up `JOIN` operations between related tables.
*   **Frequently Queried Columns**: Columns used frequently in `WHERE` clauses (for filtering data), `ORDER BY` clauses (for sorting), or `GROUP BY` clauses (for aggregation).
*   **Columns with High Cardinality**: Columns with many distinct values (e.g., `EmailAddress`, `NationalID`). Indexes are less effective on columns with very few distinct values (e.g., `Gender`).

## Example (Conceptual)

Consider a `Customers` table with millions of records.

| CustomerID (PK) | FirstName | LastName | Email                 | City    |
| :-------------- | :-------- | :------- | :-------------------- | :------ |
| 1               | Alice     | Smith    | alice.s@example.com   | New York|
| 2               | Bob       | Johnson  | bob.j@example.com     | London  |
| ...             | ...       | ...      | ...                   | ...     |

If you frequently run queries like:
`SELECT * FROM Customers WHERE Email = 'alice.s@example.com';`
`SELECT * FROM Customers WHERE City = 'London' ORDER BY LastName;`

*   An index on `Email` would speed up the first query.
*   An index on `City` (or a composite index on `(City, LastName)`) would speed up the second query.

Without indexes, the database would have to perform a full table scan, checking every row, which can be very slow for large tables.

## Creating an Index (Conceptual SQL)

```sql
-- Create a non-clustered index on a single column
CREATE INDEX idx_customers_email ON Customers (Email);

-- Create a composite non-clustered index on multiple columns
CREATE INDEX idx_customers_city_lastname ON Customers (City, LastName);

-- A unique index (also ensures uniqueness for the indexed column(s))
CREATE UNIQUE INDEX uix_products_productcode ON Products (ProductCode);
```

Indexes are a powerful tool for database performance optimization, but they must be used judiciously, considering the trade-offs between read performance and write performance, as well as storage overhead.
