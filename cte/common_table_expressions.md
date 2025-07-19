# Module SQLCTE01: **Common Table Expressions (CTEs)**

> **Module Objective**
> This module aims to introduce learners to Common Table Expressions (CTEs) in T-SQL. Learners will understand how to use CTEs to improve query readability, simplify complex subqueries, and perform recursive queries, thereby enhancing their ability to write more organized and powerful SQL code.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Define a CTE and explain its benefits over derived tables or complex subqueries.
2.  Construct simple CTEs using the `WITH` clause.
3.  Implement recursive CTEs to traverse hierarchical data.
4.  Define and use multiple CTEs within a single query.
5.  Apply CTEs with `INSERT`, `UPDATE`, and `DELETE` statements.
6.  Understand the scope and limitations of CTEs.

## Reference Materials

  - **Books:**
      - *T-SQL Fundamentals* by Itzik Ben-Gan
      - *SQL Server 2019 Querying (Exam 70-761)* by Grant Fritchey and Kevin Boles
  - **Online Resources:**
      - WITH common\_table\_expression (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver16))
      - SQL CTE (Common Table Expression) (W3Schools) ([https://www.w3schools.com/sql/sql\_cte.asp](https://www.google.com/search?q=https://www.w3schools.com/sql/sql_cte.asp))
      - SQL Server CTE (Common Table Expression) (SQLShack) ([https://www.sqlshack.com/sql-server-cte-common-table-expression/](https://www.google.com/search?q=https://www.sqlshack.com/sql-server-cte-common-table-expression/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to Common Table Expressions (CTEs)**

  - **Definition**: A Common Table Expression (CTE) is a temporary, named result set that you can reference within a single `SELECT`, `INSERT`, `UPDATE`, `DELETE`, or `MERGE` statement. It's defined using the `WITH` clause. CTEs are not stored as objects in the database; they only exist for the duration of the query execution.
  - **Purpose and Benefits**:
      - **Readability**: Break down complex, multi-step queries into logical, readable steps.
      - **Simplicity**: Simplify complex joins and subqueries, especially nested ones.
      - **Recursion**: Enable recursive queries to traverse hierarchical data (e.g., organizational charts, bill of materials).
      - **Reusability (within a single query)**: A CTE can be referenced multiple times within the same query.
      - **Alternative to Views/Derived Tables**: Can often replace complex derived tables or provide a more structured approach than subqueries.
  - **Key Characteristics**:
      - Must be followed immediately by the `SELECT`, `INSERT`, `UPDATE`, `DELETE`, or `MERGE` statement that references it.
      - Can refer to itself (for recursive CTEs) or to previously defined CTEs within the same `WITH` clause.
      - Cannot be indexed directly.

-----

### 2\. **Basic and Recursive CTEs**

#### 2.1. **`WITH` Clause (Non-Recursive CTEs)**

  - **Definition**: The fundamental syntax for defining one or more CTEs. Each CTE is essentially a named subquery.
  - **Syntax**:
    ```sql
    WITH CTE_Name (Column1, Column2, ...) AS (
        SELECT ... -- Your SELECT statement defining the CTE
    )
    -- Now you can reference CTE_Name in your main query
    SELECT ...
    FROM CTE_Name
    WHERE ...;
    ```
  - **Example**: Find employees earning above the average salary in their department.
    ```sql
    -- Assume Employees table: EmployeeID, Name, DepartmentID, Salary
    -- Assume Departments table: DepartmentID, DepartmentName

    WITH DepartmentAvgSalary AS (
        SELECT
            DepartmentID,
            AVG(Salary) AS AvgDeptSalary
        FROM Employees
        GROUP BY DepartmentID
    )
    SELECT
        E.Name,
        E.Salary,
        D.DepartmentName,
        DAS.AvgDeptSalary
    FROM Employees AS E
    JOIN Departments AS D ON E.DepartmentID = D.DepartmentID
    JOIN DepartmentAvgSalary AS DAS ON E.DepartmentID = DAS.DepartmentID
    WHERE E.Salary > DAS.AvgDeptSalary;
    ```
  - **Comparison to Derived Tables**: CTEs offer better readability and can be referenced multiple times, unlike derived tables which are typically defined and used once.

#### 2.2. **Recursive CTEs**

  - **Definition**: A special type of CTE that refers to itself. This allows for traversing hierarchical data structures where relationships are defined within the same table (e.g., organizational charts, bill of materials, network paths).
  - **Structure**: A recursive CTE has two main parts, separated by `UNION ALL`:
      - **Anchor Member**: The initial query that establishes the base result set for the recursion. This part is executed only once.
      - **Recursive Member**: The query that references the CTE itself. This part is executed repeatedly until no more rows are returned.
      - **Termination Condition**: Implicitly, the recursion stops when the recursive member returns an empty result set. It's crucial to ensure this condition is met to prevent infinite loops.
  - **Syntax**:
    ```sql
    WITH RecursiveCTE_Name (Column1, Column2, ..., Level) AS (
        -- Anchor Member (initial query)
        SELECT Column1, Column2, ..., 1 AS Level
        FROM BaseTable
        WHERE initial_condition

        UNION ALL

        -- Recursive Member (references RecursiveCTE_Name)
        SELECT T.Column1, T.Column2, ..., R.Level + 1
        FROM BaseTable AS T
        JOIN RecursiveCTE_Name AS R ON T.ParentID = R.ChildID -- Or similar join condition
        WHERE recursive_condition
    )
    SELECT * FROM RecursiveCTE_Name;
    ```
  - **Example**: Get the organizational hierarchy for an employee (assuming `Employees` table has `EmployeeID` and `ManagerID`).
    ```sql
    -- Employees table: EmployeeID, Name, ManagerID
    -- (1, 'CEO', NULL)
    -- (2, 'Manager A', 1)
    -- (3, 'Employee X', 2)
    -- (4, 'Manager B', 1)
    -- (5, 'Employee Y', 4)

    WITH EmployeeHierarchy AS (
        -- Anchor Member: Start with the top-level employee (CEO)
        SELECT
            EmployeeID,
            Name,
            ManagerID,
            0 AS HierarchyLevel -- Level 0 for the top manager
        FROM Employees
        WHERE ManagerID IS NULL -- Or a specific EmployeeID to start from

        UNION ALL

        -- Recursive Member: Get direct reports for each employee found so far
        SELECT
            E.EmployeeID,
            E.Name,
            E.ManagerID,
            EH.HierarchyLevel + 1
        FROM Employees AS E
        JOIN EmployeeHierarchy AS EH ON E.ManagerID = EH.EmployeeID
    )
    SELECT *
    FROM EmployeeHierarchy
    ORDER BY HierarchyLevel, EmployeeID;
    ```
  - **Diagram**:
    ```
    +-------------------+
    | Anchor Member     |
    | (Base Query)      |
    +-------------------+
            |
            V
    +-------------------+
    | UNION ALL         |
    +-------------------+
            |
            V
    +-------------------+
    | Recursive Member  | <---+
    | (References CTE)  |     |
    +-------------------+-----+
    ```

-----

### 3\. **Multiple CTEs and CTEs with DML**

#### 3.1. **Multiple CTEs**

  - **Definition**: You can define multiple CTEs within a single `WITH` clause. Each subsequent CTE can reference any previously defined CTEs within the same `WITH` block.
  - **Syntax**:
    ```sql
    WITH CTE_Name1 AS (
        SELECT ...
    ),
    CTE_Name2 AS ( -- Can reference CTE_Name1
        SELECT ... FROM CTE_Name1 ...
    ),
    CTE_Name3 AS ( -- Can reference CTE_Name1, CTE_Name2
        SELECT ... FROM CTE_Name1 JOIN CTE_Name2 ...
    )
    SELECT ... FROM CTE_Name3 ...;
    ```
  - **Use Case**: Breaking down a very complex query into multiple logical, sequential steps.
  - **Example**: Find departments with high average salary and list employees in those departments.
    ```sql
    WITH DepartmentAvgSalary AS (
        SELECT
            DepartmentID,
            AVG(Salary) AS AvgSalary
        FROM Employees
        GROUP BY DepartmentID
    ),
    HighSalaryDepartments AS (
        SELECT
            DepartmentID,
            AvgSalary
        FROM DepartmentAvgSalary
        WHERE AvgSalary > 70000 -- Threshold for high salary
    )
    SELECT
        E.Name,
        E.Salary,
        D.DepartmentName,
        HSD.AvgSalary AS DepartmentAvgSalary
    FROM Employees AS E
    JOIN Departments AS D ON E.DepartmentID = D.DepartmentID
    JOIN HighSalaryDepartments AS HSD ON E.DepartmentID = HSD.DepartmentID;
    ```

#### 3.2. **Use with `INSERT`/`UPDATE`/`DELETE`**

  - **Definition**: CTEs can be used as the source for `INSERT` statements, or as the target/source for `UPDATE` and `DELETE` statements. This improves readability and allows for complex DML operations.
  - **`INSERT` Example**: Insert data into an `AuditLog` table based on a filtered set of customers.
    ```sql
    -- Assume Customers table and AuditLog (CustomerID, Action, LogDate)
    WITH NewCustomers AS (
        SELECT CustomerID, FirstName, LastName
        FROM Customers
        WHERE SignUpDate >= DATEADD(month, -1, GETDATE())
    )
    INSERT INTO AuditLog (CustomerID, Action, LogDate)
    SELECT CustomerID, 'New Customer Added', GETDATE()
    FROM NewCustomers;
    ```
  - **`UPDATE` Example**: Update salaries for employees in a specific department based on a calculated factor.
    ```sql
    WITH EmployeesToUpdate AS (
        SELECT EmployeeID, Salary
        FROM Employees
        WHERE DepartmentID = 101
    )
    UPDATE E
    SET Salary = E.Salary * 1.10 -- 10% raise
    FROM Employees AS E
    JOIN EmployeesToUpdate AS ETU ON E.EmployeeID = ETU.EmployeeID;
    ```
  - **`DELETE` Example**: Delete old, inactive orders.
    ```sql
    WITH OldInactiveOrders AS (
        SELECT OrderID
        FROM Orders
        WHERE OrderDate < DATEADD(year, -2, GETDATE())
          AND OrderStatus = 'Completed'
    )
    DELETE FROM Orders
    WHERE OrderID IN (SELECT OrderID FROM OldInactiveOrders);
    ```
  - **Note**: When using CTEs with `UPDATE` or `DELETE`, the CTE itself does not perform the DML operation. It simply defines the set of rows that the DML statement will operate on.

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create an `Employees` table with `EmployeeID`, `Name`, `DepartmentID`, `Salary`. Use a simple CTE to find employees earning more than the overall company average salary. | SSMS / Azure Data Studio | Query returns employees above average salary.                         |
| 2   | Create an `Employees` table with `EmployeeID`, `Name`, `ManagerID`. Use a recursive CTE to display the full reporting hierarchy for a specific employee. | SSMS / Azure Data Studio | Hierarchy is correctly displayed with levels.                         |
| 3   | Define multiple CTEs in a single query. The first CTE calculates `TotalSales` per `CustomerID`. The second CTE then identifies `HighValueCustomers` (e.g., `TotalSales > 1000`). The final `SELECT` uses the second CTE. | SSMS / Azure Data Studio | Query effectively uses multiple CTEs for complex filtering.           |
| 4   | Use a CTE with an `UPDATE` statement to give a 5% raise to all employees in a specific department. | SSMS / Azure Data Studio | Employee salaries are updated based on CTE's filtered set.          |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which clause is used to define a Common Table Expression (CTE)?**

      - A) `DEFINE CTE`
      - B) `CREATE TEMP TABLE`
      - C) `WITH`
      - D) `LET`
      - **Answer:** C) `WITH`
      - **Explanation:** CTEs are defined using the `WITH` clause at the beginning of a `SELECT`, `INSERT`, `UPDATE`, `DELETE`, or `MERGE` statement.

2.  **What are the two main parts of a recursive CTE?**

      - A) `SELECT` and `FROM`
      - B) `ANCHOR` and `RECURSIVE`
      - C) `BASE` and `ITERATION`
      - D) Anchor member and Recursive member
      - **Answer:** D) Anchor member and Recursive member
      - **Explanation:** A recursive CTE consists of an anchor member (the initial query) and a recursive member (the query that references the CTE itself).

3.  **A CTE is a temporary, named result set that exists for how long?**

      - A) Until the database server is restarted.
      - B) Until the user's session ends.
      - C) For the duration of the single query in which it is defined.
      - D) Until explicitly dropped by a `DROP CTE` statement.
      - **Answer:** C) For the duration of the single query in which it is defined.
      - **Explanation:** CTEs are temporary and are only valid for the single statement immediately following their definition. They are not stored objects.

### Short Answer Questions

1.  Explain how a CTE can improve the readability of a complex SQL query compared to using nested subqueries.
2.  Describe a real-world scenario where a recursive CTE would be the ideal solution.
3.  Can a CTE be referenced by a subsequent query in the same batch but outside the `WITH` clause? Explain why or why not.

### Practical Task

  - **Setup**: Create an `Employees` table with `EmployeeID` (INT PK), `Name` (NVARCHAR(100)), `ManagerID` (INT NULL, FK to EmployeeID), `Salary` (DECIMAL(10,2)), `DepartmentID` (INT).
  - **Insert sample data**: Create a small hierarchy (e.g., CEO, Managers, Employees under managers). Ensure some employees have `NULL` `ManagerID` (CEO).
  - **Tasks**:
    1.  **Recursive CTE for Reporting Chain**:
          - Write a recursive CTE to find the full reporting chain for a specific employee (e.g., EmployeeID = 3). The output should show `EmployeeID`, `Name`, `ManagerID`, and `Level` (distance from the top).
    2.  **Multiple CTEs for Department Salary Analysis**:
          - Create a first CTE named `DepartmentSalaries` that calculates the `TotalSalary` and `EmployeeCount` for each `DepartmentID`.
          - Create a second CTE named `HighPerformingDepartments` that selects `DepartmentID` from `DepartmentSalaries` where `TotalSalary` is above a certain threshold (e.g., $150,000) AND `EmployeeCount` is greater than 2.
          - Finally, `SELECT` `EmployeeID`, `Name`, `Salary`, and `DepartmentID` for all employees who belong to `HighPerformingDepartments`.
    3.  **CTE with `UPDATE`**:
          - Use a CTE to identify all employees whose `Salary` is below the `AverageSalary` of their respective `Department`.
          - Then, use an `UPDATE` statement with this CTE to give these employees a 10% raise.
          - Verify the salary changes.
    4.  **Clean up**: Drop the `Employees` table.
