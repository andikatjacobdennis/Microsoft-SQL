# Module SQLJOIN01: **Joins**

> **Module Objective**
> This module aims to provide a comprehensive understanding of various SQL JOIN operations. Learners will master how to combine rows from two or more tables based on related columns, enabling them to retrieve meaningful datasets from relational databases and handle complex data relationships.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Understand the fundamental concept of joining tables in SQL.
2.  Differentiate between `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, and `FULL OUTER JOIN`.
3.  Apply `CROSS JOIN` for Cartesian products and `SELF JOIN` for relating rows within the same table.
4.  Utilize the `APPLY` operator (`CROSS APPLY` and `OUTER APPLY`) for advanced row-by-row operations.
5.  Choose the appropriate join type for different data retrieval scenarios.

## Reference Materials

  - **Books:**
      - *T-SQL Fundamentals* by Itzik Ben-Gan
      - *SQL Server 2019 Querying (Exam 70-761)* by Grant Fritchey and Kevin Boles
  - **Online Resources:**
      - FROM (Transact-SQL) - JOIN (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/queries/from-transact-sql?view=sql-server-ver16\#joins](https://www.google.com/search?q=https://learn.microsoft.com/en-us/sql/t-sql/queries/from-transact-sql%3Fview%3Dsql-server-ver16%23joins))
      - SQL Joins (W3Schools) ([https://www.w3schools.com/sql/sql\_join.asp](https://www.w3schools.com/sql/sql_join.asp))
      - SQL Server Joins (SQLShack) ([https://www.sqlshack.com/sql-server-joins-a-complete-guide/](https://www.google.com/search?q=https://www.sqlshack.com/sql-server-joins-a-complete-guide/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to Joins**

  - **Definition**: A JOIN clause is used to combine rows from two or more tables, based on a related column between them. Relational databases store data in multiple, related tables to reduce data redundancy and improve data integrity. Joins are essential for bringing this distributed data together into a single, cohesive result set.
  - **Purpose**:
      - **Data Integration**: Combine data from different tables that share a logical relationship.
      - **Querying Related Data**: Retrieve information that spans across multiple entities (e.g., customer details with their orders).
      - **Data Analysis**: Create comprehensive reports by linking various datasets.
  - **Types**: SQL Server supports several types of joins, each with a distinct behavior in how they handle matching and non-matching rows.

-----

### 2\. **Standard SQL Join Types**

For demonstration, let's consider two simple tables:

**`Employees` Table:**
| EmployeeID | Name    | DepartmentID |
| :--------- | :------ | :----------- |
| 1          | Alice   | 10           |
| 2          | Bob     | 20           |
| 3          | Charlie | NULL         |

**`Departments` Table:**
| DepartmentID | DepartmentName | Location   |
| :----------- | :------------- | :--------- |
| 10           | HR             | New York   |
| 20           | IT             | London     |
| 30           | Sales          | San Francisco |

#### 2.1. **`INNER JOIN`**

  - **Definition**: Returns only the rows that have matching values in both tables. Non-matching rows from either table are excluded. This is the most common type of join.
  - **Syntax**:
    ```sql
    SELECT columns
    FROM TableA
    INNER JOIN TableB ON TableA.column = TableB.column;
    ```
  - **Example**: Get employees who are assigned to a department.
    ```sql
    SELECT E.Name, D.DepartmentName
    FROM Employees AS E
    INNER JOIN Departments AS D ON E.DepartmentID = D.DepartmentID;
    ```
  - **Result**:
    | Name    | DepartmentName |
    | :------ | :------------- |
    | Alice   | HR             |
    | Bob     | IT             |
  - **Diagram**:
    ```
      Table A      Table B
    +-------+    +-------+
    |       |    |       |
    |  (A)  |----|  (B)  |  <- Intersection (Matching Rows)
    |       |    |       |
    +-------+    +-------+
    ```

#### 2.2. **`LEFT JOIN` (or `LEFT OUTER JOIN`)**

  - **Definition**: Returns all rows from the *left* table, and the matching rows from the *right* table. If there is no match from the right table, `NULL` values are returned for the right table's columns.
  - **Syntax**:
    ```sql
    SELECT columns
    FROM TableA
    LEFT JOIN TableB ON TableA.column = TableB.column;
    ```
  - **Example**: Get all employees and their department, even if they don't have a department.
    ```sql
    SELECT E.Name, D.DepartmentName
    FROM Employees AS E
    LEFT JOIN Departments AS D ON E.DepartmentID = D.DepartmentID;
    ```
  - **Result**:
    | Name    | DepartmentName |
    | :------ | :------------- |
    | Alice   | HR             |
    | Bob     | IT             |
    | Charlie | NULL           |
  - **Diagram**:
    ```
      Table A      Table B
    +-------+    +-------+
    |  (A)  |----|  (B)  |  <- All of A, plus intersection with B
    |       |    |       |
    +-------+    +-------+
    ```

#### 2.3. **`RIGHT JOIN` (or `RIGHT OUTER JOIN`)**

  - **Definition**: Returns all rows from the *right* table, and the matching rows from the *left* table. If there is no match from the left table, `NULL` values are returned for the left table's columns.
  - **Syntax**:
    ```sql
    SELECT columns
    FROM TableA
    RIGHT JOIN TableB ON TableA.column = TableB.column;
    ```
  - **Example**: Get all departments and their assigned employees, even if a department has no employees.
    ```sql
    SELECT E.Name, D.DepartmentName
    FROM Employees AS E
    RIGHT JOIN Departments AS D ON E.DepartmentID = D.DepartmentID;
    ```
  - **Result**:
    | Name    | DepartmentName |
    | :------ | :------------- |
    | Alice   | HR             |
    | Bob     | IT             |
    | NULL    | Sales          |
  - **Diagram**:
    ```
      Table A      Table B
    +-------+    +-------+
    |       |    |  (B)  |
    |  (A)  |----|       |  <- All of B, plus intersection with A
    |       |    |       |
    +-------+    +-------+
    ```

#### 2.4. **`FULL OUTER JOIN` (or `FULL JOIN`)**

  - **Definition**: Returns all rows when there is a match in either the left or the right table. If there is no match, `NULL` values are returned for the columns from the non-matching side. It combines the results of both `LEFT JOIN` and `RIGHT JOIN`.
  - **Syntax**:
    ```sql
    SELECT columns
    FROM TableA
    FULL OUTER JOIN TableB ON TableA.column = TableB.column;
    ```
  - **Example**: Get all employees and all departments, showing matches and non-matches from both sides.
    ```sql
    SELECT E.Name, D.DepartmentName
    FROM Employees AS E
    FULL OUTER JOIN Departments AS D ON E.DepartmentID = D.DepartmentID;
    ```
  - **Result**:
    | Name    | DepartmentName |
    | :------ | :------------- |
    | Alice   | HR             |
    | Bob     | IT             |
    | Charlie | NULL           |
    | NULL    | Sales          |
  - **Diagram**:
    ```
      Table A      Table B
    +-------+    +-------+
    |  (A)  |----|  (B)  |  <- All of A, all of B, including intersection
    |       |    |       |
    +-------+    +-------+
    ```

-----

### 3\. **Special Join Types and `APPLY` Operator**

#### 3.1. **`CROSS JOIN`**

  - **Definition**: Returns the Cartesian product of the rows from the joined tables. This means every row from the first table is combined with every row from the second table. It does not require an `ON` clause.
  - **Use Case**: Generating all possible combinations (e.g., creating a calendar, testing all parameter combinations).
  - **Example**:
    ```sql
    SELECT E.Name, D.DepartmentName
    FROM Employees AS E
    CROSS JOIN Departments AS D;
    ```
  - **Result**: (3 employees \* 3 departments = 9 rows)
    | Name    | DepartmentName |
    | :------ | :------------- |
    | Alice   | HR             |
    | Alice   | IT             |
    | Alice   | Sales          |
    | Bob     | HR             |
    | Bob     | IT             |
    | Bob     | Sales          |
    | Charlie | HR             |
    | Charlie | IT             |
    | Charlie | Sales          |

#### 3.2. **`SELF JOIN`**

  - **Definition**: A join where a table is joined with itself. This is useful when you need to compare rows within the same table, often for hierarchical data (e.g., employees and their managers).
  - **Mechanism**: The table is aliased (given different temporary names) to distinguish between the two instances of the table in the join.
  - **Example**: Find employees and their managers from the same `Employees` table (assuming `ManagerID` links to `EmployeeID`).
    ```sql
    -- Add ManagerID column to Employees table for this example
    -- EmployeeID | Name    | DepartmentID | ManagerID
    -- 1          | Alice   | 10           | NULL
    -- 2          | Bob     | 20           | 1
    -- 3          | Charlie | NULL         | 1

    SELECT E.Name AS EmployeeName, M.Name AS ManagerName
    FROM Employees AS E
    INNER JOIN Employees AS M ON E.ManagerID = M.EmployeeID;
    ```
  - **Result**:
    | EmployeeName | ManagerName |
    | :----------- | :---------- |
    | Bob          | Alice       |
    | Charlie      | Alice       |

#### 3.3. **`APPLY` Operator**

  - **Definition**: The `APPLY` operator invokes a table-valued expression for each row from an outer table expression. It's conceptually similar to a join, but the right side (table-valued expression) can reference columns from the left side. This makes it very powerful for "correlated" operations.
  - **Types**:
      - **`CROSS APPLY`**:

          - **Description**: Returns only rows from the outer table that produce a result set from the table-valued expression. It's like an `INNER JOIN` where the right side depends on the left.
          - **Use Case**: When the right-side function/query needs a parameter from the left-side table. Common for dynamic functions, XML methods, or `TOP N` per group.
          - **Example**: Get the top 2 most expensive products for each category. (Requires a `Products` table with `Category` and `Price`).
            ```sql
            -- Assume Products table: ProductID, ProductName, Category, Price
            -- Example data:
            -- (1, 'Laptop', 'Electronics', 1200), (2, 'Keyboard', 'Electronics', 80), (3, 'Mouse', 'Electronics', 25)
            -- (4, 'Jeans', 'Apparel', 70), (5, 'Shirt', 'Apparel', 30)

            SELECT C.Category, P.ProductName, P.Price
            FROM (SELECT DISTINCT Category FROM Products) AS C
            CROSS APPLY (
                SELECT TOP 2 ProductName, Price
                FROM Products
                WHERE Products.Category = C.Category
                ORDER BY Price DESC
            ) AS P;
            ```
          - **Result**:
            | Category    | ProductName | Price  |
            | :---------- | :---------- | :----- |
            | Electronics | Laptop      | 1200.00 |
            | Electronics | Keyboard    | 80.00  |
            | Apparel     | Jeans       | 70.00  |
            | Apparel     | Shirt       | 30.00  |

      - **`OUTER APPLY`**:

          - **Description**: Returns all rows from the outer table, and the matching rows from the table-valued expression. If the table-valued expression produces no rows for a given outer row, `NULL` values are returned for the columns from the right side. It's like a `LEFT JOIN` where the right side depends on the left.
          - **Use Case**: Similar to `CROSS APPLY`, but you want to include all rows from the left table even if the right-side expression returns no results.
          - **Example**: Get all categories and their top 1 most expensive product, including categories with no products.
            ```sql
            SELECT C.Category, P.ProductName, P.Price
            FROM (SELECT DISTINCT Category FROM Products) AS C
            OUTER APPLY (
                SELECT TOP 1 ProductName, Price
                FROM Products
                WHERE Products.Category = C.Category
                ORDER BY Price DESC
            ) AS P;
            ```
          - **Result**: (If 'Books' category exists but has no products)
            | Category    | ProductName | Price  |
            | :---------- | :---------- | :----- |
            | Electronics | Laptop      | 1200.00 |
            | Apparel     | Jeans       | 70.00  |
            | Books       | NULL        | NULL   |

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create two tables: `Customers` (`CustomerID`, `Name`, `City`) and `Orders` (`OrderID`, `CustomerID`, `OrderDate`). Insert sample data with some non-matching IDs. | SSMS / Azure Data Studio | Tables created and populated.                                         |
| 2   | Write queries using `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, and `FULL OUTER JOIN` to retrieve customer and order information, observing the differences in results. | SSMS / Azure Data Studio | Understanding of how each join type handles matching/non-matching rows. |
| 3   | Demonstrate a `CROSS JOIN` between two small, unrelated tables (e.g., `Colors` and `Sizes`). | SSMS / Azure Data Studio | Cartesian product of rows is displayed.                               |
| 4   | Create a simple `Employees` table with `EmployeeID`, `Name`, `ManagerID`. Use a `SELF JOIN` to list employees and their managers. | SSMS / Azure Data Studio | Correct employee-manager hierarchy is displayed.                      |
| 5   | Use `CROSS APPLY` and `OUTER APPLY` to find the top 1 latest order for each customer, including customers with no orders. | SSMS / Azure Data Studio | `APPLY` operator correctly retrieves correlated data.                 |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which type of join returns only the rows that have matching values in both tables?**

      - A) `LEFT JOIN`
      - B) `FULL OUTER JOIN`
      - C) `INNER JOIN`
      - D) `CROSS JOIN`
      - **Answer:** C) `INNER JOIN`
      - **Explanation:** `INNER JOIN` is the most common join type and only includes rows where the join condition is met in both tables.

2.  **You want to retrieve all employees, and their department names if they have one. If an employee is not assigned to any department, their name should still appear with `NULL` for the department name. Which join type should you use?**

      - A) `RIGHT JOIN`
      - B) `INNER JOIN`
      - C) `FULL OUTER JOIN`
      - D) `LEFT JOIN`
      - **Answer:** D) `LEFT JOIN`
      - **Explanation:** `LEFT JOIN` returns all rows from the left table (Employees) and matches them with the right table (Departments). If no match is found, `NULL`s are returned for the right table's columns.

3.  **The `APPLY` operator is particularly useful when the right-side table expression depends on a column from the left-side table. Which of the following best describes its behavior?**

      - A) It performs a standard equijoin between two independent tables.
      - B) It creates a Cartesian product of the two tables.
      - C) It invokes a table-valued expression for each row of the outer table.
      - D) It only returns rows where both tables have non-null values.
      - **Answer:** C) It invokes a table-valued expression for each row of the outer table.
      - **Explanation:** `APPLY` is unique in that the right-side expression (often a table-valued function or a subquery) is executed once for each row of the left-side table, allowing for correlated operations.

### Short Answer Questions

1.  Explain the difference between `LEFT JOIN` and `RIGHT JOIN`. Can one always be rewritten as the other?
2.  Describe a scenario where a `CROSS JOIN` would be a valid and useful operation.
3.  When would you choose `OUTER APPLY` over `CROSS APPLY`? Provide an example.

### Practical Task

  - **Setup**: Create the following tables and insert sample data:

      - `Customers`: `CustomerID` (INT PK), `CustomerName` (NVARCHAR(100)), `City` (NVARCHAR(50))
          - Data: (1, 'Alice', 'New York'), (2, 'Bob', 'London'), (3, 'Charlie', 'Paris')
      - `Orders`: `OrderID` (INT PK), `CustomerID` (INT FK), `OrderDate` (DATE), `Amount` (DECIMAL(10,2))
          - Data: (101, 1, '2025-01-10', 150.00), (102, 1, '2025-02-15', 200.00), (103, 2, '2025-03-20', 300.00)
          - *Note: CustomerID 3 has no orders.*
      - `Products`: `ProductID` (INT PK), `ProductName` (NVARCHAR(100)), `Category` (NVARCHAR(50)), `Price` (DECIMAL(10,2))
          - Data: (1, 'Laptop', 'Electronics', 1200.00), (2, 'Mouse', 'Electronics', 25.00), (3, 'Keyboard', 'Electronics', 75.00), (4, 'T-Shirt', 'Apparel', 20.00), (5, 'Jeans', 'Apparel', 60.00)

  - **Tasks**:

    1.  **Retrieve Customer Orders (Inner Join)**: List `CustomerName`, `OrderDate`, and `Amount` for all customers who have placed orders.
    2.  **List All Customers and Their Orders (Left Join)**: Show `CustomerName`, `OrderDate`, and `Amount` for all customers, including those who have not placed any orders.
    3.  **Find Customers Without Orders**: Using a `LEFT JOIN` and a `WHERE` clause, identify customers who have not placed any orders.
    4.  **Combine All Customers and All Orders (Full Outer Join)**: Display `CustomerName`, `OrderDate`, and `Amount`, showing all customers and all orders, with `NULL`s where there's no match.
    5.  **Generate Product-Category Combinations (Cross Join)**: Imagine a `Colors` table (`ColorID`, `ColorName`) with ('C1', 'Red'), ('C2', 'Blue') and a `Sizes` table (`SizeID`, `SizeName`) with ('S1', 'Small'), ('S2', 'Large'). Write a `CROSS JOIN` to list all possible combinations of colors and sizes. (You'll need to create these small tables).
    6.  **Find Top Product per Category (Cross Apply)**: For each product `Category`, find the `ProductName` and `Price` of the most expensive product in that category.
    7.  **Clean up**: Drop all created tables.
