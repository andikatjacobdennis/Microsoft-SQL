# Module SQLF03: **Fundamentals of T-SQL - Variables and Control Flow**

> **Module Objective**
> This module introduces learners to the concepts of variables and control flow statements in T-SQL. By the end, learners will be able to declare and use variables to store data temporarily, and implement conditional logic and looping constructs to manage program flow within SQL scripts and stored procedures.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Declare, initialize, and utilize T-SQL variables effectively.
2.  Implement conditional logic using `IF...ELSE` and `CASE` statements.
3.  Construct and control loops using `WHILE`, `BREAK`, and `CONTINUE`.
4.  Understand the purpose and usage of `RETURN` and `GOTO` statements.

## Reference Materials

  - **Books:**
      - *Pro T-SQL 2022* by Louis Davidson and Kevin Boles
      - *SQL Server T-SQL Querying* by Itzik Ben-Gan
  - **Online Resources:**
      - DECLARE @local\_variable (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/language-elements/declare-local-variable-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/declare-local-variable-transact-sql?view=sql-server-ver16))
      - Control-of-Flow Language (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/language-elements/control-of-flow?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/control-of-flow?view=sql-server-ver16))

-----

## Key Concepts & Detailed Content

### 1\. **T-SQL Variables**

  - **Definition**: Variables in T-SQL are temporary storage locations used to hold data values during the execution of a batch, stored procedure, or function. They are prefixed with an `@` symbol.
  - **Core Elements**:
      - **Declaration (`DECLARE`)**:
          - **Description**: Used to define a variable's name and its data type. A variable must be declared before it can be used.
          - **Syntax**: `DECLARE @variable_name data_type [ = initial_value ];`
          - **Example**:
            ```sql
            DECLARE @EmployeeID INT;
            DECLARE @TotalSales DECIMAL(10, 2) = 0.00;
            DECLARE @CurrentDate DATE;
            ```
      - **Initialization (`SET` / `SELECT`)**:
          - **Description**: Assigns an initial value to a declared variable.
          - **`SET`**: The preferred method for assigning a single value to a single variable.
              - **Syntax**: `SET @variable_name = expression;`
              - **Example**:
                ```sql
                DECLARE @Name VARCHAR(50);
                SET @Name = 'Alice';
                SELECT @Name; -- Output: Alice
                ```
          - **`SELECT`**: Can assign values to multiple variables in a single statement or assign the result of a query to a variable. If a `SELECT` query returns multiple rows for a scalar variable, the variable will be assigned the value from the last row. If no rows are returned, the variable will be NULL.
              - **Syntax**: `SELECT @variable_name = column_name FROM table_name WHERE condition;`
              - **Example**:
                ```sql
                DECLARE @MaxPrice DECIMAL(10, 2);
                SELECT @MaxPrice = MAX(Price) FROM Products;
                SELECT @MaxPrice; -- Output: Max price from Products table

                DECLARE @ProductCount INT;
                SELECT @ProductCount = COUNT(*) FROM Products WHERE Category = 'Electronics';
                SELECT @ProductCount;
                ```
      - **Scope**:
          - **Local**: Variables are local to the batch, stored procedure, or function in which they are declared. They cease to exist when the batch or procedure finishes execution. This is the most common type of variable.
          - **Global (via Temp Tables or Context)**: While T-SQL doesn't have true "global variables" in the traditional sense, temporary tables (`#temp_table`, `##global_temp_table`) or certain session context functions can sometimes serve a similar purpose for sharing data across a session or across multiple sessions, respectively. However, these are not declared as simple variables.
              - **Example of using temporary table for session-wide "global" data**:
            <!-- end list -->
            ```sql
            -- First Batch/Session
            CREATE TABLE #MyTempData (ID INT);
            INSERT INTO #MyTempData VALUES (10);
            -- This temp table persists for the current session until explicitly dropped or session ends

            -- Second Batch/Session (within the same connection)
            SELECT * FROM #MyTempData; -- Can access the data
            DROP TABLE #MyTempData;
            ```

-----

### 2\. **Control Flow Statements**

  - **Definition**: Control-of-flow statements determine the order in which T-SQL statements are executed. They allow for conditional execution and iterative processing.

#### 2.1. **Conditional Execution (`IF...ELSE`, `CASE`)**

  - **`IF...ELSE`**:
      - **Description**: Executes a block of code if a specified condition is true; optionally executes another block if the condition is false.
      - **Syntax**:
        ```sql
        IF condition
            statement | BEGIN...END
        [ELSE
            statement | BEGIN...END]
        ```
      - **Detail 1: Single Statement vs. Block**: If the `IF` or `ELSE` clause contains only one statement, `BEGIN...END` is optional. For multiple statements, `BEGIN...END` block is mandatory.
      - **Detail 2: Nested IFs**: `IF` statements can be nested within other `IF` or `ELSE` blocks.
      - **Example**:
        ```sql
        DECLARE @SalesAmount DECIMAL(10, 2) = 1500.00;

        IF @SalesAmount > 1000.00
            PRINT 'High Sales';
        ELSE IF @SalesAmount > 500.00
            PRINT 'Moderate Sales';
        ELSE
            PRINT 'Low Sales';

        -- Example with BEGIN...END block
        DECLARE @ProductStatus VARCHAR(20) = 'In Stock';
        IF @ProductStatus = 'In Stock'
        BEGIN
            PRINT 'Product is available.';
            UPDATE Products SET LastCheckedDate = GETDATE() WHERE Status = 'In Stock';
        END
        ELSE
        BEGIN
            PRINT 'Product is out of stock or discontinued.';
            INSERT INTO OutOfStockLog (ProductID, LogDate) VALUES (123, GETDATE());
        END
        ```
  - **`CASE`**:
      - **Description**: Evaluates a list of conditions and returns one of multiple possible result expressions. It can be used as an expression (within `SELECT`, `WHERE`, `ORDER BY`, etc.) or as a statement (within a `SELECT` statement directly, or with `UPDATE` for conditional assignments).
      - **Types**:
          - **Simple `CASE`**: Compares an expression to a set of simple expressions.
              - **Syntax**:
                ```sql
                CASE input_expression
                    WHEN when_expression THEN result_expression
                    [WHEN when_expression THEN result_expression...]
                    [ELSE else_result_expression]
                END
                ```
          - **Searched `CASE`**: Evaluates a set of Boolean expressions. More flexible.
              - **Syntax**:
                ```sql
                CASE
                    WHEN Boolean_expression THEN result_expression
                    [WHEN Boolean_expression THEN result_expression...]
                    [ELSE else_result_expression]
                END
                ```
      - **Example (Searched CASE)**:
        ```sql
        SELECT
            ProductName,
            Price,
            CASE
                WHEN Price > 500 THEN 'Expensive'
                WHEN Price BETWEEN 100 AND 500 THEN 'Mid-range'
                ELSE 'Affordable'
            END AS PriceCategory
        FROM Products;

        -- Example with UPDATE using CASE
        UPDATE Orders
        SET OrderStatus = CASE
                            WHEN TotalAmount > 1000 THEN 'Priority'
                            WHEN OrderDate < GETDATE() - 30 THEN 'Delayed'
                            ELSE 'Standard'
                          END
        WHERE IsShipped = 0;
        ```

#### 2.2. **Looping Constructs (`WHILE`, `BREAK`, `CONTINUE`)**

  - **`WHILE`**:
      - **Description**: Repeatedly executes a statement block as long as the specified condition is true.
      - **Syntax**:
        ```sql
        WHILE Boolean_expression
        BEGIN
            { sql_statement | statement_block }
            [BREAK]
            [CONTINUE]
        END
        ```
      - **Detail 1: Loop Termination**: Ensure the `Boolean_expression` eventually becomes false, or an infinite loop will occur.
      - **Example**:
        ```sql
        DECLARE @Counter INT = 1;
        WHILE @Counter <= 5
        BEGIN
            PRINT 'Current count: ' + CAST(@Counter AS VARCHAR(10));
            SET @Counter = @Counter + 1;
        END;
        ```
  - **`BREAK`**:
      - **Description**: Exits the innermost `WHILE` loop immediately. Control passes to the statement immediately following the `END` keyword of the loop.
      - **Example**:
        ```sql
        DECLARE @i INT = 1;
        WHILE @i <= 10
        BEGIN
            IF @i = 6
                BREAK; -- Exit loop when i is 6
            PRINT @i;
            SET @i = @i + 1;
        END; -- Output: 1, 2, 3, 4, 5
        ```
  - **`CONTINUE`**:
      - **Description**: Causes the `WHILE` loop to restart from the beginning (re-evaluates the `Boolean_expression`), skipping any statements remaining in the current iteration.
      - **Example**:
        ```sql
        DECLARE @j INT = 0;
        WHILE @j < 5
        BEGIN
            SET @j = @j + 1;
            IF @j = 3
                CONTINUE; -- Skip printing when j is 3
            PRINT @j;
        END; -- Output: 1, 2, 4, 5
        ```

#### 2.3. **Flow Control (`RETURN`, `GOTO`)**

  - **`RETURN`**:
      - **Description**: Unconditionally exits a query or procedure. Statements following `RETURN` in the current scope are not executed. Can return an integer value as a status.
      - **Syntax**: `RETURN [integer_expression];`
      - **Use Case**: Exiting a stored procedure early based on a condition, or returning a success/failure status code.
      - **Example**:
        ```sql
        CREATE PROCEDURE GetProductDetails @ProductID INT
        AS
        BEGIN
            IF @ProductID IS NULL
            BEGIN
                PRINT 'Product ID cannot be NULL.';
                RETURN 1; -- Indicate an error
            END

            SELECT * FROM Products WHERE ProductID = @ProductID;

            IF @@ROWCOUNT = 0
            BEGIN
                PRINT 'Product not found.';
                RETURN 2; -- Indicate no product found
            END

            RETURN 0; -- Indicate success
        END;
        ```
  - **`GOTO`**:
      - **Description**: Unconditionally jumps to a statement label within the same batch, stored procedure, or function.
      - **Syntax**: `GOTO label;`
      - **Use Case**: Generally discouraged in modern T-SQL programming due to making code harder to read, debug, and maintain (leads to "spaghetti code"). Primarily exists for backward compatibility or specific error handling patterns.
      - **Example (Caution: Avoid this in new code)**:
        ```sql
        DECLARE @Count INT = 0;
        MyLabel:
            SET @Count = @Count + 1;
            PRINT @Count;
            IF @Count < 5
                GOTO MyLabel;
        PRINT 'Done.';
        ```

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Declare and initialize variables using both `SET` and `SELECT` from existing table data. | SSMS / Azure Data Studio | Variables hold correct values as observed with `SELECT @variable_name`. |
| 2   | Write a script using `IF...ELSE IF...ELSE` to categorize a numeric value (e.g., price into "Low", "Medium", "High"). | SSMS / Azure Data Studio | Script prints the correct category based on input values.             |
| 3   | Implement a `WHILE` loop with `BREAK` and `CONTINUE` to process numbers, skipping certain values and stopping at a condition. | SSMS / Azure Data Studio | Loop runs as expected, demonstrating flow control.                    |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which statement is used to declare a local variable in T-SQL?**

      - A) `CREATE VARIABLE`
      - B) `DEFINE VAR`
      - C) `DECLARE`
      - D) `SET`
      - **Answer:** C) `DECLARE`
      - **Explanation:** `DECLARE` is the T-SQL keyword specifically used to define local variables, including their name and data type.

2.  **What happens if a `SELECT` statement used to assign a value to a scalar variable returns no rows?**

      - A) The variable retains its previous value.
      - B) The variable is set to `0`.
      - C) The variable is set to `NULL`.
      - D) An error is raised.
      - **Answer:** C) The variable is set to `NULL`.
      - **Explanation:** If a scalar `SELECT` assignment returns no rows, the variable will be set to `NULL`. If it returns multiple rows, it will take the value from the last row.

3.  **Which control flow statement immediately exits the innermost `WHILE` loop?**

      - A) `RETURN`
      - B) `CONTINUE`
      - C) `EXIT`
      - D) `BREAK`
      - **Answer:** D) `BREAK`
      - **Explanation:** `BREAK` is used to terminate the loop execution immediately, moving control to the statement after the `WHILE` loop. `CONTINUE` skips the current iteration and goes to the next.

### Short Answer Questions

1.  Explain the primary difference between using `SET` and `SELECT` to assign values to a T-SQL variable. Provide an example where `SELECT` would be more appropriate.
2.  Describe a practical scenario where a `CASE` expression would be more efficient or readable than a series of nested `IF...ELSE IF` statements.
3.  Discuss why the `GOTO` statement is generally discouraged in modern T-SQL programming.

### Practical Task

  - Write a T-SQL script that:
    1.  Declares two variables: `@ProductName` (VARCHAR) and `@StockQuantity` (INT).
    2.  Uses a `SELECT` statement to get the `ProductName` and `UnitsInStock` (or a similar column) for a specific `ProductID` from your `Products` table (you can create a simple `Products` table if you don't have one, with at least `ProductID`, `ProductName`, `UnitsInStock`).
    3.  Uses an `IF...ELSE IF...ELSE` structure to print messages based on `@StockQuantity`:
          - "Product is out of stock\!" if `@StockQuantity` is 0.
          - "Low stock, order more\!" if `@StockQuantity` is between 1 and 10 (inclusive).
          - "Product is well stocked." if `@StockQuantity` is greater than 10.
    4.  Implement a `WHILE` loop that decrements a variable (e.g., `@Countdown` starting from 5) and prints its value in each iteration. Inside the loop, if the `@Countdown` reaches 2, use `CONTINUE` to skip printing that specific value, and if it reaches 0, use `BREAK` to exit the loop.
