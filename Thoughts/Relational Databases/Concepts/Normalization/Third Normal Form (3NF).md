# Third Normal Form (3NF)

**Third Normal Form (3NF)** is a significant step in database normalization, building upon [[Second Normal Form (2NF)|2NF]]. A table is in 3NF if it is in 2NF and all non-key attributes are non-transitively dependent on the primary key. In simpler terms, it eliminates transitive dependencies.

## Rules for 3NF

A table is in Third Normal Form if and only if:

1.  It is in [[Second Normal Form (2NF)|2NF]].
2.  There are no **transitive functional dependencies** for non-prime attributes. This means that a non-key attribute should not be functionally dependent on another non-key attribute. In other words, all non-key attributes must directly depend on the primary key, and nothing else.

## Transitive Functional Dependency

A transitive functional dependency occurs when:
*   `A -> B` and `B -> C` (where `A` is the primary key or a [[Candidate Key]], and `B` and `C` are non-key attributes)
*   Then `A -> C` is a transitive dependency.

This situation implies that `C` indirectly depends on `A` through `B`. 3NF aims to remove such indirect dependencies.

## Why 3NF is Important

Achieving 3NF further reduces data redundancy and prevents various types of anomalies:

*   **Reduced Redundancy**: Information that is not directly dependent on the primary key is moved to its own table, reducing duplication.
*   **Prevent Update Anomalies**: Changing a non-key attribute that is transitively dependent on the primary key only requires updating one place.
*   **Prevent Deletion Anomalies**: Deleting a record does not inadvertently remove information about other non-key attributes that are not directly related to the primary key.
*   **Better Data Integrity**: Ensures that each piece of information is stored logically and consistently.

## Example of Not in 3NF

Consider a `Books` table that stores information about books, including their publisher and publisher's country. Assume `BookID` is the primary key.

### Not in 3NF

| BookID (PK) | BookTitle            | Author     | PublisherID | PublisherName  | PublisherCountry |
| :---------- | :------------------- | :--------- | :---------- | :------------- | :--------------- |
| 101         | The Hitchhiker's Guide | Douglas A. | P001        | Pan Books      | UK               |
| 102         | 1984                 | George O.  | P002        | Secker & Warburg| UK               |
| 103         | The Great Gatsby     | F. Scott F.| P003        | Charles S.     | USA              |

Here, `BookID` is the primary key.
Let's analyze functional dependencies:

*   `BookID -> BookTitle, Author, PublisherID, PublisherName, PublisherCountry` (all attributes depend on `BookID`)
*   `PublisherID -> PublisherName, PublisherCountry` (PublisherName and PublisherCountry depend only on PublisherID)

The transitive dependency here is:
`BookID -> PublisherID` and `PublisherID -> PublisherName, PublisherCountry`.
Therefore, `BookID -> PublisherName` and `BookID -> PublisherCountry` are transitive dependencies through `PublisherID`.

This table is in 2NF (assuming `BookID` is a simple primary key, or if it's composite, no non-key attribute depends on only part of it). However, it is not in 3NF due to the transitive dependencies:
*   `PublisherName` and `PublisherCountry` depend on `PublisherID`, which is a non-key attribute, rather than directly on the `BookID` primary key.

This leads to anomalies:
*   **Redundancy**: `PublisherName` and `PublisherCountry` are repeated for every book by the same publisher.
*   **Update Anomaly**: If a publisher changes its name or country, we would need to update multiple rows. If one is missed, data becomes inconsistent.
*   **Insertion Anomaly**: We cannot add a new publisher to the database unless they have published at least one book.
*   **Deletion Anomaly**: If we delete all books by a particular publisher, we lose all information about that publisher.

## Converting to 3NF

To convert the table to 3NF, we remove the transitively dependent attributes and place them in a new table, linked by a foreign key.

### In 3NF

We split the original table into two tables: `Books` and `Publishers`.

**Books Table (now in 3NF):**

| BookID (PK) | BookTitle            | Author     | PublisherID (FK) |
| :---------- | :------------------- | :--------- | :--------------- |
| 101         | The Hitchhiker's Guide | Douglas A. | P001             |
| 102         | 1984                 | George O.  | P002             |
| 103         | The Great Gatsby     | F. Scott F.| P003             |

*   `BookID` is the primary key.
*   `BookTitle`, `Author`, and `PublisherID` are directly dependent on `BookID`.
*   `PublisherID` is a [[Foreign Key]] referencing the `Publishers` table.

**Publishers Table (now in 3NF):**

| PublisherID (PK) | PublisherName   | PublisherCountry |
| :--------------- | :-------------- | :--------------- |
| P001             | Pan Books       | UK               |
| P002             | Secker & Warburg| UK               |
| P003             | Charles S.      | USA              |

*   `PublisherID` is the primary key.
*   `PublisherName` and `PublisherCountry` are directly dependent on `PublisherID`.

Now, both tables are in 3NF. Redundancy for publisher information is eliminated, and we can manage publisher details independently of books. We can add a publisher without any books, or change a publisher's country in one place.

3NF is generally considered a good balance between data integrity and performance for many transactional database systems.
