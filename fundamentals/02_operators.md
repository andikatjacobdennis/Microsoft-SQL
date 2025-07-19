# Module SQLF02: **Fundamentals of T-SQL - Operators**

> **Module Objective**
> This module aims to familiarize learners with the various types of operators available in T-SQL, enabling them to construct powerful and precise queries for data manipulation and retrieval. Understanding operators is crucial for filtering, calculating, and comparing data effectively.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Identify and correctly use different categories of T-SQL operators.
2.  Construct `WHERE` clauses and `SELECT` statements using comparison and logical operators to filter and combine data.
3.  Perform arithmetic calculations and bitwise manipulations on numerical data.

## Reference Materials

  - **Books:**
      - *T-SQL Fundamentals* by Itzik Ben-Gan
      - *SQL Server Query Performance Tuning Distilled* by Grant Fritchey
  - **Online Resources:**
      - Operators (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/language-elements/operators-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/operators-transact-sql?view=sql-server-ver16))
      - SQL Server Operators: The Basics (SQLShack) ([https://www.sqlshack.com/sql-server-operators-the-basics/](https://www.google.com/search?q=https://www.sqlshack.com/sql-server-operators-the-basics/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to T-SQL Operators**

  - **Definition**: Operators are symbols or keywords that specify an action to be performed on one or more expressions in T-SQL. They are used to combine, compare, and modify values, playing a fundamental role in nearly all SQL statements.
  - **Types**: T-SQL operators can be broadly categorized into Arithmetic, Comparison, Logical, and Bitwise operators, each serving a distinct purpose in query construction.
  - **Operator Precedence**: When multiple operators are used in an expression, SQL Server evaluates them in a specific order (precedence). Parentheses `()` can be used to override this order.

-----

### 2\. **Common Operator Categories**

#### 2.1. **Arithmetic Operators**

  - **Definition**: Used to perform mathematical calculations on numeric data types.
  - **Operators**:
      - **`+` (Addition)**: Adds two numbers.
          - `SELECT 10 + 5;` -- Result: 15
      - **`-` (Subtraction)**: Subtracts one number from another.
          - `SELECT 10 - 5;` -- Result: 5
      - **`*` (Multiplication)**: Multiplies two numbers.
          - `SELECT 10 * 5;` -- Result: 50
      - **`/` (Division)**: Divides one number by another. Note: Integer division truncates the decimal part.
          - `SELECT 10 / 3;` -- Result: 3 (Integer division)
          - `SELECT 10.0 / 3;` -- Result: 3.333333
      - **`%` (Modulo)**: Returns the integer remainder of a division.
          - `SELECT 10 % 3;` -- Result: 1

#### 2.2. **Comparison Operators**

  - **Definition**: Used to compare two expressions. They always return a Boolean result: TRUE, FALSE, or UNKNOWN (if NULLs are involved). Primarily used in `WHERE` and `HAVING` clauses.
  - **Operators**:
      - **`=` (Equal to)**: Checks if two expressions are equal.
          - `SELECT * FROM Products WHERE Price = 25.00;`
      - **`!=` or `<>` (Not Equal to)**: Checks if two expressions are not equal.
          - `SELECT * FROM Orders WHERE OrderStatus != 'Completed';`
      - **`>` (Greater than)**: Checks if the left expression is greater than the right.
          - `SELECT * FROM Employees WHERE Salary > 50000;`
      - **`<` (Less than)**: Checks if the left expression is less than the right.
          - `SELECT * FROM Customers WHERE Age < 18;`
      - **`>=` (Greater than or Equal to)**:
          - `SELECT * FROM Products WHERE StockQuantity >= 100;`
      - **`<=` (Less than or Equal to)**:
          - `SELECT * FROM Sales WHERE SaleDate <= '2024-12-31';`
  - **Special Comparison Operators**:
      - **`BETWEEN`**: Checks if a value is within a specified range (inclusive).
          - `SELECT * FROM Products WHERE Price BETWEEN 20.00 AND 30.00;`
      - **`LIKE`**: Used for pattern matching with string data (`%` for any string, `_` for any single character).
          - `SELECT * FROM Customers WHERE FirstName LIKE 'J%';`
      - **`IN`**: Checks if a value matches any value in a list of values.
          - `SELECT * FROM Orders WHERE OrderStatus IN ('Pending', 'Processing');`
      - **`IS NULL` / `IS NOT NULL`**: Checks for NULL values. Note: `NULL` is not equal to `NULL`.
          - `SELECT * FROM Employees WHERE ManagerID IS NULL;`

#### 2.3. **Logical Operators**

  - **Definition**: Used to combine or modify conditions in `WHERE` and `HAVING` clauses.
  - **Operators**:
      - **`AND`**: Returns TRUE if all conditions are TRUE.
          - `SELECT * FROM Products WHERE Category = 'Electronics' AND Price > 100;`
      - **`OR`**: Returns TRUE if any condition is TRUE.
          - `SELECT * FROM Customers WHERE City = 'New York' OR City = 'Los Angeles';`
      - **`NOT`**: Reverses the logical state of an expression.
          - `SELECT * FROM Orders WHERE NOT OrderStatus = 'Shipped';` (Equivalent to `OrderStatus != 'Shipped'`)

-----

### 3\. **Bitwise Operators and Operator Precedence**

#### 3.1. **Bitwise Operators**

  - **Definition**: Perform bit manipulations between two integer expressions. They operate on the individual bits of the integer values.
  - **Operators**:
      - **`&` (Bitwise AND)**: Performs a logical AND operation on each bit.
          - `SELECT 5 & 3;` -- Binary: 0101 AND 0011 = 0001 (Result: 1)
      - **`|` (Bitwise OR)**: Performs a logical OR operation on each bit.
          - `SELECT 5 | 3;` -- Binary: 0101 OR 0011 = 0111 (Result: 7)
      - **`^` (Bitwise Exclusive OR - XOR)**: Performs a logical XOR operation on each bit.
          - `SELECT 5 ^ 3;` -- Binary: 0101 XOR 0011 = 0110 (Result: 6)
      - **`~` (Bitwise NOT)**: Inverts the bits of its operand. (Note: This is a unary operator)
          - `SELECT ~5;` -- Result: -6 (Due to two's complement representation)
  - **Case Study/Example**:
      - Bitwise operators are often used in scenarios like checking specific flags stored in an integer column (e.g., permissions where each bit represents a different permission).
    <!-- end list -->
    ```sql
    -- Assume Permissions column stores integer where:
    -- Bit 0 (1): Read
    -- Bit 1 (2): Write
    -- Bit 2 (4): Delete

    -- To check if a user has Write permission (Bit 1 is set)
    SELECT UserName FROM Users WHERE (Permissions & 2) = 2;

    -- To add Read permission (Bit 0) to a user's existing permissions
    UPDATE Users SET Permissions = Permissions | 1 WHERE UserName = 'JohnDoe';
    ```

#### 3.2. **Operator Precedence**

  - **Explanation**: When an expression contains multiple operators, SQL Server evaluates them in a specific order. Operators with higher precedence are evaluated before operators with lower precedence. Operators with the same precedence are evaluated from left to right.
  - **Order (High to Low, simplified)**:
    1.  Arithmetic Operators (`*`, `/`, `%` then `+`, `-`)
    2.  Comparison Operators (`=`, `>`, `<`, `>=`, `<=`, `! =`, `< >`, `! >`, `! <`, `LIKE`, `IN`, `BETWEEN`, `IS NULL`)
    3.  Bitwise Operators (`&`, `^`, `|`)
    4.  Logical Operators (`NOT` then `AND` then `OR`)
  - **Override with Parentheses**: Parentheses `()` can be used to explicitly define the order of evaluation. Expressions within parentheses are always evaluated first.
      - Example: `SELECT (10 + 5) * 2;` -- Result: 30 (Addition first due to parentheses)
      - Example: `SELECT 10 + 5 * 2;` -- Result: 20 (Multiplication first due to precedence)

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Write `SELECT` statements demonstrating each arithmetic operator.        | SSMS / Azure Data Studio | Correct calculation results are displayed.                              |
| 2   | Create `WHERE` clauses using various comparison and logical operators to filter data from a sample table. | SSMS / Azure Data Studio | Queries return the expected filtered rows.                            |
| 3   | Experiment with bitwise operators by setting and checking flags in an integer column. | SSMS / Azure Data Studio | Understanding of bitwise operations confirmed by results.              |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which operator returns the remainder of a division operation?**

      - A) `/`
      - B) `*`
      - C) `%`
      - D) `^`
      - **Answer:** C) `%`
      - **Explanation:** The modulo operator (`%`) is specifically used to get the remainder of a division.

2.  **To retrieve all products with a price greater than or equal to $50 and less than $100, which combination of operators is most suitable?**

      - A) `>` AND `<`
      - B) `>=` AND `<`
      - C) `BETWEEN` AND `AND`
      - D) `>=` OR `<`
      - **Answer:** B) `>=` AND `<`
      - **Explanation:** `BETWEEN` is inclusive, so `BETWEEN 50 AND 100` would include 100. To specifically exclude 100, `>=` and `<` is the correct combination.

3.  **In the expression `10 + 5 * 2`, what is the result according to operator precedence in T-SQL?**

      - A) 30
      - B) 20
      - C) 25
      - D) 17
      - **Answer:** B) 20
      - **Explanation:** Multiplication (`*`) has higher precedence than addition (`+`). So, `5 * 2` is evaluated first (10), then `10 + 10` is performed, resulting in 20.

### Short Answer Questions

1.  Explain the difference between `!=` and `<>` in T-SQL. Are there any practical scenarios where one is preferred over the other?
2.  Describe how `AND`, `OR`, and `NOT` logical operators work together to build complex conditions in a `WHERE` clause. Provide a simple example.
3.  Illustrate how parentheses can be used to override default operator precedence with a clear example.

### Practical Task

  - Create a table named `Orders` with columns: `OrderID` (INT), `TotalAmount` (DECIMAL), `OrderDate` (DATE), `IsShipped` (BIT).
  - Insert at least 10 diverse records.
  - Write SQL queries using combinations of arithmetic, comparison, and logical operators to:
      - Find all orders placed after '2024-01-01' with a `TotalAmount` greater than $50.
      - Calculate a 10% discount for all non-shipped orders and display the new amount without updating the table.
      - Identify orders where the `TotalAmount` is between $20 and $70 (inclusive).
      - Find orders placed in the current year that are not yet shipped.
