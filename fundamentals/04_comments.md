# Module SQLF04: **Fundamentals of T-SQL - Comments**

> **Module Objective**
> This module aims to introduce learners to the importance and syntax of comments in T-SQL. By the end, learners will be able to effectively use single-line and multi-line comments to enhance code readability, maintainability, and documentation within their SQL scripts.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Understand the purpose and benefits of using comments in T-SQL code.
2.  Correctly implement single-line comments using `--`.
3.  Correctly implement multi-line comments using `/* */`.
4.  Apply best practices for commenting to improve code documentation.

## Reference Materials

  - **Books:**
      - *SQL Server 2022 Querying for Dummies* by Michael Meys
      - *Developing Microsoft SQL Server 2012 Databases* by Alan Beaulieu
  - **Online Resources:**
      - Comments (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/language-elements/comments-transact-sql?view=sql-server-ver16](https://www.google.com/search?q=https://learn.microsoft.com/en-us/sql/t-sql/language-elements/comments-transact-sql%3Fview%3Dsql-server-ver16))
      - SQL Comments: A Quick Guide (SQLShack) ([https://www.sqlshack.com/sql-comments-a-quick-guide/](https://www.google.com/search?q=https://www.sqlshack.com/sql-comments-a-quick-guide/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to Comments in T-SQL**

  - **Definition**: Comments are non-executable text within SQL code that are ignored by the SQL Server engine during execution. They serve as explanatory notes for humans reading the code.
  - **Purpose and Importance**:
      - **Code Readability**: Explains complex logic or business rules, making the code easier to understand for anyone (including your future self).
      - **Maintainability**: Helps in debugging and modifying code by quickly understanding the intent of different sections.
      - **Documentation**: Provides inline documentation for scripts, stored procedures, functions, and triggers.
      - **Debugging/Temporary Disabling**: Can be used to temporarily disable (comment out) parts of a query or script for testing or debugging purposes without deleting the code.

-----

### 2\. **Types of Comments**

#### 2.1. **Single-line Comment (`--`)**

  - **Description**: Used to comment out a single line or a portion of a line from the comment markers to the end of the line.
  - **Syntax**: `-- your comment text here`
  - **Usage**:
      - **Entire Line**:
        ```sql
        -- This query retrieves all active customers.
        SELECT CustomerID, FirstName, LastName
        FROM Customers
        WHERE IsActive = 1;
        ```
      - **End of Line**:
        ```sql
        SELECT OrderID, OrderDate, TotalAmount -- Select order details
        FROM Orders
        WHERE OrderDate >= '2024-01-01'; -- Filter orders from the current year
        ```
  - **Core Elements**:
      - The two hyphens `--` must be on the same line.
      - Anything after `--` on that line is considered a comment.

#### 2.2. **Multi-line Comment (`/* */`)**

  - **Description**: Used to comment out blocks of code or multi-line explanations. Everything between the opening `/*` and closing `*/` is considered a comment, including line breaks.
  - **Syntax**:
    ```sql
    /*
    Your multi-line
    comment text goes here.
    It can span multiple lines.
    */
    ```
  - **Usage**:
      - **Code Block Documentation**:
        ```sql
        /*
        This stored procedure calculates the total sales
        for a given product category and updates the
        CategorySummary table.
        Input parameter: @CategoryName VARCHAR(100)
        */
        CREATE PROCEDURE CalculateCategorySales
            @CategoryName VARCHAR(100)
        AS
        BEGIN
            -- ... (procedure logic here)
            SELECT SUM(TotalAmount) FROM Sales
            WHERE ProductCategory = @CategoryName;
        END;
        ```
      - **Temporarily Disabling Code**:
        ```sql
        SELECT ProductID, ProductName, Price
        FROM Products
        /*
        WHERE Price > 100 AND
              UnitsInStock > 0;
        */
        ORDER BY ProductName;
        ```
  - **Core Elements**:
      - The comment starts with `/*` and ends with `*/`.
      - All text between these markers, regardless of line breaks, is a comment.
      - **Nesting**: Multi-line comments can be nested. For example: `/* Outer comment /* Inner comment */ Still outer */`. However, this can lead to confusion and is generally avoided in favor of clear, non-nested blocks.

-----

### 3\. **Best Practices for Commenting**

  - **Key concept 1: Comment What and Why, Not How**:
      - **Explanation**: Focus on explaining *what* a piece of code does from a business logic perspective and *why* it's implemented in a certain way, rather than merely restating *how* the SQL syntax works. The code itself should ideally be self-explanatory regarding "how."
      - **Bad Example**:
        ```sql
        -- This selects ProductID
        SELECT ProductID FROM Products;
        ```
      - **Good Example**:
        ```sql
        -- Retrieve product IDs that are part of the 'Electronics' category
        -- This helps in identifying items eligible for the Q3 promotional discount.
        SELECT ProductID FROM Products WHERE Category = 'Electronics';
        ```
  - **Key concept 2: Keep Comments Up-to-Date**:
      - **Explanation**: Outdated comments can be worse than no comments, as they can mislead developers. Always update comments when the corresponding code changes.
  - **Key concept 3: Use Comments for Complex Logic**:
      - **Explanation**: Not every line needs a comment. Focus on areas where the logic is intricate, unusual, or involves specific business rules.
  - **Key concept 4: File Headers for Major Scripts/Procedures**:
      - **Explanation**: For larger scripts, stored procedures, or functions, include a multi-line comment at the beginning that describes its purpose, author, creation date, modification history, input parameters, and expected output.
      - **Case study or example**:
        ```sql
        /*
        -------------------------------------------------------------------
        Name: usp_GetCustomerOrderHistory
        Author: Your Name
        Date: 2025-07-20
        Description: Retrieves detailed order history for a specific customer,
                     including product details and order totals.
        Parameters:
            @CustomerID INT - The unique identifier for the customer.
        Returns: A result set containing order details.
        Change History:
            2025-07-20: Initial creation.
            2025-08-15: Added product categories to the output.
        -------------------------------------------------------------------
        */
        CREATE PROCEDURE usp_GetCustomerOrderHistory
            @CustomerID INT
        AS
        BEGIN
            -- ... (procedure body)
        END;
        ```

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Add a single-line comment to explain a `SELECT` statement's purpose.     | SSMS / Azure Data Studio | The comment appears correctly and is ignored during execution.        |
| 2   | Create a multi-line comment to describe a hypothetical stored procedure's function, parameters, and author. | SSMS / Azure Data Studio | A well-formatted header comment is present.                           |
| 3   | Temporarily comment out a `WHERE` clause in a query using multi-line comments and verify the query executes without the filter. | SSMS / Azure Data Studio | Query results change as expected when the `WHERE` clause is commented out. |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which of the following is the correct syntax for a single-line comment in T-SQL?**

      - A) `# This is a comment`
      - B) `// This is a comment`
      - C) `-- This is a comment`
      - D) `' This is a comment`
      - **Answer:** C) `-- This is a comment`
      - **Explanation:** The double-hyphen `--` is the standard T-SQL syntax for single-line comments.

2.  **To comment out a large block of code that spans multiple lines, which comment syntax should you use?**

      - A) `--` at the start of each line
      - B) `/* */`
      - C) `//`
      - D) `#`
      - **Answer:** B) `/* */`
      - **Explanation:** The `/* */` syntax is designed for multi-line comments and blocks of code, as it comments out everything between the start and end markers.

3.  **What is the primary benefit of adding comments to your T-SQL code?**

      - A) It makes the code execute faster.
      - B) It encrypts sensitive information.
      - C) It improves code readability and maintainability.
      - D) It automatically generates documentation.
      - **Answer:** C) It improves code readability and maintainability.
      - **Explanation:** Comments are crucial for making code understandable to humans, which directly contributes to easier debugging, updates, and overall maintenance.

### Short Answer Questions

1.  Beyond improving readability, list two other practical uses for comments in T-SQL development.
2.  Can `/* */` comments be nested within each other in SQL Server? Provide a simple example if yes, or explain why not.
3.  You've changed the logic of an existing stored procedure. What best practice related to comments should you follow immediately after making the change?

### Practical Task

  - Open an existing SQL script or write a new one that includes at least one `SELECT` statement, one `INSERT` statement, and one `UPDATE` statement.
  - Add a multi-line header comment at the top of the script explaining its overall purpose, who authored it, and the date.
  - Add a single-line comment to each `SELECT`, `INSERT`, and `UPDATE` statement, briefly explaining what that specific statement does (e.g., "Retrieves customer data," "Inserts a new product record").
  - Temporarily comment out one of your `INSERT` or `UPDATE` statements using the multi-line comment syntax and execute the script to verify the statement was skipped.
