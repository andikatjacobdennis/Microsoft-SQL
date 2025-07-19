# Module SQLSUB01: **Subqueries**

> **Module Objective**
> This module aims to provide a thorough understanding of subqueries in T-SQL. Learners will master how to embed queries within other SQL statements, enabling them to retrieve complex, interdependent data efficiently and solve problems that cannot be addressed with simple joins.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Define what a subquery is and explain its role in T-SQL.
2.  Differentiate between independent (non-correlated) and correlated subqueries.
3.  Utilize subqueries effectively in the `SELECT` clause (scalar subqueries).
4.  Implement subqueries in the `WHERE` clause using comparison operators and `IN`, `NOT IN`, `EXISTS`, `NOT EXISTS` predicates.
5.  Employ subqueries in the `FROM` clause (derived tables).
6.  Understand the performance implications and best practices for using subqueries.

## Reference Materials

  - **Books:**
      - *T-SQL Fundamentals* by Itzik Ben-Gan
      - *SQL Server 2019 Querying (Exam 70-761)* by Grant Fritchey and Kevin Boles
  - **Online Resources:**
      - Subqueries (SQL Server) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/relational-databases/subqueries/subqueries?view=sql-server-ver16](https://www.google.com/search?q=https://learn.microsoft.com/en-us/sql/relational-databases/subqueries/subqueries%3Fview%3Dsql-server-ver16))
      - SQL Subquery (W3Schools) ([https://www.w3schools.com/sql/sql\_subquery.asp](https://www.google.com/search?q=https://www.w3schools.com/sql/sql_subquery.asp))
      - SQL Server Subquery: Beginner's Guide (SQLShack) ([https://www.sqlshack.com/sql-server-subquery-beginners-guide/](https://www.google.com/search?q=https://www.sqlshack.com/sql-server-subquery-beginners-guide/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to Subqueries**

  - **Definition**: A subquery (or inner query) is a query nested inside another SQL query (outer query). The subquery executes first, and its result is then used by the outer query. Subqueries can appear in various clauses of a `SELECT`, `INSERT`, `UPDATE`, or `DELETE` statement.
  - **Purpose**:
      - **Complex Filtering**: Filter data based on results from another query.
      - **Value Lookup**: Retrieve single values or lists of values.
      - **Derived Tables**: Treat the result set of a subquery as a temporary table.
      - **Existence Checks**: Check for the existence of related rows.
  - **Types**:
      - **Independent (Non-Correlated) Subquery**:
          - **Description**: A subquery that can be executed independently of the outer query. It runs once and returns a result set that the outer query then uses.
          - **Characteristics**: Does not reference any columns from the outer query.
      - **Correlated Subquery**:
          - **Description**: A subquery that depends on the outer query for its values. It executes once for *each row* processed by the outer query.
          - **Characteristics**: References one or more columns from the outer query.

-----

### 2\. **Subqueries in `SELECT`, `FROM`, and `WHERE` Clauses**

For demonstration, let's consider two simple tables:

**`Employees` Table:**
| EmployeeID | Name    | DepartmentID | Salary  |
| :--------- | :------ | :----------- | :------ |
| 1          | Alice   | 10           | 60000   |
| 2          | Bob     | 20           | 75000   |
| 3          | Charlie | 10           | 50000   |
| 4          | David   | 30           | 90000   |
| 5          | Eve     | 20           | 65000   |

**`Departments` Table:**
| DepartmentID | DepartmentName |
| :----------- | :------------- |
| 10           | HR             |
| 20           | IT             |
| 30           | Sales          |
| 40           | Marketing      |

#### 2.1. **Scalar Subqueries (in `SELECT` Clause)**

  - **Definition**: A subquery that returns a single value (one row, one column). It can be used anywhere a single value expression is expected.
  - **Use Cases**: Retrieving aggregated values for each row, or looking up a single related value.
  - **Example**: Get employee names and their department names. (Can also be done with `JOIN`)
    ```sql
    SELECT
        E.Name,
        (SELECT D.DepartmentName FROM Departments AS D WHERE D.DepartmentID = E.DepartmentID) AS DepartmentName
    FROM Employees AS E;
    ```
  - **Result**:
    | Name    | DepartmentName |
    | :------ | :------------- |
    | Alice   | HR             |
    | Bob     | IT             |
    | Charlie | HR             |
    | David   | Sales          |
    | Eve     | IT             |
  - **Note**: If the subquery returns more than one value, it will result in an error ("Subquery returned more than 1 value."). If it returns no value, `NULL` is returned.

#### 2.2. **Subqueries in `WHERE` Clause**

  - **Definition**: Used to filter the rows returned by the outer query based on conditions involving the subquery's result.
  - **Types of Operators**:
      - **Comparison Operators (`=`, `!=`, `>`, `<`, `>=`, `<=`)**: Used when the subquery returns a single value.
          - **Example**: Find employees earning more than the average salary.
            ```sql
            SELECT Name, Salary
            FROM Employees
            WHERE Salary > (SELECT AVG(Salary) FROM Employees);
            ```
          - **Result (assuming average salary is 68000)**:
            | Name  | Salary |
            | :---- | :----- |
            | Bob   | 75000  |
            | David | 90000  |
      - **`IN` / `NOT IN`**: Used when the subquery returns a list of values.
          - **Example (`IN`)**: Find employees who are in departments 'HR' or 'IT'.
            ```sql
            SELECT Name, DepartmentID
            FROM Employees
            WHERE DepartmentID IN (SELECT DepartmentID FROM Departments WHERE DepartmentName IN ('HR', 'IT'));
            ```
          - **Result**:
            | Name    | DepartmentID |
            | :------ | :----------- |
            | Alice   | 10           |
            | Bob     | 20           |
            | Charlie | 10           |
            | Eve     | 20           |
          - **Example (`NOT IN`)**: Find employees not in 'HR' or 'IT'.
            ```sql
            SELECT Name, DepartmentID
            FROM Employees
            WHERE DepartmentID NOT IN (SELECT DepartmentID FROM Departments WHERE DepartmentName IN ('HR', 'IT'));
            ```
          - **Note**: `NOT IN` behaves unpredictably with `NULL` values in the subquery result. If the subquery returns `NULL`, no rows will be returned by the outer query.
      - **`EXISTS` / `NOT EXISTS`**: Used for correlated subqueries to check for the existence of rows. They return `TRUE` if the subquery returns any rows, and `FALSE` otherwise. They are often more efficient than `IN` for large datasets.
          - **Example (`EXISTS`)**: Find departments that have at least one employee.
            ```sql
            SELECT DepartmentName
            FROM Departments AS D
            WHERE EXISTS (SELECT 1 FROM Employees AS E WHERE E.DepartmentID = D.DepartmentID);
            ```
          - **Result**:
            | DepartmentName |
            | :------------- |
            | HR             |
            | IT             |
            | Sales          |
          - **Example (`NOT EXISTS`)**: Find departments that have no employees.
            ```sql
            SELECT DepartmentName
            FROM Departments AS D
            WHERE NOT EXISTS (SELECT 1 FROM Employees AS E WHERE E.DepartmentID = D.DepartmentID);
            ```
          - **Result**:
            | DepartmentName |
            | :------------- |
            | Marketing      |
          - **Note**: `EXISTS` and `NOT EXISTS` subqueries typically return `TRUE` or `FALSE` and don't actually retrieve data; hence `SELECT 1` is commonly used.

#### 2.3. **Derived Tables (in `FROM` Clause)**

  - **Definition**: A subquery placed in the `FROM` clause that produces a temporary, unnamed result set that can be queried like a regular table. It must be given an alias.
  - **Use Cases**:
      - Pre-aggregating data before joining.
      - Simplifying complex queries by breaking them into logical steps.
      - Performing operations that require a "snapshot" of data.
  - **Example**: Get department names and the count of employees in each department who earn above the department's average salary.
    ```sql
    SELECT
        D.DepartmentName,
        EmpCount.HighEarnerCount
    FROM Departments AS D
    INNER JOIN (
        SELECT
            E.DepartmentID,
            COUNT(E.EmployeeID) AS HighEarnerCount
        FROM Employees AS E
        WHERE E.Salary > (SELECT AVG(Salary) FROM Employees WHERE DepartmentID = E.DepartmentID)
        GROUP BY E.DepartmentID
    ) AS EmpCount ON D.DepartmentID = EmpCount.DepartmentID;
    ```
  - **Result**:
    | DepartmentName | HighEarnerCount |
    | :------------- | :-------------- |
    | IT             | 1               |
    | Sales          | 1               |
  - **Diagram**:
    ```
    Outer Query
    +-------------------+
    |                   |
    | SELECT ...        |
    | FROM TableA       |
    | JOIN ( Subquery ) | <-- This subquery acts as a temporary table
    |      AS Alias     |
    | ON ...            |
    +-------------------+
    ```

-----

### 3\. **Performance and Best Practices**

#### 3.1. **Performance Considerations**

  - **Correlated Subqueries**: Can be less efficient than joins, especially on large datasets, because they execute once for *every row* of the outer query. However, for `EXISTS`/`NOT EXISTS` or complex row-by-row logic, they might be the most straightforward or only solution.
  - **Independent Subqueries (Comparison Operators/IN)**: Generally perform well as they execute once. However, `IN` with a very large list of values can be slow. `EXISTS` often performs better than `IN` if the subquery involves joins or aggregations, as `EXISTS` stops processing as soon as it finds the first match.
  - **Derived Tables**: Often very efficient as the optimizer can build an execution plan for the derived table first, then use that result for the outer query. This is a common and often preferred alternative to complex joins.
  - **Alternatives to Subqueries**: Many subqueries can be rewritten as `JOIN` operations (especially `INNER JOIN` and `LEFT JOIN`), Common Table Expressions (CTEs), or window functions, which often lead to more optimized execution plans and better readability.

#### 3.2. **Best Practices for Using Subqueries**

  - **Use Aliases**: Always alias derived tables and tables in subqueries for clarity and to avoid ambiguity.
  - **Keep it Simple**: Break down complex queries into smaller, more manageable subqueries or CTEs.
  - **Evaluate Alternatives**: Before settling on a subquery, consider if a `JOIN` or a CTE would offer better performance or readability.
  - **Avoid `NOT IN` with NULLs**: Be very careful when using `NOT IN` if the subquery's result set might contain `NULL` values. It's often safer to use `NOT EXISTS` or handle `NULL`s explicitly.
  - **Test Performance**: Always test the performance of queries with subqueries on representative data volumes, as their efficiency can vary greatly depending on the scenario and data.
  - **Choose `EXISTS` over `IN` for existence checks**: When you only need to check for the *existence* of a row rather than comparing values, `EXISTS` is usually more efficient because it can stop as soon as it finds one match.

#### 3.3. **Case Study: Finding Duplicates with Subqueries**

  - **Problem**: Identify customers who have duplicate email addresses.
  - **Solution with Subquery**:
    ```sql
    -- Assume Customers table has CustomerID, Name, Email
    SELECT C1.CustomerID, C1.Name, C1.Email
    FROM Customers AS C1
    WHERE C1.Email IN (
        SELECT Email
        FROM Customers
        GROUP BY Email
        HAVING COUNT(*) > 1
    );
    ```
  - **Explanation**: The inner subquery finds all email addresses that appear more than once. The outer query then retrieves all customer details for those duplicate email addresses. This clearly demonstrates how a subquery can simplify a problem that might otherwise require more complex joins or temporary tables.

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create an `Orders` table with `OrderID`, `CustomerID`, `OrderDate`, `TotalAmount`. Insert data. Use a scalar subquery in the `SELECT` list to display `OrderID`, `TotalAmount`, and the `AverageOrderAmount` across all orders. | SSMS / Azure Data Studio | Query returns order details with global average.                     |
| 2   | Use a subquery with `IN` to find `Customers` who have placed orders with an `Amount` greater than $500. | SSMS / Azure Data Studio | Query returns customer details filtered by order amount.             |
| 3   | Use a correlated subquery with `EXISTS` to find all `Departments` that have at least one `Employee`. | SSMS / Azure Data Studio | Query returns only departments with employees.                       |
| 4   | Create a derived table (subquery in `FROM`) to find the `DepartmentID`s with an `AverageSalary` greater than $60,000. Then join this derived table with `Departments` to display `DepartmentName` and `AverageSalary`. | SSMS / Azure Data Studio | Query accurately displays departments based on aggregated average salary. |
| 5   | Rewrite a query that uses `NOT IN` (with potential `NULL` issues) to use `NOT EXISTS` for safer filtering. | SSMS / Azure Data Studio | Query returns correct results, unaffected by `NULL` values.          |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which type of subquery executes once for each row processed by the outer query?**

      - A) Scalar subquery
      - B) Independent subquery
      - C) Derived table
      - D) Correlated subquery
      - **Answer:** D) Correlated subquery
      - **Explanation:** A correlated subquery depends on the outer query and re-executes for every row of the outer query.

2.  **A subquery used in the `FROM` clause is commonly referred to as what?**

      - A) A join condition
      - B) A derived table
      - C) A scalar function
      - D) An aggregated query
      - **Answer:** B) A derived table
      - **Explanation:** A subquery in the `FROM` clause creates a temporary, unnamed result set that acts like a table, hence the term "derived table".

3.  **Which operator is generally preferred over `IN` when you only need to check for the existence of related rows in a subquery, especially if `NULL` values might be present in the subquery's result?**

      - A) `=`
      - B) `ANY`
      - C) `EXISTS`
      - D) `ALL`
      - **Answer:** C) `EXISTS`
      - **Explanation:** `EXISTS` is often more efficient for existence checks and handles `NULL`s gracefully, unlike `IN` when the subquery can return `NULL`.

### Short Answer Questions

1.  Explain the key difference in execution flow between an independent subquery and a correlated subquery.
2.  Describe a situation where a scalar subquery would be useful in the `SELECT` list.
3.  Discuss why `NOT IN` might produce unexpected results if the subquery used with it returns `NULL` values, and what alternative should be considered.

### Practical Task

  - **Setup**: Create two tables:
      - `Products`: `ProductID` (INT PK), `ProductName` (NVARCHAR(100)), `Category` (NVARCHAR(50)), `Price` (DECIMAL(10,2))
          - Data: (1, 'Laptop', 'Electronics', 1200.00), (2, 'Mouse', 'Electronics', 25.00), (3, 'Keyboard', 'Electronics', 75.00), (4, 'T-Shirt', 'Apparel', 20.00), (5, 'Jeans', 'Apparel', 60.00), (6, 'SQL Book', 'Books', 45.00)
      - `OrderItems`: `OrderItemID` (INT PK), `OrderID` (INT), `ProductID` (INT FK), `Quantity` (INT)
          - Data: (1, 101, 1, 1), (2, 101, 2, 2), (3, 102, 4, 3), (4, 103, 1, 1), (5, 103, 3, 1)
  - **Tasks**:
    1.  **Scalar Subquery for Category Average Price**:
          - Write a query that lists `ProductName`, `Price`, and the `AveragePrice` of all products in the same `Category` as the current product. (This will be a correlated scalar subquery).
    2.  **Products Sold (IN/EXISTS)**:
          - Write a query using `IN` to list `ProductID` and `ProductName` for all products that have been sold at least once (i.e., exist in `OrderItems`).
          - Rewrite the same query using `EXISTS`.
    3.  **Products Never Sold (NOT IN/NOT EXISTS)**:
          - Write a query using `NOT IN` to list `ProductID` and `ProductName` for all products that have *never* been sold. Test this with and without `NULL` `ProductID`s in `OrderItems` (if you can simulate it, though `FK` prevents this normally).
          - Rewrite the same query using `NOT EXISTS`.
    4.  **Top N per Group (Derived Table with `ROW_NUMBER`)**:
          - Using a derived table with `ROW_NUMBER()`, find the top 2 most expensive products in each `Category`.
    5.  **Clean up**: Drop the `OrderItems` and `Products` tables.
