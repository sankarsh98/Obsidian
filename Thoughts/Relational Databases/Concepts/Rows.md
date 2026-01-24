# Rows (Records/Tuples)

In a relational database, a **row** (also known as a **record** or a **tuple**) represents a single, complete, and distinct entry in a [[Tables|table]]. Each row contains a set of related data values, with each value corresponding to a specific [[Columns|column]] in that table.

## Understanding Rows

Think of a table as a spreadsheet. If columns define the categories of information (like "Name," "Age," "City"), then each row is an actual entry for one person, containing their specific name, age, and city.

### Characteristics of Rows:

*   **Instance of an Entity**: Each row represents one instance of the entity that the table describes. For example, in a `Customers` table, each row is a unique customer.
*   **Collection of Attributes**: A row is a collection of attribute values (data for each column) that together describe a single entity.
*   **Uniqueness**: While not strictly enforced for all tables, in well-designed relational databases, each row should ideally be uniquely identifiable, usually through a [[Primary Key]].
*   **Unordered**: The logical order of rows in a table is not inherent and generally not guaranteed unless an `ORDER BY` clause is explicitly used in an SQL query. Databases store rows in an order optimized for performance, not necessarily for human readability.

## Example

Consider the `Products` table:

| ProductID (PK) | ProductName    | Price | Category |
| :------------- | :------------- | :---- | :------- |
| 101            | Laptop         | 1200  | Electronics |
| 102            | Mouse          | 25    | Electronics |
| 103            | Keyboard       | 75    | Electronics |
| 201            | Desk Chair     | 150   | Furniture |

In this table:

*   The entry `101 | Laptop | 1200 | Electronics` is a single **row**, representing one specific product.
*   Each value (`101`, `Laptop`, `1200`, `Electronics`) corresponds to a specific column (`ProductID`, `ProductName`, `Price`, `Category`).
*   `ProductID` acts as the primary key, ensuring each product row is unique.

## Relationship with other Concepts

*   **[[Tables|Tables]]**: Rows are the horizontal components that populate a table.
*   **[[Columns|Columns]]**: Rows consist of data values that align with the definitions of the columns.
*   **[[Primary Key]]**: A column or combination of columns used to uniquely identify each row in a table. Without a primary key, it would be difficult to reliably distinguish between individual rows if other column values were duplicated.
*   **Data Integrity**: Rows help maintain data integrity by ensuring that all related data for a single entity is kept together.

Understanding rows is essential for data entry, retrieval, and manipulation, as they are the individual units of information that users typically work with.
