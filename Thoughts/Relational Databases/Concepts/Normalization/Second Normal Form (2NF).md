# Second Normal Form (2NF)

**Second Normal Form (2NF)** is the next step in database normalization after achieving [[First Normal Form (1NF)|1NF]]. A table is in 2NF if it is in 1NF and all non-key attributes are fully functionally dependent on the entire [[Primary Key]]. This rule primarily applies to tables with composite primary keys.

## Rules for 2NF

A table is in Second Normal Form if and only if:

1.  It is in [[First Normal Form (1NF)|1NF]].
2.  All non-key attributes are **fully functionally dependent** on the primary key. This means that no non-key attribute is dependent on only *part* of a composite primary key.

## Functional Dependency

A functional dependency (FD) `A -> B` exists if the value of attribute `A` uniquely determines the value of attribute `B`.

*   **Full Functional Dependency**: An attribute `B` is fully functionally dependent on a composite attribute `A` if `B` is functionally dependent on `A`, but not on any proper subset of `A`.
*   **Partial Functional Dependency**: An attribute `B` is partially functionally dependent on a composite attribute `A` if `B` is functionally dependent on `A` and also on a proper subset of `A`. It is these partial dependencies that 2NF aims to eliminate.

## Why 2NF is Important

Eliminating partial functional dependencies helps to:

*   **Reduce Data Redundancy**: Avoids storing the same information multiple times within a table.
*   **Prevent Update Anomalies**: Prevents situations where updating one piece of data requires updating multiple rows, leading to inconsistencies if not all are updated.
*   **Prevent Deletion Anomalies**: Ensures that deleting a record does not inadvertently remove information about other entities.
*   **Prevent Insertion Anomalies**: Allows for inserting information about one part of an entity without requiring information about another.

## Example of Not in 2NF

Consider a `Order_Details` table that tracks items in an order, assuming `(OrderID, ProductID)` is the composite primary key.

### Not in 2NF

| OrderID (PK) | ProductID (PK) | ProductName      | OrderDate  | Quantity | ProductPrice |
| :----------- | :------------- | :--------------- | :--------- | :------- | :----------- |
| 1            | A1             | Laptop           | 2023-01-01 | 1        | 1200         |
| 1            | B2             | Mouse            | 2023-01-01 | 2        | 25           |
| 2            | A1             | Laptop           | 2023-01-02 | 1        | 1200         |
| 2            | C3             | Keyboard         | 2023-01-02 | 1        | 75           |

Here, the composite primary key is `(OrderID, ProductID)`.
Let's analyze the functional dependencies:

*   `(OrderID, ProductID) -> Quantity` (Quantity depends on a specific product in a specific order)
*   `OrderID -> OrderDate` (OrderDate depends only on OrderID, not on ProductID) - **Partial Dependency!**
*   `ProductID -> ProductName, ProductPrice` (ProductName and ProductPrice depend only on ProductID, not on OrderID) - **Partial Dependency!**

Because `OrderDate` depends only on `OrderID` (part of the composite key), and `ProductName`, `ProductPrice` depend only on `ProductID` (another part of the composite key), this table is not in 2NF.

This leads to anomalies:
*   **Redundancy**: `OrderDate`, `ProductName`, and `ProductPrice` are repeated for every item in an order.
*   **Update Anomaly**: If `OrderDate` needs to be changed for `OrderID = 1`, it must be updated in two rows. If only one is updated, data becomes inconsistent.
*   **Deletion Anomaly**: If we delete the last item from `OrderID = 2` (e.g., `ProductID = C3`), we lose the `OrderDate` for `OrderID = 2`.
*   **Insertion Anomaly**: We cannot record an `OrderDate` unless there is at least one product in that order.

## Converting to 2NF

To convert the table to 2NF, we decompose it into smaller tables, ensuring that all non-key attributes are fully functionally dependent on the primary key of their respective tables.

### In 2NF

We split the original table into three tables: `Orders`, `Products`, and `Order_Items`.

**Orders Table (now in 2NF):**

| OrderID (PK) | OrderDate  |
| :----------- | :--------- |
| 1            | 2023-01-01 |
| 2            | 2023-01-02 |

*   `OrderID` is the primary key.
*   `OrderDate` is fully functionally dependent on `OrderID`.

**Products Table (now in 2NF):**

| ProductID (PK) | ProductName | ProductPrice |
| :------------- | :---------- | :----------- |
| A1             | Laptop      | 1200         |
| B2             | Mouse       | 25           |
| C3             | Keyboard    | 75           |

*   `ProductID` is the primary key.
*   `ProductName` and `ProductPrice` are fully functionally dependent on `ProductID`.

**Order_Items Table (Junction Table, now in 2NF):**

| OrderID (PK/FK) | ProductID (PK/FK) | Quantity |
| :-------------- | :---------------- | :------- |
| 1               | A1                | 1        |
| 1               | B2                | 2        |
| 2               | A1                | 1        |
| 2               | C3                | 1        |

*   `(OrderID, ProductID)` is the composite primary key.
*   `Quantity` is fully functionally dependent on the entire composite key `(OrderID, ProductID)`.

Now, each table is in 2NF. This decomposition eliminates redundancy and prevents the anomalies observed earlier. `OrderDate` is no longer repeated, and product details are stored only once. We can now add an order without products, add products without orders, and update product prices or order dates independently.

Moving to 2NF is a crucial step for improving database design by addressing partial dependencies and enhancing data integrity.
