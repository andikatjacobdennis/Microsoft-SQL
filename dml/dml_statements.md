# Module SQLDML01: **Data Manipulation Language (DML) Statements**

> **Module Objective**
> This module focuses on Data Manipulation Language (DML) commands, which are essential for interacting with data stored in database tables. Learners will master how to retrieve, insert, update, and delete data, forming the backbone of all database applications.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Construct powerful `SELECT` statements to retrieve specific data from tables, applying various clauses like `WHERE`, `ORDER BY`, `GROUP BY`, and `HAVING`.
2.  Insert new records into tables using `INSERT` statements with different syntax variations.
3.  Modify existing data in tables using `UPDATE` statements with conditional logic.
4.  Remove data from tables using `DELETE` statements.
5.  Understand and apply the `MERGE` statement for efficient upsert operations.

## Reference Materials

  - **Books:**
      - *T-SQL Fundamentals* by Itzik Ben-Gan
      - *SQL Server 2022 Querying for Dummies* by Michael Meys
  - **Online Resources:**
      - SELECT (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/queries/select-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/queries/select-transact-sql?view=sql-server-ver16))
      - INSERT (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/statements/insert-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/statements/insert-transact-sql?view=sql-server-ver16))
      - UPDATE (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/statements/update-transact-sql?view=sql-server-ver16](https://www.google.com/search?q=https://learn.microsoft.com/en-us/sql/t-sql/statements/update-transact-sql%3Fview%3Dsql-server-ver16))
      - DELETE (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/statements/delete-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/statements/delete-transact-sql?view=sql-server-ver16))
      - MERGE (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/statements/merge-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/statements/merge-transact-sql?view=sql-server-ver16))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to Data Manipulation Language (DML)**

  - **Definition**: Data Manipulation Language (DML) is a subset of SQL used for adding, retrieving, updating, and deleting data in a database. DML statements work on the data within the schema defined by DDL.
  - **Key Characteristics**:
      - **Transactional**: DML commands are typically transactional, meaning changes are not made permanent until a `COMMIT` statement is issued. They can be rolled back using `ROLLBACK`.
      - **Data-Centric**: Focus on interacting with the actual data rows stored in tables.
      - **Frequently Used**: These are the most commonly used SQL commands in database applications.

-----

### 2\. **Core DML Statements: `SELECT`, `INSERT`, `UPDATE`, `DELETE`**

#### 2.1. **`SELECT` Statement**

  - **Definition**: Used to retrieve data from one or more tables. It is the most frequently used DML command.
  - **Core Elements and Clauses**:
      - **`SELECT Columns`**: Specifies the columns to retrieve. `*` retrieves all columns.
          - `SELECT ProductID, ProductName FROM Products;`
          - `SELECT * FROM Customers;`
      - **`FROM Clause`**: Specifies the table(s) from which to retrieve data.
          - `FROM Products`
      - **`WHERE Clause`**: Filters rows based on specified conditions.
          - `SELECT ProductName, Price FROM Products WHERE Price > 50.00;`
          - `SELECT * FROM Orders WHERE OrderDate = '2025-07-20' AND TotalAmount > 100;`
      - **`ORDER BY`**: Sorts the result set by one or more columns.
          - `SELECT FirstName, LastName FROM Employees ORDER BY LastName ASC, FirstName DESC;`
      - **`GROUP BY`**: Groups rows that have the same values in specified columns into a summary row. Often used with aggregate functions.
          - `SELECT Category, COUNT(*) AS NumberOfProducts FROM Products GROUP BY Category;`
      - **`HAVING`**: Filters groups based on a specified condition (used with `GROUP BY`).
          - `SELECT Category, AVG(Price) AS AveragePrice FROM Products GROUP BY Category HAVING AVG(Price) > 100;`
      - **`DISTINCT`**: Returns only unique values in the specified column(s).
          - `SELECT DISTINCT City FROM Customers;`
      - **Aliases**: Assigns a temporary name to tables or columns for readability and brevity.
          - `SELECT C.FirstName AS CustomerFName, O.OrderDate FROM Customers AS C JOIN Orders AS O ON C.CustomerID = O.CustomerID;`

#### 2.2. **`INSERT` Statement**

  - **Definition**: Adds new rows of data into a table.
  - **Types**:
      - **`INSERT INTO ... VALUES`**: Inserts one or more rows by explicitly listing values.
          - **Syntax**: `INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...);`
          - **Example**:
            ```sql
            INSERT INTO Employees (FirstName, LastName, Email, HireDate)
            VALUES ('Jane', 'Doe', 'jane.doe@example.com', '2023-01-15');
            ```
            (Omitting columns with default values or identity columns is common)
      - **`INSERT INTO ... SELECT`**: Inserts rows into a table by selecting data from another table or a query result.
          - **Syntax**: `INSERT INTO target_table (column1, column2, ...) SELECT columnA, columnB, ... FROM source_table WHERE condition;`
          - **Example**:
            ```sql
            INSERT INTO ArchiveEmployees (EmployeeID, FirstName, LastName)
            SELECT EmployeeID, FirstName, LastName FROM Employees WHERE HireDate < '2020-01-01';
            ```
      - **`DEFAULT Values`**: When a column has a `DEFAULT` constraint, you can omit it from the `INSERT` statement or use the `DEFAULT` keyword to insert its default value.
          - `INSERT INTO Products (ProductName, Price, StockQuantity) VALUES ('New Item', 29.99, DEFAULT);`
          - `INSERT INTO Employees (FirstName, LastName, Email) VALUES ('Bob', 'Smith', 'bob@example.com');` -- HireDate will get its default (GETDATE())

#### 2.3. **`UPDATE` Statement**

  - **Definition**: Modifies existing data in one or more rows of a table.
  - **Core Elements**:
      - **`SET Clause`**: Specifies which columns to modify and their new values.
          - `SET Salary = Salary * 1.05`
      - **`WHERE Clause`**: Specifies which rows to update. **Crucial to prevent updating all rows.**
          - `UPDATE Employees SET Salary = 60000 WHERE EmployeeID = 101;`
          - `UPDATE Products SET Price = Price * 0.90, LastUpdated = GETDATE() WHERE Category = 'Electronics';`
  - **Example**:
    ```sql
    UPDATE Orders
    SET OrderStatus = 'Shipped', ShipDate = GETDATE()
    WHERE OrderID = 12345;
    ```

#### 2.4. **`DELETE` Statement**

  - **Definition**: Removes existing rows from a table.
  - **Core Elements**:
      - **`FROM Clause`**: Implicitly refers to the table from which rows are deleted. Explicitly naming it is optional but good practice.
          - `DELETE FROM Customers`
      - **`WHERE Clause`**: Specifies which rows to delete. **Crucial to prevent deleting all rows.**
          - `DELETE FROM Orders WHERE OrderDate < '2024-01-01';`
  - **Example**:
    ```sql
    DELETE FROM Products WHERE StockQuantity = 0; -- Delete out of stock products
    ```

-----

### 3\. **The `MERGE` Statement**

  - **Definition**: Performs `INSERT`, `UPDATE`, or `DELETE` operations on a target table based on the results of a join with a source table. It's often used for "upsert" scenarios (update if exists, insert if not).
  - **Key Concepts**:
      - **`MERGE INTO`**: Specifies the target table that will be modified.
      - **`USING Clause`**: Specifies the source table or query that provides data for comparison.
      - **`ON Clause`**: Defines the join condition between the target and source tables.
      - **`WHEN MATCHED`**: Defines actions to take when a row in the source matches a row in the target based on the `ON` condition (e.g., `UPDATE` or `DELETE`).
      - **`WHEN NOT MATCHED [BY TARGET]`**: Defines actions to take when a row in the source does not match any row in the target (e.g., `INSERT`).
      - **`WHEN NOT MATCHED BY SOURCE`**: Defines actions to take when a row in the target does not match any row in the source (e.g., `DELETE` from target).
      - **`OUTPUT Clause`**: Allows returning information about the rows affected by the `MERGE` operation (e.g., inserted, updated, deleted rows).
  - **Example**:
    ```sql
    -- Assume we have SourceProducts and TargetProducts tables
    -- TargetProducts is our main table, SourceProducts has new/updated data

    MERGE TargetProducts AS target
    USING SourceProducts AS source
    ON (target.ProductID = source.ProductID)
    WHEN MATCHED THEN
        UPDATE SET target.ProductName = source.ProductName,
                   target.Price = source.Price,
                   target.StockQuantity = source.StockQuantity
    WHEN NOT MATCHED BY TARGET THEN
        INSERT (ProductID, ProductName, Price, StockQuantity)
        VALUES (source.ProductID, source.ProductName, source.Price, source.StockQuantity)
    WHEN NOT MATCHED BY SOURCE THEN
        DELETE
    OUTPUT $action, INSERTED.ProductID, INSERTED.ProductName, DELETED.ProductID, DELETED.ProductName;
    -- $action indicates if the row was 'INSERT', 'UPDATE', or 'DELETE'
    ```
  - **Use Case**: Data synchronization, batch processing, data warehousing (ETL processes).

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create a `Products` table and insert 5-7 diverse records using `INSERT ... VALUES`. | SSMS / Azure Data Studio | Table populated with sample data.                                     |
| 2   | Write `SELECT` queries using `WHERE`, `ORDER BY`, `GROUP BY`, and `HAVING` to answer specific business questions about your products. | SSMS / Azure Data Studio | Queries return correct filtered, sorted, and aggregated results.      |
| 3   | Update prices for a specific product category and delete all products that are out of stock. | SSMS / Azure Data Studio | Data in table is updated and deleted as per conditions.             |
| 4   | Create a `SourceProducts` table and use the `MERGE` statement to synchronize it with your `Products` table. | SSMS / Azure Data Studio | `Products` table reflects inserts, updates, and deletes from `SourceProducts`. |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which `SELECT` clause is used to filter groups based on a specified condition after aggregation?**

      - A) `WHERE`
      - B) `ORDER BY`
      - C) `HAVING`
      - D) `GROUP BY`
      - **Answer:** C) `HAVING`
      - **Explanation:** `WHERE` filters individual rows *before* grouping, while `HAVING` filters aggregated groups *after* grouping.

2.  **You want to add new product records to the `Products` table, and some column values should come from an existing `StagingProducts` table. Which `INSERT` syntax is most appropriate?**

      - A) `INSERT INTO Products VALUES (...);`
      - B) `INSERT INTO Products SELECT ... FROM StagingProducts;`
      - C) `UPDATE Products SET ... FROM StagingProducts;`
      - D) `MERGE Products ... USING StagingProducts;`
      - **Answer:** B) `INSERT INTO Products SELECT ... FROM StagingProducts;`
      - **Explanation:** `INSERT INTO ... SELECT` is specifically designed to insert data from the result set of a `SELECT` query into a target table.

3.  **What is the primary benefit of using the `MERGE` statement compared to separate `INSERT`, `UPDATE`, and `DELETE` statements?**

      - A) It is always faster than separate statements.
      - B) It can perform all three DML operations in a single atomic statement, reducing multiple trips to the database.
      - C) It automatically handles all data type conversions.
      - D) It bypasses logging for faster operations.
      - **Answer:** B) It can perform all three DML operations in a single atomic statement, reducing multiple trips to the database.
      - **Explanation:** `MERGE` offers a concise and efficient way to synchronize two tables by combining insert, update, and delete logic into one transactional statement, which can reduce network overhead and improve performance in complex scenarios.

### Short Answer Questions

1.  Explain the execution order of the `SELECT` statement clauses: `FROM`, `WHERE`, `GROUP BY`, `HAVING`, `SELECT`, `ORDER BY`.
2.  Provide a SQL example where using a column alias is beneficial for readability in a complex query.
3.  Describe a real-world scenario where the `MERGE` statement would be an ideal solution.

### Practical Task

  - **Setup**: Assume you have a `Customers` table with `CustomerID` (INT PK), `FirstName` (NVARCHAR), `LastName` (NVARCHAR), `Email` (VARCHAR UNIQUE), `SignUpDate` (DATE), `LastLoginDate` (DATETIME NULL).
  - **Tasks**:
    1.  **Insert five new customers** into the `Customers` table. Make sure at least one customer has a `NULL` `LastLoginDate`.
    2.  **Select all customers** who signed up in the current year (`2025`) and order them by `LastName` in ascending order.
    3.  **Update the `LastLoginDate`** for two specific customers (by `CustomerID`) to the current timestamp.
    4.  **Count the number of customers per `SignUpDate` year**, but only show years where there are more than 2 customers.
    5.  **Delete any customers** who signed up before `2024-01-01` and have never logged in (`LastLoginDate` is NULL).
    6.  **Simulate a data synchronization using `MERGE`**:
          - Create a temporary table `#NewCustomerData` with `CustomerID`, `FirstName`, `LastName`, `Email`.
          - Insert 2-3 existing `Customers` with updated `FirstName` or `LastName` into `#NewCustomerData`.
          - Insert 1-2 completely new customers into `#NewCustomerData` (with new `CustomerID`s).
          - Use `MERGE` to synchronize `Customers` (target) with `#NewCustomerData` (source).
              - When matched by `CustomerID`, update `FirstName`, `LastName`, `Email`.
              - When not matched by target, insert the new customer.
              - When not matched by source, delete the customer from `Customers`.
          - Include the `OUTPUT` clause to show what `MERGE` action (`INSERTED`, `UPDATED`, `DELETED`) was performed for each row.
