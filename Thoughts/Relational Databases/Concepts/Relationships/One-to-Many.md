# One-to-Many Relationship (1:N or 1:M)

A **One-to-Many (1:N or 1:M) relationship** is the most common type of relationship in relational databases. It exists when a single record in one [[Tables|table]] (the "parent" or "one" side) can be associated with one or more records in another table (the "child" or "many" side), but a record in the child table can only be associated with one record in the parent table.

## Characteristics

*   **Parent-Child Dependency**: The "many" side of the relationship depends on the "one" side. For example, many orders can belong to one customer, but each order belongs to only one customer.
*   **Referential Integrity**: Maintained using a [[Foreign Key]] in the "many" side table that references the [[Primary Key]] of the "one" side table.

## Implementation

A 1:N relationship is typically implemented by placing the [[Primary Key]] of the "one" side table into the "many" side table as a [[Foreign Key]].

*   **Parent Table (One side)**: Contains the primary key that will be referenced.
*   **Child Table (Many side)**: Contains a foreign key that refers to the primary key of the parent table.

## Example Scenario

Let's illustrate with `Customers` and `Orders` tables.

### Customers Table (Parent / "One" Side)

| CustomerID (PK) | FirstName | LastName |
| :-------------- | :-------- | :------- |
| 1               | Alice     | Smith    |
| 2               | Bob       | Johnson  |

### Orders Table (Child / "Many" Side)

| OrderID (PK) | OrderDate  | CustomerID (FK) | TotalAmount |
| :----------- | :--------- | :-------------- | :---------- |
| 101          | 2023-01-05 | 1               | 150.00      |
| 102          | 2023-01-06 | 2               | 200.50      |
| 103          | 2023-01-07 | 1               | 50.25       |
| 104          | 2023-01-08 | 1               | 300.00      |

In this example:

*   `CustomerID` is the primary key in the `Customers` table.
*   `OrderID` is the primary key in the `Orders` table.
*   `CustomerID` in the `Orders` table is a foreign key that references `Customers.CustomerID`.

Here, one customer (`CustomerID = 1`, Alice Smith) can have multiple orders (`OrderID = 101`, `103`, `104`). However, each order (`OrderID = 101`) is associated with only one customer (`CustomerID = 1`). This clearly demonstrates a one-to-many relationship.

## Key Considerations

*   **Referential Integrity**: The foreign key constraint ensures that an `OrderID` cannot be assigned a `CustomerID` that does not exist in the `Customers` table. This prevents data inconsistencies.
*   **Cascading Actions**: When defining the foreign key, you can specify `ON DELETE` and `ON UPDATE` actions (e.g., `CASCADE`, `SET NULL`, `RESTRICT`) to manage what happens to the child records when the parent record is deleted or its primary key is updated.
*   **Querying**: `JOIN` operations are frequently used to combine data from parent and child tables (e.g., to find all orders placed by a specific customer).

One-to-many relationships are essential for efficient database design, reducing data redundancy (since customer details are stored only once in the `Customers` table) and maintaining data integrity across related entities.
