# Candidate Key

A **Candidate Key** is a key (a single [[Columns|column]] or a set of columns) in a [[Tables|table]] that can uniquely identify each [[Rows|row]] in that table without any redundant attributes. In essence, it's any minimal set of attributes that can serve as a unique identifier for the rows.

## Characteristics of a Candidate Key

1.  **Unique Identification**: A candidate key must uniquely identify each row in the table. No two rows can have the same value for the candidate key.
2.  **Irreducible (Minimal)**: No proper subset of the candidate key's attributes can uniquely identify tuples. This means that if you remove any column from a composite candidate key, it will no longer be able to uniquely identify rows.
3.  **Non-Nullability**: While not an explicit requirement of the definition itself, practically, if a candidate key is chosen to be the [[Primary Key]], it must not contain `NULL` values. If it's not chosen as the primary key, it can potentially contain `NULL` values, provided uniqueness is still maintained. However, for true unique identification, `NULL` values are generally avoided.

## Relationship to Primary Key

Every table can have one or more candidate keys. Out of all the candidate keys, one is chosen by the database designer to be the **[[Primary Key]]**. The remaining candidate keys that are not chosen as the primary key are then called **Alternate Keys**.

*   **Candidate Keys**: All keys that can uniquely identify a record.
*   **Primary Key**: The chosen candidate key.
*   **Alternate Keys**: The candidate keys not chosen as the primary key.

## Example

Consider a `Students` table:

| StudentID | NationalID | Email                 | FirstName | LastName |
| :-------- | :--------- | :-------------------- | :-------- | :------- |
| 101       | 12345      | alice.s@example.com   | Alice     | Smith    |
| 102       | 67890      | bob.j@example.com     | Bob       | Johnson  |
| 103       | 11223      | charlie.b@example.com | Charlie   | Brown    |

In this table, the potential candidate keys are:

1.  `StudentID`: Assuming `StudentID` is an auto-generated unique identifier.
2.  `NationalID`: Assuming `NationalID` is unique for each student.
3.  `Email`: Assuming each student has a unique email address.
4.  (`FirstName`, `LastName`): This combination *might* be unique, but it's less reliable. For instance, two students could share the same first and last name. If it were truly guaranteed to be unique, it would be a candidate key, but it's not a minimal set if `StudentID` alone is unique.

From these, if we assume `StudentID`, `NationalID`, and `Email` are all unique and irreducible:

*   **Candidate Keys**: `StudentID`, `NationalID`, `Email`
*   **If we choose `StudentID` as the Primary Key**:
    *   **Primary Key**: `StudentID`
    *   **Alternate Keys**: `NationalID`, `Email`

## Importance of Candidate Keys

*   **Flexibility in Design**: Understanding all candidate keys gives database designers flexibility in choosing the most appropriate primary key for a table.
*   **Data Integrity**: They ensure that every row in a table is uniquely identifiable, which is fundamental for data integrity.
*   **Alternative Identification**: Alternate keys (the non-chosen candidate keys) can still be used to uniquely identify records and can have `UNIQUE` constraints applied to them to ensure their uniqueness.

Identifying candidate keys is an important step in the database design process, particularly during [[First Normal Form (1NF)|Normalization]] to ensure that tables are well-structured and free from redundancy.
