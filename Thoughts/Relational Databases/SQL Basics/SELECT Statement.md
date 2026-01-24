# SELECT Statement

The `SELECT` statement in SQL is a Data Manipulation Language (DML) command used to retrieve data from one or more [[Tables|tables]] in a database. It is one of the most frequently used SQL commands and is fundamental for querying data.

## Basic Syntax

The most basic form of a `SELECT` statement includes specifying the columns you want to retrieve and the table from which to retrieve them.

```sql
SELECT column1, column2, ...
FROM table_name;
```

*   **`column1, column2, ...`**: The names of the columns you want to retrieve.
*   **`FROM table_name`**: The name of the table from which to retrieve the data.

### Retrieving All Columns

To select all columns from a table, you can use the asterisk (`*`) wildcard:

```sql
SELECT *
FROM table_name;
```

## Clauses Used with SELECT

The `SELECT` statement can be combined with various clauses to filter, sort, group, and join data, allowing for highly complex and specific queries.

### 1. WHERE Clause (Filtering Rows)

The `WHERE` clause is used to filter records based on specified conditions. It appears after the `FROM` clause.

```sql
SELECT column1, column2
FROM table_name
WHERE condition;
```

*   **`condition`**: An expression that evaluates to true, false, or unknown. Only rows where the condition is true are returned.

**Example**: Select customers from 'New York'
```sql
SELECT FirstName, LastName
FROM Customers
WHERE City = 'New York';
```

### 2. ORDER BY Clause (Sorting Results)

The `ORDER BY` clause is used to sort the result-set in ascending (ASC) or descending (DESC) order based on one or more columns. `ASC` is the default.

```sql
SELECT column1, column2
FROM table_name
ORDER BY column1 ASC|DESC, column2 ASC|DESC;
```

**Example**: Select products, ordered by price (descending) and then by name (ascending)
```sql
SELECT ProductName, Price
FROM Products
ORDER BY Price DESC, ProductName ASC;
```

### 3. LIMIT Clause (Restricting Number of Rows)

The `LIMIT` clause (or `TOP` for SQL Server, `ROWNUM` for Oracle) is used to restrict the number of rows returned by the query.

```sql
SELECT column1, column2
FROM table_name
LIMIT number_of_rows OFFSET offset_value; -- Standard SQL, often variations
```

**Example**: Select the top 5 most expensive products
```sql
SELECT ProductName, Price
FROM Products
ORDER BY Price DESC
LIMIT 5;
```

### 4. GROUP BY Clause (Grouping Rows)

The `GROUP BY` clause groups rows that have the same values in specified columns into summary rows, typically with aggregate functions (like `COUNT`, `MAX`, `MIN`, `SUM`, `AVG`).

```sql
SELECT column1, aggregate_function(column2)
FROM table_name
GROUP BY column1;
```

**Example**: Count customers per city
```sql
SELECT City, COUNT(CustomerID) AS NumberOfCustomers
FROM Customers
GROUP BY City;
```

### 5. HAVING Clause (Filtering Groups)

The `HAVING` clause is used to filter groups created by the `GROUP BY` clause. It's similar to `WHERE` but operates on grouped data after aggregation.

```sql
SELECT column1, aggregate_function(column2)
FROM table_name
GROUP BY column1
HAVING condition_on_aggregate;
```

**Example**: Count customers per city, only for cities with more than 2 customers
```sql
SELECT City, COUNT(CustomerID) AS NumberOfCustomers
FROM Customers
GROUP BY City
HAVING COUNT(CustomerID) > 2;
```

### 6. JOIN Clauses (Combining Tables)

`JOIN` clauses are used to combine rows from two or more tables based on a related column between them.
Common types include `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, and `FULL JOIN`.

```sql
SELECT Customers.FirstName, Customers.LastName, Orders.OrderID
FROM Customers
INNER JOIN Orders ON Customers.CustomerID = Orders.CustomerID;
```

**Example**: List customer names and their orders
```sql
SELECT C.FirstName, C.LastName, O.OrderID, O.OrderDate
FROM Customers AS C
INNER JOIN Orders AS O ON C.CustomerID = O.CustomerID
ORDER BY C.LastName, O.OrderDate;
```
*Aliases `C` and `O` are used for brevity.*

## Importance of SELECT

The `SELECT` statement is the heart of database querying. Mastering its various clauses and combinations allows users to extract precisely the information they need from a database, making it an indispensable tool for data analysis, reporting, and application development.
