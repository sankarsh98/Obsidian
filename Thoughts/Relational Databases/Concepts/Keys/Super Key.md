# Super Key

A **Super Key** is a key (a single [[Columns|column]] or a set of columns) in a [[Tables|table]] that can uniquely identify each [[Rows|row]] within that table. It is the most general form of a key.

## Characteristics of a Super Key

1.  **Unique Identification**: The defining characteristic of a super key is that it must uniquely identify each row in a table. If you have a super key, you can always pinpoint a specific row.
2.  **Can be Redundant**: Unlike a [[Candidate Key]], a super key does *not* have to be minimal. It can contain additional attributes beyond what is strictly necessary for unique identification. This means that if `K` is a super key, then any superset of `K` (any set of attributes that includes all attributes of `K`) is also a super key.

## Relationship with other Keys

*   **Relationship to Candidate Key**: A [[Candidate Key]] is a *minimal* super key. This means it's a super key from which no attribute can be removed without losing the uniqueness property. Every candidate key is a super key, but not every super key is a candidate key.
*   **Relationship to Primary Key**: A [[Primary Key]] is a chosen candidate key, and since every candidate key is a super key, the primary key is also always a super key.

## Analogy

Imagine a group of people.
*   **Super Key**: Any combination of information that helps you uniquely identify a person. For example, (Full Name, Address, Phone Number, Eye Color) could identify someone, but so could just (Full Name, Address). The first one is a super key, but it's not minimal.
*   **Candidate Key**: The *minimal* information that uniquely identifies a person. For example, (Social Security Number) or (Email Address, Date of Birth).
*   **Primary Key**: The chosen minimal information (candidate key) that is used as the official identifier.

## Example

Consider a `Students` table with the following attributes:

| StudentID | NationalID | Email                 | FirstName | LastName |
| :-------- | :--------- | :-------------------- | :-------- | :------- |
| 101       | 12345      | alice.s@example.com   | Alice     | Smith    |
| 102       | 67890      | bob.j@example.com     | Bob       | Johnson  |
| 103       | 11223      | charlie.b@example.com | Charlie   | Brown    |

Let's assume:
*   `StudentID` is unique.
*   `NationalID` is unique.
*   `Email` is unique.

Here are some possible Super Keys for this table:

*   **`{StudentID}`**: This can uniquely identify a student.
*   **`{NationalID}`**: This can uniquely identify a student.
*   **`{Email}`**: This can uniquely identify a student.
*   **`{StudentID, FirstName}`**: Since `{StudentID}` alone can identify a student, adding `FirstName` doesn't make it *more* unique, but it still *does* uniquely identify a student. Thus, it's a super key.
*   **`{StudentID, LastName, Email}`**: Again, `{StudentID}` alone is enough, but this combination still uniquely identifies a student.
*   **`{NationalID, FirstName, LastName}`**: If `NationalID` is unique, this is a super key.
*   **`{StudentID, NationalID, Email, FirstName, LastName}`**: The set of all attributes is always a super key.

In this example:
*   **Candidate Keys**: `{StudentID}`, `{NationalID}`, `{Email}` (assuming they are minimal and unique).
*   All the listed combinations above are **Super Keys**.

## Importance

While super keys are a broader concept, understanding them helps to define and identify candidate keys effectively. The ultimate goal in database design is often to select one of the candidate keys as the primary key for optimal efficiency and integrity.
