# Module SQLVIEW01: **Views**

> **Module Objective**
> This module aims to provide a thorough understanding of T-SQL Views. Learners will discover how to create and manage virtual tables, leverage views for data abstraction, security, and simplification of complex queries, and understand different view options and types.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Understand the concept and benefits of database views.
2.  Create simple and complex views using the `CREATE VIEW` statement.
3.  Apply view options such as `WITH ENCRYPTION`, `WITH SCHEMABINDING`, and `WITH CHECK OPTION`.
4.  Differentiate between various types of views, including simple, complex, and indexed views.
5.  Modify and drop existing views.
6.  Understand the considerations for updatable views.

## Reference Materials

  - **Books:**
      - *T-SQL Querying (Developer Reference)* by Itzik Ben-Gan
      - *Microsoft SQL Server 2019 T-SQL Querying (Exam 70-761)* by Grant Fritchey and Kevin Boles
  - **Online Resources:**
      - CREATE VIEW (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/statements/create-view-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-view-transact-sql?view=sql-server-ver16))
      - Views (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/relational-databases/views/views?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/relational-databases/views/views?view=sql-server-ver16))
      - SQL Server Views: A Complete Guide (SQLShack) ([https://www.sqlshack.com/sql-server-views-a-complete-guide/](https://www.google.com/search?q=https://www.sqlshack.com/sql-server-views-a-complete-guide/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to Views**

  - **Definition**: A view is a virtual table whose contents are defined by a query. Unlike a table, a view does not store data itself; it is a stored query that acts as a window into one or more underlying tables. When you query a view, SQL Server executes the view's defining query and presents the result set as if it were a table.
  - **Purpose and Benefits**:
      - **Security**: Restrict access to specific rows and/or columns of a table. Users can be granted permissions on the view, not the underlying tables.
      - **Data Abstraction/Simplification**: Present a simplified or customized view of the data, hiding complex joins, calculations, or sensitive columns.
      - **Code Reusability**: Encapsulate complex queries, making them reusable across multiple applications or reports.
      - **Data Consistency**: Ensure that all applications accessing data through the view see a consistent representation.
      - **Backward Compatibility**: Can maintain compatibility for legacy applications when underlying table schemas change (by altering the view, not the application).

-----

### 2\. **`CREATE VIEW` Statement and Options**

  - **Definition**: The `CREATE VIEW` statement is used to define a new view in the database.
  - **Syntax**:
    ```sql
    CREATE VIEW [ schema_name . ] view_name [ ( column [ ,...n ] ) ]
    [ WITH <view_attribute> [ ,...n ] ]
    AS select_statement
    [ WITH CHECK OPTION ] ;
    ```
  - **Core Elements**:
      - `view_name`: The name of the view.
      - `(column [,...n])`: Optional list of column names for the view. If omitted, the view columns inherit names from the `select_statement`.
      - `AS select_statement`: The query that defines the view's data. This can be any valid `SELECT` statement.

#### 2.1. **View Options (`WITH <view_attribute>`)**

  - **`WITH ENCRYPTION`**:

      - **Description**: Encrypts the definition of the view in the `sys.syscomments` table. This prevents unauthorized users from viewing the view's underlying `SELECT` statement.
      - **Use Case**: Protecting sensitive business logic or intellectual property.
      - **Example**:
        ```sql
        CREATE VIEW dbo.ConfidentialEmployeeData
        WITH ENCRYPTION
        AS
        SELECT EmployeeID, FirstName, LastName, Salary -- Assume Salary is sensitive
        FROM Employees;
        GO
        ```
      - **Note**: Once encrypted, you cannot view the definition using `sp_helptext` or `OBJECT_DEFINITION()`. You must drop and recreate the view to change its definition.

  - **`WITH SCHEMABINDING`**:

      - **Description**: Binds the view to the schema of the underlying tables. This means that the base tables cannot be altered or dropped in a way that would affect the view definition unless the view is dropped or altered first.
      - **Benefits**:
          - **Performance**: Can improve performance by allowing the query optimizer to create more efficient execution plans.
          - **Reliability**: Prevents accidental schema changes to underlying tables from breaking the view.
          - **Required for Indexed Views**: A view must be schema-bound to create a clustered index on it (making it an indexed view).
      - **Requirements**:
          - All objects referenced must be in the same database.
          - All objects must be referenced by two-part names (`schema.object_name`).
          - The `SELECT` statement cannot use `*` (asterisk) for columns. All columns must be explicitly listed.
      - **Example**:
        ```sql
        CREATE VIEW dbo.SalesSummaryByProduct
        WITH SCHEMABINDING
        AS
        SELECT
            P.ProductID,
            P.ProductName,
            SUM(O.Quantity * O.UnitPrice) AS TotalRevenue
        FROM dbo.Products AS P -- Two-part name
        JOIN dbo.OrderDetails AS O ON P.ProductID = O.ProductID -- Two-part name
        GROUP BY P.ProductID, P.ProductName;
        GO
        ```

  - **`WITH CHECK OPTION`**:

      - **Description**: Ensures that all `INSERT` and `UPDATE` statements executed against the view comply with the `WHERE` clause of the view's defining `SELECT` statement. If a row is inserted or updated through the view such that it no longer satisfies the view's `WHERE` clause, the operation is rejected.
      - **Use Case**: Maintain data integrity through the view.
      - **Example**:
        ```sql
        CREATE VIEW dbo.HighValueCustomers
        AS
        SELECT CustomerID, FirstName, LastName, TotalOrders
        FROM Customers
        WHERE TotalOrders > 100
        WITH CHECK OPTION;
        GO

        -- This INSERT will fail because TotalOrders is not > 100
        -- INSERT INTO dbo.HighValueCustomers (CustomerID, FirstName, LastName, TotalOrders)
        -- VALUES (999, 'New', 'Customer', 50);

        -- This UPDATE will fail if it makes TotalOrders <= 100
        -- UPDATE dbo.HighValueCustomers SET TotalOrders = 80 WHERE CustomerID = 123;
        ```

-----

### 3\. **Types of Views**

#### 3.1. **Simple View**

  - **Definition**: A view based on a single table, typically used to hide columns or rows, or to rename columns.
  - **Characteristics**: Often updatable (you can `INSERT`, `UPDATE`, `DELETE` through them) if they meet certain criteria (e.g., no `GROUP BY`, no aggregate functions, all `NOT NULL` columns from the base table are included).
  - **Example**:
    ```sql
    CREATE VIEW dbo.CustomerContactInfo AS
    SELECT CustomerID, FirstName, LastName, Email
    FROM Customers;
    GO
    ```

#### 3.2. **Complex View**

  - **Definition**: A view based on multiple tables (using joins), or containing aggregate functions, `GROUP BY` clauses, `DISTINCT`, `UNION`, etc.
  - **Characteristics**: Generally **not updatable** directly through DML statements, as SQL Server cannot unambiguously determine which underlying table or row to modify.
  - **Example**:
    ```sql
    CREATE VIEW dbo.OrderSummary AS
    SELECT
        C.CustomerID,
        C.FirstName,
        C.LastName,
        O.OrderID,
        O.OrderDate,
        SUM(OD.Quantity * OD.UnitPrice) AS TotalOrderAmount
    FROM Customers AS C
    JOIN Orders AS O ON C.CustomerID = O.CustomerID
    JOIN OrderDetails AS OD ON O.OrderID = OD.OrderID
    GROUP BY C.CustomerID, C.FirstName, C.LastName, O.OrderID, O.OrderDate;
    GO
    ```

#### 3.3. **Indexed View (Materialized View)**

  - **Definition**: A view that has a unique clustered index created on it. When a clustered index is created on a view, the view's result set is physically stored in the database, similar to a table. This is also known as a materialized view.
  - **Benefits**:
      - **Significant Performance Improvement**: For queries that frequently access the view's data, especially complex aggregations or joins, as the data is pre-computed and stored.
      - **Query Optimizer Usage**: The SQL Server query optimizer can use an indexed view even if the query doesn't explicitly reference the view, if the view's definition matches part of the query.
  - **Requirements**:
      - The view must be created `WITH SCHEMABINDING`.
      - The view must be deterministic.
      - All functions in the view must be deterministic.
      - The `SELECT` statement cannot contain `DISTINCT`, `TOP`, `UNION`, `OUTER JOIN` (except for specific cases), `SUBQUERY`, `FULLTEXT` functions, `ROWSET` functions, etc.
      - A unique clustered index must be created on the view first.
  - **Example**: (Requires `Products` and `OrderDetails` tables, with `ProductID` as PK in `Products` and `ProductID` as FK in `OrderDetails`)
    ```sql
    -- 1. Create the base tables (if not exists)
    CREATE TABLE dbo.Products (
        ProductID INT PRIMARY KEY,
        ProductName NVARCHAR(100),
        Price DECIMAL(10, 2)
    );

    CREATE TABLE dbo.OrderDetails (
        OrderDetailID INT PRIMARY KEY,
        OrderID INT,
        ProductID INT,
        Quantity INT,
        UnitPrice DECIMAL(10, 2)
    );
    GO

    -- Insert sample data
    INSERT INTO dbo.Products VALUES (1, 'Laptop', 1200.00), (2, 'Mouse', 25.00);
    INSERT INTO dbo.OrderDetails VALUES (101, 1, 1, 1, 1200.00), (102, 1, 2, 2, 25.00);
    GO

    -- 2. Create the view WITH SCHEMABINDING
    CREATE VIEW dbo.ProductSalesSummary
    WITH SCHEMABINDING
    AS
    SELECT
        P.ProductID,
        P.ProductName,
        SUM(OD.Quantity) AS TotalQuantitySold,
        SUM(OD.Quantity * OD.UnitPrice) AS TotalRevenue
    FROM dbo.Products AS P
    JOIN dbo.OrderDetails AS OD ON P.ProductID = OD.ProductID
    GROUP BY P.ProductID, P.ProductName;
    GO

    -- 3. Create a UNIQUE CLUSTERED INDEX on the view
    CREATE UNIQUE CLUSTERED INDEX IX_ProductSalesSummary_ProductID
    ON dbo.ProductSalesSummary (ProductID);
    GO

    -- Now, querying ProductSalesSummary will use the materialized data.
    SELECT * FROM dbo.ProductSalesSummary;
    ```

#### 3.4. **Partitioned View**

  - **Definition**: A view that combines horizontally partitioned data from a set of member tables across one or more servers. It allows data from multiple tables (often on different servers) to be treated as a single table.
  - **Characteristics**: Advanced concept used in distributed database environments.
  - **Use Case**: Scaling large databases by distributing data across multiple physical locations while providing a unified logical view.

-----

### 4\. **Modifying and Dropping Views**

#### 4.1. **`ALTER VIEW`**

  - **Description**: Used to modify the definition of an existing view. The syntax is identical to `CREATE VIEW`, but you use `ALTER` instead of `CREATE`.
  - **Syntax**: Same as `CREATE VIEW`, but replace `CREATE` with `ALTER`.
  - **Example**:
    ```sql
    ALTER VIEW dbo.CustomerContactInfo AS
    SELECT CustomerID, FirstName, LastName, Email, PhoneNumber -- Added PhoneNumber
    FROM Customers;
    GO
    ```
  - **Considerations for Updatable Views**:
      - A view is updatable if DML statements (`INSERT`, `UPDATE`, `DELETE`) can be directly applied to it, and SQL Server can unambiguously translate these operations to the underlying base tables.
      - **Rules for Updatability**:
          - Must be based on a single table (for `INSERT`/`DELETE`).
          - Cannot contain `DISTINCT`, `GROUP BY`, `HAVING`, `UNION`, `TOP`.
          - Cannot contain aggregate functions.
          - Cannot contain subqueries in the `SELECT` list.
          - All `NOT NULL` columns from the base table must be included in the view (for `INSERT`).
          - Computed columns in the view are not updatable.
      - **`INSTEAD OF` Triggers**: For complex views that are not directly updatable, you can create `INSTEAD OF` triggers on the view to define custom DML logic that executes on the underlying base tables.

#### 4.2. **`DROP VIEW`**

  - **Definition**: Used to remove one or more existing views from the database.
  - **Syntax**: `DROP VIEW [ IF EXISTS ] { database_name. [ schema_name ] . | schema_name . } view_name [ ,...n ] ;`
  - **Example**:
    ```sql
    DROP VIEW dbo.CustomerContactInfo;
    DROP VIEW IF EXISTS dbo.SalesSummaryByProduct;
    ```
  - **Note**: If a view is schema-bound (`WITH SCHEMABINDING`), you cannot drop its underlying base tables until the view itself is dropped.

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create a simple view `EmployeeBasicInfo` from the `Employees` table, showing only `EmployeeID`, `FirstName`, `LastName`, and `Email`. | SSMS / Azure Data Studio | View created and returns selected columns.                            |
| 2   | Create a complex view `DepartmentSalesSummary` that joins `Departments` and `Orders` tables to show total sales per department. | SSMS / Azure Data Studio | View returns aggregated sales data per department.                    |
| 3   | Create a view `CurrentYearOrders` with `WITH CHECK OPTION` to only include orders from the current year. Attempt to insert an order from a past year through this view. | SSMS / Azure Data Studio | View created; insert operation fails as expected.                     |
| 4   | Alter the `EmployeeBasicInfo` view to include the `HireDate` column. Then, drop the `DepartmentSalesSummary` view. | SSMS / Azure Data Studio | View definition updated; view successfully removed.                   |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which view option prevents underlying base tables from being altered or dropped if it would affect the view's definition?**

      - A) `WITH ENCRYPTION`
      - B) `WITH CHECK OPTION`
      - C) `WITH SCHEMABINDING`
      - D) `WITH NATIVE_COMPILATION`
      - **Answer:** C) `WITH SCHEMABINDING`
      - **Explanation:** `WITH SCHEMABINDING` creates a schema-bound view, which prevents changes to the underlying tables that would invalidate the view's definition.

2.  **A view that contains `GROUP BY` clauses and aggregate functions is generally considered what type of view?**

      - A) Simple View
      - B) Updatable View
      - C) Indexed View
      - D) Complex View
      - **Answer:** D) Complex View
      - **Explanation:** Views involving joins, aggregations, or other complex logic are classified as complex views and are typically not directly updatable.

3.  **What is the primary benefit of creating an Indexed View?**

      - A) It encrypts the view's definition for security.
      - B) It allows DML operations on non-updatable views.
      - C) It physically stores the view's result set, potentially improving query performance.
      - D) It ensures all `INSERT` and `UPDATE` operations comply with the view's `WHERE` clause.
      - **Answer:** C) It physically stores the view's result set, potentially improving query performance.
      - **Explanation:** An indexed view materializes its data, meaning the result of the view's query is stored on disk, leading to faster data retrieval for complex queries.

### Short Answer Questions

1.  Explain how views can enhance database security without requiring changes to table permissions.
2.  Describe a scenario where `WITH CHECK OPTION` would be particularly useful for maintaining data integrity through a view.
3.  What are the main requirements for a view to be considered for an indexed view, and why is `WITH SCHEMABINDING` crucial for this?

### Practical Task

  - **Setup**: Create two tables:
      - `Departments`: `DepartmentID` (INT PK), `DepartmentName` (NVARCHAR(50))
      - `Employees`: `EmployeeID` (INT PK), `FirstName` (NVARCHAR(50)), `LastName` (NVARCHAR(50)), `Salary` (DECIMAL(10,2)), `DepartmentID` (INT FK to Departments)
  - **Insert sample data**: At least 3 departments and 10 employees, ensuring employees are assigned to departments.
  - **Tasks**:
    1.  **Create a view `vw_EmployeeSalaries`**:
          - Show `EmployeeID`, `FirstName`, `LastName`, `DepartmentName`, and `Salary`.
          - Join `Employees` and `Departments`.
          - **Include `WITH ENCRYPTION`** for this view.
    2.  **Create a view `vw_HighEarners`**:
          - Show `EmployeeID`, `FirstName`, `LastName`, `Salary` for employees earning more than $70,000.
          - **Include `WITH CHECK OPTION`**.
          - Attempt an `INSERT` through `vw_HighEarners` for an employee with a salary of $60,000. What happens?
          - Attempt an `UPDATE` through `vw_HighEarners` that would change an employee's salary from $80,000 to $65,000. What happens?
    3.  **Create a view `vw_DepartmentAvgSalary`**:
          - Show `DepartmentName` and `AverageSalary` (rounded to 2 decimal places).
          - Use `GROUP BY` and `AVG()`.
          - **Include `WITH SCHEMABINDING`**.
          - Attempt to drop the `DepartmentName` column from the `Departments` table. What happens? (You'll need to drop the view first).
    4.  **Alter `vw_EmployeeSalaries`**: Modify it to also include the employee's `Email` (add an `Email` column to `Employees` table first if not present).
    5.  **Drop all created views**.
    6.  **Clean up**: Drop the `Employees` and `Departments` tables.
