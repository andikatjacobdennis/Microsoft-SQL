# Module SQLFNC02: **Functions - User-Defined Functions (UDFs)**

> **Module Objective**
> This module aims to empower learners to create and manage their own custom functions in T-SQL. By understanding the different types of User-Defined Functions (UDFs) – scalar and table-valued – learners will be able to encapsulate reusable logic, improve code modularity, and enhance query capabilities for specific business needs.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Understand the concept, benefits, and limitations of User-Defined Functions (UDFs).
2.  Create and utilize Scalar User-Defined Functions to return single data values.
3.  Implement Inline Table-Valued Functions to return table datasets efficiently.
4.  Develop Multi-Statement Table-Valued Functions for more complex table-returning logic.
5.  Modify, drop, and apply best practices for UDF development in SQL Server.

## Reference Materials

  - **Books:**
      - *T-SQL Querying (Developer Reference)* by Itzik Ben-Gan
      - *Pro T-SQL 2022* by Louis Davidson and Kevin Boles
  - **Online Resources:**
      - CREATE FUNCTION (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/statements/create-function-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-function-transact-sql?view=sql-server-ver16))
      - User-Defined Functions (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/relational-databases/user-defined-functions/user-defined-functions?view=sql-server-ver16](https://www.google.com/search?q=https://learn.microsoft.com/en-us/relational-databases/user-defined-functions/user-defined-functions%3Fview%3Dsql-server-ver16))
      - SQL Server User Defined Functions – The Basics (SQLShack) ([https://www.sqlshack.com/sql-server-user-defined-functions-the-basics/](https://www.google.com/search?q=https://www.sqlshack.com/sql-server-user-defined-functions-the-basics/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to User-Defined Functions (UDFs)**

  - **Definition**: A User-Defined Function (UDF) in T-SQL is a routine that accepts parameters, performs an action (such as a complex calculation or query), and returns a result. Unlike stored procedures, functions can be used in the `SELECT` list, `WHERE` clause, `JOIN` conditions, and `HAVING` clause.
  - **Purpose**:
      - **Code Reusability**: Encapsulate frequently used logic in a single object.
      - **Modularity**: Break down complex queries or calculations into smaller, manageable units.
      - **Data Transformation**: Perform custom data manipulations not covered by built-in functions.
      - **Query Simplification**: Improve readability by replacing complex expressions with a single function call.
  - **Benefits**:
      - Improved code maintainability.
      - Reduced code duplication.
      - Enhanced performance for Inline Table-Valued Functions due to optimization.
  - **Limitations/Drawbacks**:
      - **No Side Effects**: Cannot perform data modification (`INSERT`, `UPDATE`, `DELETE`, `MERGE`) directly.
      - **Error Handling**: Limited error handling capabilities (e.g., cannot use `TRY...CATCH` blocks for non-batch-terminating errors).
      - **Performance for Scalar/Multi-Statement UDFs**: Can lead to poor performance ("RBAR - Row By Agonizing Row") if called in `SELECT` lists or `WHERE` clauses on large datasets, as they are often executed once per row.
      - **Non-Deterministic Functions**: Cannot contain non-deterministic built-in functions (like `GETDATE()`, `RAND()`) if they are to be schema-bound.

-----

### 2\. **Types of User-Defined Functions**

#### 2.1. **Scalar Functions**

  - **Definition**: A scalar function returns a single data value of the data type defined in its `RETURNS` clause.
  - **Syntax**:
    ```sql
    CREATE FUNCTION [ schema_name. ] function_name
    ( { @parameter_name [ AS ] data_type [ = default ] } [ ,...n ] )
    RETURNS data_type
    [ WITH <function_option> [ ,...n ] ]
    AS
    BEGIN
        -- Function body: declare variables, perform calculations
        -- No SELECT statements that return a result set.
        RETURN scalar_expression
    END ;
    ```
  - **Core Elements**:
      - `RETURNS data_type`: Specifies the data type of the single value the function will return.
      - `BEGIN...END`: Encloses the function's logic.
      - `RETURN scalar_expression`: The mandatory statement that returns the single value.
  - **Example**: Calculate the net price after discount.
    ```sql
    CREATE FUNCTION dbo.fn_CalculateNetPrice
    (
        @Price DECIMAL(10, 2),
        @DiscountPercentage DECIMAL(5, 2)
    )
    RETURNS DECIMAL(10, 2)
    AS
    BEGIN
        DECLARE @NetPrice DECIMAL(10, 2);
        SET @NetPrice = @Price * (1 - @DiscountPercentage);
        RETURN @NetPrice;
    END;
    GO

    -- Usage:
    SELECT dbo.fn_CalculateNetPrice(100.00, 0.10) AS NetPrice; -- Result: 90.00
    SELECT ProductName, Price, Discount, dbo.fn_CalculateNetPrice(Price, Discount) AS NetPrice
    FROM Products; -- Example usage in a SELECT statement
    ```

#### 2.2. **Table-Valued Functions (TVFs)**

  - **Definition**: A Table-Valued Function (TVF) returns a table data type. They can be used in the `FROM` clause of a query, just like a table or view.
  - **Types**:
      - **Inline Table-Valued Functions (ITVFs)**:
          - **Description**: A simplified TVF that consists of a single `SELECT` statement. SQL Server can often "inline" the function's logic into the calling query, leading to excellent performance (similar to a view).
          - **Syntax**:
            ```sql
            CREATE FUNCTION [ schema_name. ] function_name
            ( { @parameter_name [ AS ] data_type [ = default ] } [ ,...n ] )
            RETURNS TABLE
            [ WITH <function_option> [ ,...n ] ]
            AS
            RETURN
                ( select_statement ) ;
            ```
          - **Example**: Get employees by department.
            ```sql
            CREATE FUNCTION dbo.fn_GetEmployeesByDepartment (@DepartmentID INT)
            RETURNS TABLE
            AS
            RETURN
            (
                SELECT EmployeeID, FirstName, LastName, Email
                FROM Employees
                WHERE DepartmentID = @DepartmentID
            );
            GO

            -- Usage:
            SELECT EmployeeID, FirstName, LastName
            FROM dbo.fn_GetEmployeesByDepartment(101);

            -- Can be joined:
            SELECT T1.EmployeeID, T1.FirstName, D.DepartmentName
            FROM dbo.fn_GetEmployeesByDepartment(101) AS T1
            JOIN Departments AS D ON T1.DepartmentID = D.DepartmentID;
            ```
      - **Multi-Statement Table-Valued Functions (MSTVFs)**:
          - **Description**: A more complex TVF that can contain multiple T-SQL statements (including `INSERT`, `UPDATE`, `DELETE` into a local table variable), building a result set row by row, and then returning a table.
          - **Syntax**:
            ```sql
            CREATE FUNCTION [ schema_name. ] function_name
            ( { @parameter_name [ AS ] data_type [ = default ] } [ ,...n ] )
            RETURNS @return_variable TABLE
            (
                column_name data_type [ ( length | precision, scale ) ]
                [ COLLATE collation_name ]
                [ DEFAULT constant_expression ]
                [ CHECK ( search_condition ) ]
            )
            [ WITH <function_option> [ ,...n ] ]
            AS
            BEGIN
                -- Function body: multiple SQL statements
                INSERT @return_variable (...) VALUES (...);
                -- ... other logic
                RETURN
            END ;
            ```
          - **Example**: Get employee hierarchy with level.
            ```sql
            CREATE FUNCTION dbo.fn_GetEmployeeHierarchy (@EmployeeID INT)
            RETURNS @EmployeeHierarchy TABLE
            (
                EmployeeID INT,
                EmployeeName NVARCHAR(100),
                ManagerID INT,
                HierarchyLevel INT
            )
            AS
            BEGIN
                -- Anchor member
                INSERT INTO @EmployeeHierarchy
                SELECT EmployeeID, FirstName + ' ' + LastName, ManagerID, 1
                FROM Employees
                WHERE EmployeeID = @EmployeeID;

                -- Recursive member (simplified - actual recursion uses CTE)
                -- For complex hierarchies, a recursive CTE is preferred within an MSTVF or directly in a query.
                -- This example shows simple multi-statement logic for illustration.
                INSERT INTO @EmployeeHierarchy
                SELECT e.EmployeeID, e.FirstName + ' ' + e.LastName, e.ManagerID, 2
                FROM Employees AS e
                JOIN @EmployeeHierarchy AS eh ON e.ManagerID = eh.EmployeeID
                WHERE eh.HierarchyLevel = 1 AND e.EmployeeID <> eh.EmployeeID;

                RETURN;
            END;
            GO

            -- Usage:
            SELECT * FROM dbo.fn_GetEmployeeHierarchy(1); -- Assume EmployeeID 1 is a top-level manager
            ```
          - **Diagram**:
            ```
            +-------------------------+
            |  Multi-Statement TVF    |
            |                         |
            |   DECLARE @TableVariable|
            |   INSERT @TableVariable |
            |   UPDATE @TableVariable |
            |   ...                   |
            |   RETURN                |
            +-------------------------+
                    |
                    | (Table data type)
                    V
            +-------------------------+
            |      Result Set         |
            +-------------------------+
            ```
            *Note: MSTVFs often perform worse than ITVFs or direct queries due to optimizer limitations. They materialize data in a table variable which can prevent certain query optimizations.*

-----

### 3\. **Modifying, Dropping UDFs and Best Practices**

#### 3.1. **Modifying UDFs (`ALTER FUNCTION`)**

  - **Description**: Used to change the definition of an existing UDF. The syntax is identical to `CREATE FUNCTION`, but using `ALTER`.
  - **Syntax**: Same as `CREATE FUNCTION`, replace `CREATE` with `ALTER`.
  - **Example**:
    ```sql
    ALTER FUNCTION dbo.fn_CalculateNetPrice
    (
        @Price DECIMAL(10, 2),
        @DiscountPercentage DECIMAL(5, 2),
        @TaxRate DECIMAL(5, 2) = 0.05 -- Added new parameter
    )
    RETURNS DECIMAL(10, 2)
    AS
    BEGIN
        DECLARE @NetPrice DECIMAL(10, 2);
        SET @NetPrice = @Price * (1 - @DiscountPercentage) * (1 + @TaxRate);
        RETURN @NetPrice;
    END;
    GO
    ```

#### 3.2. **Dropping UDFs (`DROP FUNCTION`)**

  - **Description**: Used to remove an existing UDF from the database.
  - **Syntax**: `DROP FUNCTION [ IF EXISTS ] { schema_name. } function_name [ ,...n ] ;`
  - **Example**:
    ```sql
    DROP FUNCTION dbo.fn_CalculateNetPrice;
    DROP FUNCTION IF EXISTS dbo.fn_GetEmployeesByDepartment;
    ```
    *Note: You cannot drop a function if it is referenced by another schema-bound object (e.g., a view created with `WITH SCHEMABINDING`). You must drop the referencing object first.*

#### 3.3. **Key Concepts for UDF Development**

  - **Performance Considerations**:
      - **Scalar UDFs and MSTVFs**: Avoid using them in the `SELECT` list or `WHERE` clause of large queries, especially when they perform complex operations or interact with data. This can lead to "Row-By-Agonizing-Row" processing and significant performance degradation.
      - **ITVFs**: Generally perform well because the optimizer can often "inline" their logic, treating them like views. Prefer ITVFs over MSTVFs whenever possible.
      - **Replace with CTEs/Subqueries**: Often, complex logic in scalar/MSTVFs can be rewritten as CTEs or subqueries within the main query for better performance.
  - **Deterministic vs. Non-Deterministic Functions**:
      - **Deterministic**: Always return the same result value any time they are called with a specific set of input values and given the same state of the database. (e.g., `SUM()`, `POWER()`, `DATEADD()`).
      - **Non-Deterministic**: May return different result values each time they are called with a specific set of input values, even if the database state remains the same. (e.g., `GETDATE()`, `RAND()`).
      - **Impact**: Deterministic functions can be indexed (e.g., in indexed views or computed columns), leading to performance benefits. Non-deterministic functions cannot be indexed.
  - **Schema Binding (`WITH SCHEMABINDING`)**:
      - **Explanation**: Specifies that the function is bound to the schema of the underlying objects it references. This prevents those underlying objects from being altered or dropped if it would affect the function definition.
      - **Benefits**: Improves query plan stability and allows the function to be used in computed columns and indexed views (if it's deterministic).
      - **Requirement**: All objects referenced by the function must be in the same database and schema.
      - **Example**:
        ```sql
        CREATE FUNCTION dbo.fn_GetFullName (@FirstName NVARCHAR(50), @LastName NVARCHAR(50))
        RETURNS NVARCHAR(101)
        WITH SCHEMABINDING -- Bind to schema
        AS
        BEGIN
            RETURN @FirstName + ' ' + @LastName;
        END;
        ```
  - **Error Handling**: UDFs cannot use `TRY...CATCH` for transaction-breaking errors (like division by zero if not handled with `NULLIF` or `IIF`). Error handling within UDFs is generally limited.

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create a scalar function that calculates the tax amount for a given price and tax rate. | SSMS / Azure Data Studio | Function correctly returns tax amount for various inputs.             |
| 2   | Create an inline table-valued function that returns all products from a specific category. | SSMS / Azure Data Studio | Function returns a table of products filtered by category.            |
| 3   | Create a multi-statement table-valued function that retrieves employees and their managers, and adds a calculated 'YearsOfService' column. | SSMS / Azure Data Studio | Function returns a table with employee and manager info, plus calculated years. |
| 4   | Alter one of your existing UDFs to add new logic or parameters. Then, drop another UDF. | SSMS / Azure Data Studio | Function definition updated; function successfully dropped.           |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which type of User-Defined Function (UDF) is generally preferred for performance when returning a table result, due to its ability to be "inlined" by the query optimizer?**

      - A) Scalar Function
      - B) Multi-Statement Table-Valued Function (MSTVF)
      - C) Inline Table-Valued Function (ITVF)
      - D) Aggregate Function
      - **Answer:** C) Inline Table-Valued Function (ITVF)
      - **Explanation:** ITVFs are highly optimized because their single SELECT statement can often be merged directly into the calling query's execution plan, similar to a view.

2.  **Which of the following operations is NOT allowed within a T-SQL User-Defined Function?**

      - A) Declaring local variables.
      - B) Executing a `SELECT` statement that returns a result set (for TVFs).
      - C) Performing an `INSERT` operation on a persistent table.
      - D) Using `BEGIN...END` blocks.
      - **Answer:** C) Performing an `INSERT` operation on a persistent table.
      - **Explanation:** UDFs are designed to be side-effect-free; they cannot perform DML operations (INSERT, UPDATE, DELETE, MERGE) on base tables. They can insert into table variables within MSTVFs.

3.  **What is the purpose of the `WITH SCHEMABINDING` option when creating a User-Defined Function?**

      - A) It encrypts the function's definition.
      - B) It ensures the function is deterministic.
      - C) It prevents changes to underlying objects that the function references.
      - D) It allows the function to execute cross-database queries.
      - **Answer:** C) It prevents changes to underlying objects that the function references.
      - **Explanation:** `WITH SCHEMABINDING` creates a binding between the function and the schema of the objects it references, preventing those objects from being modified or dropped in a way that would break the function.

### Short Answer Questions

1.  List two key differences between a T-SQL User-Defined Function and a Stored Procedure.
2.  Explain why Scalar User-Defined Functions (UDFs) can sometimes lead to poor query performance when used in a `SELECT` list of a query processing many rows.
3.  Describe a scenario where a Multi-Statement Table-Valued Function (MSTVF) might be necessary, even with its potential performance drawbacks, over an Inline Table-Valued Function (ITVF).

### Practical Task

  - **Setup**: Assume you have an `Employees` table with columns: `EmployeeID` (INT PK), `FirstName` (NVARCHAR), `LastName` (NVARCHAR), `BirthDate` (DATE), `HireDate` (DATE), `ManagerID` (INT NULL).
  - **Tasks**:
    1.  **Create a Scalar Function**: `dbo.fn_CalculateYearsOfService` that takes `HireDate` (DATE) as input and returns the number of full years an employee has served (INT).
          - Test the function with various hire dates.
          - Use this function in a `SELECT` query on your `Employees` table to display `FirstName`, `LastName`, and `YearsOfService`.
    2.  **Create an Inline Table-Valued Function (ITVF)**: `dbo.fn_GetTeamMembers` that takes `ManagerID` (INT) as input and returns a table of `EmployeeID`, `FirstName`, `LastName`, `HireDate` for all employees reporting to that manager.
          - Test the function by passing a `ManagerID` and verify the results.
          - Join this function's output with the main `Employees` table or another table (e.g., `Departments` if you create one) to display additional information.
    3.  **(Optional, Advanced)** **Create a Multi-Statement Table-Valued Function (MSTVF)**: `dbo.fn_GetEmployeesAndTheirAge` that returns a table with `EmployeeID`, `FullName`, and `Age` for all employees, where `FullName` is concatenated and `Age` is calculated using `dbo.fn_CalculateYearsOfService` or a direct calculation. This function should explicitly declare a table variable and insert data into it.
          - Test the function and compare its usage with a direct query or an ITVF.
    4.  **Alter `dbo.fn_CalculateYearsOfService`**: Modify the function to include a check that returns 0 if `HireDate` is NULL or in the future.
    5.  **Drop `dbo.fn_GetTeamMembers`** and verify it's removed from the database.
