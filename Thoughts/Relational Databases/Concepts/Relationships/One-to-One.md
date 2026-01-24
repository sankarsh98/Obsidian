# One-to-One Relationship (1:1)

A **One-to-One (1:1) relationship** in a relational database occurs when a single record in one [[Tables|table]] can be related to at most one record in another table, and vice-versa. While less common than one-to-many or many-to-many, 1:1 relationships are used for specific design purposes.

## Characteristics

*   **Mutual Exclusivity**: Each row in the "parent" table is associated with zero or one row in the "child" table.
*   **Unique Link**: The connection between the two tables is established by linking their respective [[Primary Key|primary keys]] or by using a [[Foreign Key]] in one table that also has a [[Candidate Key|unique constraint]] on it, referencing the primary key of the other table.

## Implementation

A 1:1 relationship is typically implemented by:

1.  **Shared Primary Key**: Both tables share the same primary key. This implies that the primary key of the "child" table also acts as its foreign key, referencing the "parent" table's primary key.
2.  **Unique Foreign Key**: The "child" table has a foreign key that references the primary key of the "parent" table. Additionally, a unique constraint is placed on this foreign key column in the child table to ensure that only one record in the child table can be associated with a record in the parent table.

## When to Use One-to-One Relationships

While it might seem counter-intuitive to split related data across two tables if they are always linked one-to-one, there are several valid reasons:

1.  **Data Partitioning (Security/Access Control)**:
    *   If some attributes of an entity are highly sensitive and require stricter security measures or different access permissions, they can be placed in a separate table.
    *   Example: A `Users` table might hold basic user info, while a `UserSecurity` table (1:1 with `Users`) holds passwords and security questions.

2.  **Performance Optimization**:
    *   If an entity has many attributes, some of which are frequently accessed and others rarely, splitting them into two tables can improve performance.
    *   Example: A `Products` table with common product details, and a `ProductSpecifications` table (1:1 with `Products`) for large, infrequently used textual descriptions or technical specs. This avoids loading large amounts of unused data during common queries.

3.  **Handling Optional Attributes (Sparse Data)**:
    *   If an entity has many optional attributes that are often `NULL` for most records, placing these attributes in a separate table can save storage space and simplify the main table.
    *   Example: A `Employees` table with core employee data, and an `EmployeeBenefits` table (1:1 with `Employees`) for benefits information that only applies to certain employees.

4.  **Managing Large Objects (BLOBs/CLOBs)**:
    *   Large binary objects (BLOBs like images, videos) or character objects (CLOBs like large documents) can be stored in a separate table due to their size, preventing the main table from becoming unwieldy.
    *   Example: A `Documents` table might contain metadata, and a `DocumentContent` table (1:1 with `Documents`) stores the actual document binary.

5.  **Subtyping / Generalization Hierarchies**:
    *   In object-oriented modeling, 1:1 relationships can represent subtypes.
    *   Example: A `Persons` table, and `Customers` (1:1 with `Persons`) and `Employees` (1:1 with `Persons`) tables that inherit common `Person` attributes.

## Example Scenario

Let's illustrate with an `Employees` table and an `EmployeeDetails` table.

### Employees Table

| EmployeeID (PK) | FirstName | LastName | Department |
| :-------------- | :-------- | :------- | :--------- |
| 101             | John      | Doe      | Sales      |
| 102             | Jane      | Smith    | Marketing  |

### EmployeeDetails Table

| EmployeeID (PK/FK) | DateOfBirth | SocialSecurityNumber | EmergencyContact |
| :----------------- | :---------- | :------------------- | :--------------- |
| 101                | 1985-03-15  | XXX-XX-1234          | Alice Doe        |
| 102                | 1990-07-22  | YYY-YY-5678          | Bob Smith        |

In this example:
*   `EmployeeID` is the primary key in the `Employees` table.
*   `EmployeeID` in the `EmployeeDetails` table is both its primary key *and* a foreign key referencing `Employees.EmployeeID`. The unique constraint on `EmployeeDetails.EmployeeID` ensures the 1:1 relationship.
*   Sensitive data like Social Security Number and Emergency Contact are separated into `EmployeeDetails`.

One-to-one relationships, when used appropriately, can enhance database design by improving modularity, security, and performance.
