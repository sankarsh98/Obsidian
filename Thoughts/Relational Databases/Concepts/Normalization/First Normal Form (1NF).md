# First Normal Form (1NF)

**First Normal Form (1NF)** is the first and most basic level of database normalization. It sets the fundamental rules for organizing data in a relational database. A table is said to be in 1NF if it meets specific criteria aimed at eliminating repeating groups and ensuring that all attributes hold atomic values.

## Rules for 1NF

A table is in First Normal Form if and only if:

1.  **Each Column Contains Atomic Values**: Every attribute (column) in a table must contain only a single, indivisible value. There should be no multi-valued attributes (e.g., a single cell containing a comma-separated list of values).
2.  **No Repeating Groups**: There are no repeating groups of columns. This means you should not have multiple columns representing the same type of data (e.g., `Phone1`, `Phone2`, `Phone3`). Instead, these should be moved to a separate table.
3.  **Unique Column Names**: Each column in the table must have a unique name.
4.  **Order of Data Does Not Matter**: The order in which data is stored in the table (rows and columns) does not affect its meaning or integrity.

## Why 1NF is Important

Achieving 1NF is the foundational step for a well-structured relational database. It lays the groundwork for:

*   **Eliminating Redundancy**: By removing repeating groups, it starts to reduce redundant data storage.
*   **Improving Data Integrity**: Ensures that data is stored consistently and can be easily accessed and modified.
*   **Simplifying Data Manipulation**: Makes it easier to query, insert, update, and delete data using standard SQL commands.
*   **Enabling Further Normalization**: Without 1NF, it's impossible to move to higher normal forms (2NF, 3NF, etc.).

## Example of Not in 1NF

Consider a `Students` table where a student can have multiple phone numbers and email addresses stored in a single row:

### Not in 1NF

| StudentID | Name  | Phone Numbers          | Email Addresses                 |
| :-------- | :---- | :--------------------- | :------------------------------ |
| 1         | Alice | 111-2222, 333-4444     | alice@email.com, alice@work.com |
| 2         | Bob   | 555-6666               | bob@email.com                   |

This table violates 1NF because:
*   The `Phone Numbers` column contains multiple values (multi-valued attribute).
*   The `Email Addresses` column contains multiple values (multi-valued attribute).
*   There are "repeating groups" implicitly in the form of multiple values within a single cell.

## Converting to 1NF

To convert the above table to 1NF, we need to create separate rows for each phone number and email, or move them to separate tables. The most common and recommended approach is to create separate tables for the multi-valued attributes and link them using [[Foreign Key|foreign keys]].

### In 1NF (Approach 1: Separate Tables for Multi-Valued Attributes)

**Students Table:**

| StudentID (PK) | Name  |
| :------------- | :---- |
| 1              | Alice |
| 2              | Bob   |

**StudentPhones Table:**

| StudentID (FK) | PhoneNumber |
| :------------- | :---------- |
| 1              | 111-2222    |
| 1              | 333-4444    |
| 2              | 555-6666    |

**StudentEmails Table:**

| StudentID (FK) | EmailAddress    |
| :------------- | :-------------- |
| 1              | alice@email.com |
| 1              | alice@work.com  |
| 2              | bob@email.com   |

Now, each column in each table contains atomic values, and there are no repeating groups. Each student's phone numbers and emails are stored in separate rows in their respective tables, linked by the `StudentID` foreign key.

### In 1NF (Approach 2: Flattening, Less Recommended if attributes are numerous)

Another way to achieve 1NF (less ideal for multiple repeating groups but sometimes used for a small fixed number) is to flatten the data, though this often introduces redundancy and is generally avoided for multi-valued attributes like phone numbers unless the number of phones is fixed and small.

**Students Table (in 1NF by flattening - illustrates atomicity but not ideal design):**

| StudentID | Name  | PhoneNumber | EmailAddress    |
| :-------- | :---- | :---------- | :-------------- |
| 1         | Alice | 111-2222    | alice@email.com |
| 1         | Alice | 333-4444    | alice@work.com  |
| 2         | Bob   | 555-6666    | bob@email.com   |

This version is technically in 1NF as each cell is atomic. However, it introduces significant redundancy (Alice's name is repeated) and makes it difficult to manage unique primary keys for the `Students` table itself, often leading to a composite key that includes `PhoneNumber` or `EmailAddress`. The first approach (separate tables) is generally preferred for true multi-valued attributes.

Moving to 1NF is the essential first step in ensuring a database is ready for effective management and querying.
