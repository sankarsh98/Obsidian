# Foreign Key

A **Foreign Key (FK)** is a key (a column or a set of columns) in one [[Tables|table]] that refers to the [[Primary Key|Primary Key]] of another table. Its primary purpose is to link two tables together and establish a [[One-to-Many|relationship]] between them.

## Characteristics of a Foreign Key

1.  **References a Primary Key**: A foreign key in one table (the "referencing" or "child" table) must always refer to a primary key (or unique key) in another table (the "referenced" or "parent" table).
2.  **Data Type Match**: The data type of the foreign key column(s) must match the data type of the primary key column(s) it references.
3.  **Nullability**: Unlike primary keys, foreign keys *can* contain `NULL` values. A `NULL` foreign key value indicates that there is no relationship for that particular row.
4.  **Referential Integrity**: The most crucial aspect of foreign keys is the enforcement of **referential integrity**. This means that if a foreign key value exists in the child table, then a corresponding (matching) primary key value must exist in the parent table. This prevents "orphan" records where a child record points to a non-existent parent.

## Purpose of a Foreign Key

*   **Establish Relationships**: Foreign keys are the mechanism by which [[Tables|tables]] are linked, allowing the database to represent relationships like "one-to-many" or "many-to-many" (through an intermediate table).
*   **Enforce Referential Integrity**: They ensure that relationships between tables remain consistent. For example, you cannot delete a customer record (parent table) if there are orders associated with that customer (child table) without first dealing with those orders.
*   **Prevent Invalid Data**: By requiring a foreign key value to match an existing primary key, invalid data entries are prevented, thus maintaining data quality.
*   **Allow for Joins**: Foreign keys are used in SQL `JOIN` operations to combine data from related tables.

## Example

Consider two tables: `Authors` and `Books`.

### Authors Table (Parent Table)

| AuthorID (PK) | AuthorName    | Country    |
| :------------ | :------------ | :--------- |
| 1             | Jane Austen   | UK         |
| 2             | Mark Twain    | USA        |
| 3             | Leo Tolstoy   | Russia     |

Here, `AuthorID` is the Primary Key for the `Authors` table.

### Books Table (Child Table)

| BookID (PK) | Title             | AuthorID (FK) | PublishYear |
| :---------- | :---------------- | :------------ | :---------- |
| 1001        | Pride and Prejudice | 1             | 1813        |
| 1002        | Huckleberry Finn  | 2             | 1884        |
| 1003        | War and Peace     | 3             | 1869        |
| 1004        | Emma              | 1             | 1815        |

In the `Books` table, `BookID` is its Primary Key. The `AuthorID` column in the `Books` table is a Foreign Key. It refers to the `AuthorID` (Primary Key) in the `Authors` table.

**Referential Integrity in Action**:
*   If you try to insert a book with `AuthorID = 99` into the `Books` table, the database would reject it because `AuthorID = 99` does not exist in the `Authors` table.
*   If you try to delete `AuthorID = 1` (Jane Austen) from the `Authors` table, the database might prevent it (or cascade the deletion to related books, depending on the `ON DELETE` rule configured) because there are books (`Pride and Prejudice`, `Emma`) referencing her `AuthorID`.

## ON DELETE and ON UPDATE Actions

When defining a foreign key, you can specify actions that the database should take when the referenced primary key in the parent table is deleted or updated:

*   **`ON DELETE CASCADE`**: If the parent row is deleted, all child rows referencing it are also deleted.
*   **`ON DELETE SET NULL`**: If the parent row is deleted, the foreign key values in the child rows are set to `NULL`. (Requires the foreign key column to be nullable).
*   **`ON DELETE RESTRICT` / `NO ACTION`**: Prevents deletion of the parent row if there are dependent child rows. (This is often the default behavior).
*   **`ON UPDATE CASCADE`**: If the parent primary key is updated, the foreign key values in the child rows are also updated.

Foreign keys are vital for designing a logically consistent and robust relational database, enabling complex data relationships and ensuring data integrity.
