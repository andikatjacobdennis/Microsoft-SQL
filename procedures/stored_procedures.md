# Module SQLPRC01: **Stored Procedures**

> **Module Objective**
> This module aims to provide a comprehensive understanding of T-SQL Stored Procedures. Learners will learn how to create, execute, and manage stored procedures, including passing parameters and handling output, enabling them to encapsulate complex business logic, improve performance, and enhance database security.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Define and understand the benefits of using stored procedures in SQL Server.
2.  Create stored procedures with input and output parameters.
3.  Execute stored procedures using `EXEC` or `EXECUTE`.
4.  Modify existing stored procedures using `ALTER PROCEDURE`.
5.  Remove stored procedures using `DROP PROCEDURE`.
6.  Implement basic error handling within stored procedures.

## Reference Materials

  - **Books:**
      - *T-SQL Programming (Developer Reference)* by Itzik Ben-Gan
      - *Pro T-SQL 2022* by Louis Davidson and Kevin Boles
  - **Online Resources:**
      - CREATE PROCEDURE (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/statements/create-procedure-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-procedure-transact-sql?view=sql-server-ver16))
      - EXECUTE (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/language-elements/execute-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/execute-transact-sql?view=sql-server-ver16))
      - SQL Server Stored Procedures: The Basics (SQLShack) ([https://www.sqlshack.com/sql-server-stored-procedures-the-basics/](https://www.google.com/search?q=https://www.sqlshack.com/sql-server-stored-procedures-the-basics/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to Stored Procedures**

  - **Definition**: A stored procedure is a prepared SQL code that you can save, so the code can be reused over and over again. It's a collection of T-SQL statements compiled into a single execution plan.
  - **Benefits**:
      - **Performance**: Once compiled, the execution plan is cached, leading to faster execution for subsequent calls.
      - **Security**: Users can be granted permissions to execute procedures without directly accessing the underlying tables, enhancing data security.
      - **Modularity and Reusability**: Encapsulate complex business logic, reducing code duplication and making applications easier to maintain.
      - **Reduced Network Traffic**: Executing a single procedure call over the network is more efficient than sending multiple individual SQL statements.
      - **Data Integrity**: Enforce business rules consistently across applications.
  - **Limitations**:
      - **Limited Portability**: T-SQL stored procedures are specific to SQL Server syntax.
      - **Debugging**: Can be more challenging to debug than simple ad-hoc queries, especially for very complex procedures.

-----

### 2\. **Creating and Executing Stored Procedures**

#### 2.1. **`CREATE PROCEDURE`**

  - **Definition**: The statement used to define and create a new stored procedure.
  - **Syntax**:
    ```sql
    CREATE PROCEDURE [ schema_name. ] procedure_name
        [ { @parameter_name [ AS ] data_type [ VARYING ]
            [ = default ] [ OUT | OUTPUT | READONLY ]
          } [ ,...n ]
        ]
    [ WITH <procedure_option> [ ,...n ] ]
    [ FOR REPLICATION ]
    AS
        { [ BEGIN ] sql_statement [ ; ] [ ...n ] [ END ] } ;
    ```
  - **Key Elements**:
      - `procedure_name`: The name of the stored procedure.
      - `@parameter_name`: Variables used to pass values into or out of the procedure.
          - **Input Parameters (default)**: Pass values into the procedure.
          - **Output Parameters (`OUT` or `OUTPUT`)**: Pass values back from the procedure to the calling environment.
          - **`READONLY`**: Specifies that the parameter cannot be modified within the procedure (for table-valued parameters).
      - `AS BEGIN ... END`: Defines the body of the procedure containing the T-SQL statements.
  - **Example (Basic)**:
    ```sql
    CREATE PROCEDURE GetCustomerDetails
        @CustomerID INT
    AS
    BEGIN
        SELECT CustomerID, FirstName, LastName, Email
        FROM Customers
        WHERE CustomerID = @CustomerID;
    END;
    GO
    ```
  - **Example (With Output Parameter)**:
    ```sql
    CREATE PROCEDURE GetProductCountByCategory
        @CategoryName NVARCHAR(100),
        @ProductCount INT OUTPUT
    AS
    BEGIN
        SELECT @ProductCount = COUNT(*)
        FROM Products
        WHERE Category = @CategoryName;
    END;
    GO
    ```

#### 2.2. **`EXECUTE` Procedure (`EXEC` / `EXECUTE`)**

  - **Definition**: Used to run a stored procedure. `EXEC` is a shorthand alias for `EXECUTE`.
  - **Syntax**:
    ```sql
    EXEC[UTE] [ @return_status = ] { procedure_name [ ; number ] | @procedure_name_var }
        [ [ @parameter = ] { value | @variable [ OUTPUT ] | [ DEFAULT ] }
            [ ,...n ]
        ]
    [ WITH RECOMPILE ] ;
    ```
  - **With Parameters**:
      - **Positional**: Pass values in the order they are declared in the procedure.
          - `EXEC GetCustomerDetails 101;`
      - **Named**: Pass values by specifying the parameter name, allowing for any order.
          - `EXEC GetCustomerDetails @CustomerID = 101;`
  - **Handling Output Parameters**:
      - You need to declare a variable in the calling scope to capture the output.
      - **Example**:
        ```sql
        DECLARE @Count INT;
        EXEC GetProductCountByCategory @CategoryName = 'Electronics', @ProductCount = @Count OUTPUT;
        SELECT 'Number of Electronics Products: ' + CAST(@Count AS VARCHAR(10));
        ```
  - **Return Status**: Procedures can return an integer status value (0 for success, non-zero for error).
      - **Example**:
        ```sql
        CREATE PROCEDURE AddNewProduct
            @ProductName NVARCHAR(100),
            @Price DECIMAL(10, 2)
        AS
        BEGIN
            IF @ProductName IS NULL OR @Price <= 0
            BEGIN
                RETURN -1; -- Indicate invalid input
            END

            INSERT INTO Products (ProductName, Price) VALUES (@ProductName, @Price);
            RETURN 0; -- Indicate success
        END;
        GO

        DECLARE @Status INT;
        EXEC @Status = AddNewProduct 'Laptop', 1200.00;
        IF @Status = 0
            PRINT 'Product added successfully.';
        ELSE
            PRINT 'Failed to add product. Status: ' + CAST(@Status AS VARCHAR(10));
        ```

-----

### 3\. **Modifying and Dropping Stored Procedures**

#### 3.1. **`ALTER PROCEDURE`**

  - **Definition**: Used to change the definition of an existing stored procedure. The syntax is almost identical to `CREATE PROCEDURE`, but you use `ALTER` instead of `CREATE`.
  - **Syntax**: Same as `CREATE PROCEDURE`, but replace `CREATE` with `ALTER`.
  - **Example**:
    ```sql
    ALTER PROCEDURE GetCustomerDetails
        @CustomerID INT,
        @IncludeEmail BIT = 0 -- Added new parameter with default value
    AS
    BEGIN
        SELECT CustomerID, FirstName, LastName,
               CASE WHEN @IncludeEmail = 1 THEN Email ELSE NULL END AS Email
        FROM Customers
        WHERE CustomerID = @CustomerID;
    END;
    GO
    ```

#### 3.2. **`DROP PROCEDURE`**

  - **Definition**: Used to remove one or more existing stored procedures from the database.
  - **Syntax**: `DROP PROCEDURE [ IF EXISTS ] { schema_name. } procedure_name [ ,...n ] ;`
  - **Example**:
    ```sql
    DROP PROCEDURE GetCustomerDetails;
    DROP PROCEDURE IF EXISTS GetProductCountByCategory, AddNewProduct;
    ```
  - **Case Study: Error Handling with `TRY...CATCH`**:
      - Stored procedures are ideal for robust error handling. The `TRY...CATCH` block allows you to gracefully manage runtime errors.
      - **Explanation**:
          - The `TRY` block contains the SQL statements that might generate an error.
          - If an error occurs within the `TRY` block, control immediately jumps to the `CATCH` block.
          - The `CATCH` block contains statements to handle the error (e.g., log the error, display a custom message, rollback a transaction).
      - **Example**:
        ```sql
        CREATE PROCEDURE InsertNewOrder
            @CustomerID INT,
            @OrderTotal DECIMAL(10, 2)
        AS
        BEGIN
            SET NOCOUNT ON; -- Suppress row count messages

            BEGIN TRY
                BEGIN TRANSACTION; -- Start a transaction

                IF NOT EXISTS (SELECT 1 FROM Customers WHERE CustomerID = @CustomerID)
                BEGIN
                    RAISERROR('Customer ID does not exist.', 16, 1); -- Raise a custom error
                END

                INSERT INTO Orders (CustomerID, OrderDate, TotalAmount)
                VALUES (@CustomerID, GETDATE(), @OrderTotal);

                COMMIT TRANSACTION; -- Commit changes if successful
                PRINT 'Order inserted successfully.';
            END TRY
            BEGIN CATCH
                IF @@TRANCOUNT > 0
                    ROLLBACK TRANSACTION; -- Rollback if an error occurred within the transaction

                -- Get error details
                DECLARE @ErrorMessage NVARCHAR(MAX) = ERROR_MESSAGE();
                DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
                DECLARE @ErrorState INT = ERROR_STATE();

                -- Log the error (e.g., to an ErrorLog table)
                INSERT INTO ErrorLog (ErrorMessage, ErrorSeverity, ErrorState, ErrorTime)
                VALUES (@ErrorMessage, @ErrorSeverity, @ErrorState, GETDATE());

                -- Re-raise the error to the calling application
                RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
            END CATCH;
        END;
        GO
        ```

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create a stored procedure that accepts an `EmployeeID` and returns that employee's `FirstName` and `LastName`. | SSMS / Azure Data Studio | Procedure executes successfully, returning correct employee details. |
| 2   | Modify the procedure from Task 1 to include an output parameter that returns the employee's `Salary`. Call the procedure and capture the output. | SSMS / Azure Data Studio | Output parameter correctly captures and displays the salary.         |
| 3   | Create a new stored procedure that attempts an `INSERT` operation and includes a `TRY...CATCH` block for error handling. Test with valid and invalid data. | SSMS / Azure Data Studio | Procedure handles errors gracefully, logging or printing messages.   |
| 4   | Alter one of your existing procedures to add new logic or change a parameter. Then, drop another procedure. | SSMS / Azure Data Studio | Procedure definition updated; procedure successfully dropped.         |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which keyword is used to define an output parameter in a stored procedure?**

      - A) `IN`
      - B) `RETURN`
      - C) `OUT` or `OUTPUT`
      - D) `RESULT`
      - **Answer:** C) `OUT` or `OUTPUT`
      - **Explanation:** Both `OUT` and `OUTPUT` keywords are used interchangeably to designate a parameter as an output parameter, allowing values to be passed back to the calling environment.

2.  **What is a primary benefit of using stored procedures in terms of database performance?**

      - A) They always use less memory than ad-hoc queries.
      - B) Their execution plans are often cached, leading to faster subsequent executions.
      - C) They automatically optimize all `SELECT` statements within them.
      - D) They reduce the need for indexing tables.
      - **Answer:** B) Their execution plans are often cached, leading to faster subsequent executions.
      - **Explanation:** When a stored procedure is executed for the first time, SQL Server creates and caches an execution plan. Subsequent calls can reuse this plan, saving compilation time and improving performance.

3.  **Which T-SQL construct is best suited for handling runtime errors within a stored procedure?**

      - A) `IF...ELSE`
      - B) `WHILE` loop
      - C) `TRY...CATCH` block
      - D) `GOTO` statement
      - **Answer:** C) `TRY...CATCH` block
      - **Explanation:** The `TRY...CATCH` block is specifically designed for structured error handling in T-SQL, allowing you to intercept and manage errors that occur during execution.

### Short Answer Questions

1.  List three distinct advantages of using stored procedures over embedding direct SQL queries in application code.
2.  Explain how you would capture the return status of a stored procedure in a calling batch. Provide a simple example.
3.  Describe a scenario where `ALTER PROCEDURE` would be used instead of dropping and recreating a procedure.

### Practical Task

  - **Setup**: Assume you have a `Products` table with `ProductID` (INT PK), `ProductName` (NVARCHAR), `Price` (DECIMAL), `StockQuantity` (INT).
  - **Tasks**:
    1.  **Create a Stored Procedure `usp_UpdateProductStock`**:
          - It should take `@ProductID INT` and `@QuantityChange INT` as input parameters.
          - Inside the procedure, update the `StockQuantity` for the given `ProductID` by adding `@QuantityChange`.
          - Include an output parameter `@NewStockQuantity INT OUTPUT` that returns the updated stock quantity.
          - Implement basic error handling using `TRY...CATCH`:
              - If `ProductID` does not exist, raise an error message like 'Product not found.'
              - If the `@QuantityChange` would result in a negative `StockQuantity`, raise an error like 'Stock quantity cannot be negative.' and rollback any changes.
              - Log any other unexpected errors (you can just `PRINT` them for this exercise).
    2.  **Test `usp_UpdateProductStock`**:
          - Call it with a valid `ProductID` and a positive `@QuantityChange`. Capture and print the `@NewStockQuantity`.
          - Call it with a valid `ProductID` and a negative `@QuantityChange` that would result in negative stock. Observe the error message.
          - Call it with an invalid `ProductID`. Observe the error message.
    3.  **Create another Stored Procedure `usp_GetLowStockProducts`**:
          - It should take `@Threshold INT` as an input parameter.
          - It should return all `ProductID`, `ProductName`, and `StockQuantity` for products where `StockQuantity` is less than or equal to `@Threshold`.
          - If `@Threshold` is negative, return an error message and a status of -1 using `RETURN`.
    4.  **Test `usp_GetLowStockProducts`**:
          - Call it with a valid threshold (e.g., 10).
          - Call it with a negative threshold and capture its return status.
    5.  **Alter `usp_UpdateProductStock`**: Modify it to also update a `LastUpdatedDate` column (you'll need to add this column to your `Products` table first) to `GETDATE()` whenever the stock is updated.
    6.  **Drop `usp_GetLowStockProducts`**.
