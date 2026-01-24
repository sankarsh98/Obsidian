# Boyce-Codd Normal Form (BCNF)

**Boyce-Codd Normal Form (BCNF)**, also known as 3.5NF, is a stricter version of [[Third Normal Form (3NF)|3NF]]. It addresses certain anomalies that 3NF might miss in tables with more than one [[Candidate Key]] where those candidate keys overlap. A table is in BCNF if and only if for every non-trivial functional dependency (FD) `X -> Y`, `X` is a superkey.

## Rules for BCNF

A table is in Boyce-Codd Normal Form if and only if:

1.  It is in [[Third Normal Form (3NF)|3NF]].
2.  For every functional dependency `X -> Y` (where `X` determines `Y`), `X` must be a [[Super Key]] of the table.

In essence, BCNF ensures that for any dependency `X -> Y`, `X` must be a [[Candidate Key]] (or include one) of the table. This means no non-key attribute can determine any part of a candidate key or other non-key attributes.

## Why BCNF is Stricter than 3NF

The difference between 3NF and BCNF lies in how they handle cases where a non-prime attribute (an attribute not part of any candidate key) determines part of a candidate key, or when one candidate key determines another candidate key.

*   **3NF**: Allows a functional dependency `X -> Y` where `X` is not a superkey, *provided* `Y` is a prime attribute (i.e., part of some candidate key).
*   **BCNF**: Does *not* allow such a dependency. If `X -> Y`, then `X` *must* be a superkey, regardless of whether `Y` is a prime attribute or not.

This stricter rule means BCNF resolves more anomalies, particularly in tables with multiple overlapping candidate keys.

## Example of a Table in 3NF but Not BCNF

Consider a `Student_Courses_Lecturers` table where:
*   Each student can enroll in multiple courses.
*   Each course is taught by multiple lecturers.
*   Each lecturer can teach multiple courses.
*   For a specific course, a student is assigned only one lecturer.
*   For a specific student, a course is taught by only one lecturer.
*   A lecturer teaches a specific course to only one student. (This last one is the unusual condition that makes it not BCNF.)

Functional Dependencies:
1.  `(Student, Course) -> Lecturer` (A student in a course has one lecturer)
2.  `(Lecturer, Course) -> Student` (A lecturer teaches a course to one student) - *This is the problematic one for BCNF*
3.  `Student -> Major` (A student has one major - for simplicity, let's assume `Major` is another non-key attribute)

Let's assume the table structure:

### Student_Courses_Lecturers (Not in BCNF)

| Student (PK) | Course (PK) | Lecturer | Major |
| :----------- | :---------- | :------- | :---- |
| Alice        | DB101       | Dr. Smith| CS    |
| Alice        | AI201       | Dr. Jones| CS    |
| Bob          | DB101       | Dr. Smith| Math  |
| Charlie      | AI201       | Dr. Jones| Physics|

Here, the composite **Primary Key** is `(Student, Course)`.

**Candidate Keys**:
*   `(Student, Course)`
*   `(Lecturer, Course)` (because a lecturer teaches a course to only one student, based on the FD 2)

**Analysis for BCNF Violation**:
Consider the functional dependency: `(Lecturer, Course) -> Student`.
*   `X = (Lecturer, Course)`
*   `Y = Student`

`X` (Lecturer, Course) is a candidate key, so `(Lecturer, Course)` is a superkey. This functional dependency actually satisfies BCNF on its own.

Let's re-evaluate the example for a clearer BCNF violation scenario, which typically occurs when a non-key attribute determines a *part* of a candidate key, or a non-key attribute determines another non-key attribute that then determines a candidate key.

A more classic example for 3NF but not BCNF often involves overlapping candidate keys where a non-prime attribute determines a candidate key, or a part of one.

Let's use a `Course_Professor_Head` table:
*   A course can have multiple professors.
*   A professor can teach multiple courses.
*   Each course has only one Head of Department.
*   Each professor works for only one Head of Department.
*   Assume for each course, there's a specific professor assigned to teach it.

Functional Dependencies:
1.  `(Course, Professor) -> HeadOfDepartment` (A specific professor for a course belongs to a specific head)
2.  `Professor -> HeadOfDepartment` (A professor has only one Head of Department)

**Candidate Keys**:
*   `(Course, Professor)`

### Course_Professor_Head (In 3NF, Not in BCNF)

| Course (PK) | Professor (PK) | HeadOfDepartment |
| :---------- | :------------- | :--------------- |
| CS101       | Dr. Smith      | Prof. White      |
| CS101       | Dr. Jones      | Prof. White      |
| MA201       | Dr. Green      | Prof. Black      |

Here, the Primary Key is `(Course, Professor)`.
All non-key attributes (`HeadOfDepartment`) are fully functionally dependent on the primary key, and there are no transitive dependencies (`HeadOfDepartment` doesn't determine anything else). So, it's in 3NF.

However, consider the functional dependency: `Professor -> HeadOfDepartment`.
*   `X = Professor`
*   `Y = HeadOfDepartment`

Is `Professor` a superkey? No, because a professor can teach multiple courses (`Dr. Smith` teaches `CS101` and potentially others not shown, but he is part of the `(Course, Professor)` PK and cannot uniquely identify the `Course` on its own). Since `X` (`Professor`) is not a superkey, this table is **not in BCNF**.

This leads to anomalies:
*   **Redundancy**: `HeadOfDepartment` is repeated for every course a professor teaches.
*   **Update Anomaly**: If `Dr. Smith`'s `HeadOfDepartment` changes, it must be updated in multiple rows.
*   **Insertion Anomaly**: We cannot record a `HeadOfDepartment` for a `Professor` unless they are teaching a course.

## Converting to BCNF

To convert to BCNF, we decompose the table into smaller tables, ensuring that for every functional dependency `X -> Y`, `X` is a superkey.

### In BCNF

We decompose the `Course_Professor_Head` table into `Course_Professor` and `Professor_Head`.

**Course_Professor Table (in BCNF):**

| Course (PK) | Professor (PK) |
| :---------- | :------------- |
| CS101       | Dr. Smith      |
| CS101       | Dr. Jones      |
| MA201       | Dr. Green      |

*   Primary Key: `(Course, Professor)`
*   Functional dependencies: `(Course, Professor) -> {}` (no non-key attributes here, so it satisfies BCNF)

**Professor_Head Table (in BCNF):**

| Professor (PK) | HeadOfDepartment |
| :------------- | :--------------- |
| Dr. Smith      | Prof. White      |
| Dr. Jones      | Prof. White      |
| Dr. Green      | Prof. Black      |

*   Primary Key: `Professor`
*   Functional dependency: `Professor -> HeadOfDepartment`
*   Here, `Professor` is a superkey. So, this table is in BCNF.

By decomposing the table, we've eliminated the redundancy and anomalies. `HeadOfDepartment` information is now stored only once per professor.

BCNF is generally the highest form of normalization considered practical for most transactional databases. Achieving BCNF often simplifies the database structure and ensures a high degree of data integrity, though it might sometimes lead to more tables and complex queries.
