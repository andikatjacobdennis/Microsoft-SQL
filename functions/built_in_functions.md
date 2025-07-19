# Module SQLFNC01: **Functions - Built-in Functions**

> **Module Objective**
> This module aims to provide a comprehensive understanding of T-SQL's rich set of built-in functions. Learners will discover how to use these functions to perform various operations on data, including string manipulation, date calculations, mathematical computations, data aggregation, and retrieving system information, thereby enhancing their data querying and manipulation capabilities.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Apply common string functions to manipulate and format character data.
2.  Perform date and time calculations and conversions using date functions.
3.  Utilize mathematical functions for numerical computations.
4.  Employ aggregate functions to summarize data sets.
5.  Leverage system functions to obtain database and session-related information.

## Reference Materials

  - **Books:**
      - *T-SQL Querying (Developer Reference)* by Itzik Ben-Gan
      - *SQL Server 2022 Querying for Dummies* by Michael Meys
  - **Online Resources:**
      - Built-in Functions (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/functions/functions?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/functions/functions?view=sql-server-ver16))
      - SQL Server String Functions (SQLShack) ([https://www.sqlshack.com/sql-server-string-functions/](https://www.google.com/search?q=https://www.sqlshack.com/sql-server-string-functions/))
      - SQL Server Date and Time Functions (SQLShack) ([https://www.sqlshack.com/sql-server-date-and-time-functions/](https://www.google.com/search?q=https://www.sqlshack.com/sql-server-date-and-time-functions/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to Built-in Functions**

  - **Definition**: Built-in functions are predefined routines provided by SQL Server that perform specific operations and return a value. They can be used in `SELECT` lists, `WHERE` clauses, `HAVING` clauses, `ORDER BY` clauses, and other parts of T-SQL statements.
  - **Importance**:
      - **Data Transformation**: Modify data into a desired format (e.g., converting data types, manipulating strings).
      - **Calculations**: Perform mathematical, date, or statistical computations.
      - **Data Analysis**: Summarize and aggregate data for reporting.
      - **System Information**: Retrieve metadata or session-specific information.
  - **Types**: SQL Server categorizes built-in functions into several groups based on their functionality, such as String, Date and Time, Mathematical, Aggregate, and System functions.

-----

### 2\. **Common Categories of Built-in Functions**

#### 2.1. **String Functions**

  - **Purpose**: Used for manipulating and querying string (character) data.
  - **Functions**:
      - **`LEN()`**: Returns the number of characters of the specified string expression, excluding trailing blanks.
          - `SELECT LEN('Hello World   ');` -- Result: 11
      - **`LEFT(string, length)`**: Returns the left part of a character string with the specified number of characters.
          - `SELECT LEFT('SQL Server', 3);` -- Result: 'SQL'
      - **`RIGHT(string, length)`**: Returns the right part of a character string with the specified number of characters.
          - `SELECT RIGHT('SQL Server', 6);` -- Result: 'Server'
      - **`CHARINDEX(substring, string [, start_location])`**: Returns the starting position of the specified `substring` within `string`. Returns 0 if not found.
          - `SELECT CHARINDEX('Server', 'SQL Server Database');` -- Result: 5
      - **`REPLACE(string, old_string, new_string)`**: Replaces all occurrences of `old_string` with `new_string` within `string`.
          - `SELECT REPLACE('Hello World', 'World', 'SQL');` -- Result: 'Hello SQL'
      - **`LOWER(string)`**: Converts all characters in the specified string to lowercase.
          - `SELECT LOWER('SQL SERVER');` -- Result: 'sql server'
      - **`UPPER(string)`**: Converts all characters in the specified string to uppercase.
          - `SELECT UPPER('sql server');` -- Result: 'SQL SERVER'
      - **`LTRIM(string)`**: Removes leading blanks (spaces) from a string.
          - `SELECT LTRIM('   Hello');` -- Result: 'Hello'
      - **`RTRIM(string)`**: Removes trailing blanks (spaces) from a string.
          - `SELECT RTRIM('Hello   ');` -- Result: 'Hello'

#### 2.2. **Date Functions**

  - **Purpose**: Used for manipulating and querying date and time values.
  - **Functions**:
      - **`GETDATE()`**: Returns the current database system date and time.
          - `SELECT GETDATE();` -- Result: Current date and time (e.g., 2025-07-20 10:30:45.123)
      - **`DATEADD(interval, number, date)`**: Adds a specified `number` of `interval` units to a `date`.
          - `SELECT DATEADD(day, 7, GETDATE());` -- Result: Date 7 days from now
          - `SELECT DATEADD(month, -1, '2025-07-20');` -- Result: '2025-06-20'
      - **`DATEDIFF(interval, start_date, end_date)`**: Returns the count of the specified `interval` boundaries crossed between `start_date` and `end_date`.
          - `SELECT DATEDIFF(year, '1990-01-01', GETDATE());` -- Result: Age in years
          - `SELECT DATEDIFF(hour, '2025-07-20 10:00:00', '2025-07-20 12:30:00');` -- Result: 2
      - **`CONVERT(data_type, expression [, style])`**: Converts an expression of one data type to another. Often used for date formatting.
          - `SELECT CONVERT(VARCHAR(10), GETDATE(), 101);` -- Result: '07/20/2025' (mm/dd/yyyy)
      - **`FORMAT(value, format [, culture])`**: Formats a value with the specified format. More flexible for dates and numbers than `CONVERT`.
          - `SELECT FORMAT(GETDATE(), 'yyyy-MM-dd HH:mm:ss');` -- Result: '2025-07-20 10:30:45'
          - `SELECT FORMAT(1234567.89, 'C', 'en-US');` -- Result: '$1,234,567.89'

#### 2.3. **Mathematical Functions**

  - **Purpose**: Perform various mathematical operations.
  - **Functions**:
      - **`ABS(number)`**: Returns the absolute (positive) value of a number.
          - `SELECT ABS(-10.5);` -- Result: 10.5
      - **`CEILING(number)`**: Returns the smallest integer greater than or equal to the given number.
          - `SELECT CEILING(10.1);` -- Result: 11
          - `SELECT CEILING(-10.1);` -- Result: -10
      - **`FLOOR(number)`**: Returns the largest integer less than or equal to the given number.
          - `SELECT FLOOR(10.9);` -- Result: 10
          - `SELECT FLOOR(-10.9);` -- Result: -11
      - **`ROUND(number, decimal_places [, function_to_perform])`**: Rounds a numeric expression to the specified length or precision.
          - `SELECT ROUND(123.456, 2);` -- Result: 123.460
          - `SELECT ROUND(123.456, 1);` -- Result: 123.500
      - **`POWER(base, exponent)`**: Returns the value of `base` raised to the power of `exponent`.
          - `SELECT POWER(2, 3);` -- Result: 8.0
      - **`SQRT(number)`**: Returns the square root of a specified float expression.
          - `SELECT SQRT(25);` -- Result: 5.0

#### 2.4. **Aggregate Functions**

  - **Purpose**: Perform calculations on a set of rows and return a single summary value. Often used with `GROUP BY`.
  - **Functions**:
      - **`COUNT(expression)`**: Returns the number of items in a group. `COUNT(*)` counts all rows, `COUNT(column_name)` counts non-NULL values.
          - `SELECT COUNT(*) FROM Employees;` -- Total number of employees
          - `SELECT COUNT(Email) FROM Employees;` -- Number of employees with an email
      - **`SUM(expression)`**: Returns the sum of all values in a numeric column.
          - `SELECT SUM(OrderTotal) FROM Orders;` -- Total sales
      - **`AVG(expression)`**: Returns the average of all values in a numeric column.
          - `SELECT AVG(Salary) FROM Employees;` -- Average employee salary
      - **`MIN(expression)`**: Returns the minimum value in a column.
          - `SELECT MIN(Price) FROM Products;` -- Lowest product price
      - **`MAX(expression)`**: Returns the maximum value in a column.
          - `SELECT MAX(OrderDate) FROM Orders;` -- Latest order date

-----

### 3\. **System Functions and Conditional Logic with Functions**

#### 3.1. **System Functions**

  - **Purpose**: Retrieve various system, session, or metadata information.
  - **Functions**:
      - **`@@IDENTITY`**: Returns the last-inserted identity value produced by the last `INSERT` statement in the current session, regardless of the table.
          - `INSERT INTO Products (ProductName) VALUES ('Test Product'); SELECT @@IDENTITY;`
      - **`SCOPE_IDENTITY()`**: Returns the last-inserted identity value produced by an `INSERT` statement into a table in the same scope (stored procedure, trigger, function, or batch). This is generally preferred over `@@IDENTITY` for reliability.
          - `INSERT INTO Products (ProductName) VALUES ('Another Product'); SELECT SCOPE_IDENTITY();`
      - **`ISNULL(check_expression, replacement_value)`**: Replaces NULL with the specified `replacement_value`.
          - `SELECT ISNULL(NULL, 'N/A');` -- Result: 'N/A'
          - `SELECT ISNULL(Email, 'No Email Provided') FROM Customers;`
      - **`COALESCE(expression1, expression2, ..., expressionN)`**: Returns the first non-NULL expression among its arguments. More versatile than `ISNULL` as it can take multiple arguments.
          - `SELECT COALESCE(NULL, NULL, 'Value Found', 'Another Value');` -- Result: 'Value Found'
          - `SELECT COALESCE(HomePhone, WorkPhone, MobilePhone, 'No Phone') FROM Contacts;`
      - **`CAST(expression AS data_type)`**: Converts an expression from one data type to another. Less flexible than `CONVERT` for certain types but standard SQL.
          - `SELECT CAST('123' AS INT);` -- Result: 123
          - `SELECT CAST(GETDATE() AS DATE);` -- Result: Current Date only
      - **`CONVERT(data_type, expression [, style])`**: Converts an expression of one data type to another. Offers more style options, especially for dates.
          - `SELECT CONVERT(VARCHAR(20), GETDATE(), 120);` -- Result: '2025-07-20 10:30:45' (yyyy-mm-dd hh:mi:ss)

#### 3.2. **Case Study: Combining Functions for Data Analysis**

  - **Example**: Calculate the average order value per customer, formatted to two decimal places, and display the customer's full name in uppercase.
    ```sql
    -- Assume Customers (CustomerID, FirstName, LastName) and Orders (OrderID, CustomerID, OrderTotal)
    SELECT
        C.CustomerID,
        UPPER(C.FirstName + ' ' + C.LastName) AS FullName,
        FORMAT(AVG(O.OrderTotal), 'N2') AS AverageOrderValue
    FROM Customers AS C
    JOIN Orders AS O ON C.CustomerID = O.CustomerID
    GROUP BY C.CustomerID, C.FirstName, C.LastName
    HAVING COUNT(O.OrderID) > 1 -- Only show customers with more than one order
    ORDER BY AverageOrderValue DESC;
    ```
    This example combines `UPPER`, string concatenation (`+`), `AVG`, `FORMAT`, `GROUP BY`, and `HAVING` to produce a meaningful business report.

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Use string functions (`LEN`, `LEFT`, `RIGHT`, `REPLACE`, `UPPER`, `LOWER`, `LTRIM`, `RTRIM`) to clean and format text data in a sample table. | SSMS / Azure Data Studio | Data is transformed as expected; understanding of string manipulation. |
| 2   | Calculate age from a birth date, find the difference between two dates, and format dates in various styles using date functions. | SSMS / Azure Data Studio | Correct date calculations and formatted date outputs.                 |
| 3   | Apply aggregate functions (`COUNT`, `SUM`, `AVG`, `MIN`, `MAX`) with `GROUP BY` to summarize sales data by category. | SSMS / Azure Data Studio | Accurate summary statistics for grouped data.                         |
| 4   | Experiment with `ISNULL` and `COALESCE` to handle NULL values in a query, and use `CAST`/`CONVERT` for data type conversions. | SSMS / Azure Data Studio | NULL values are replaced; data types are correctly converted.         |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which function would you use to return the number of characters in a string, excluding any trailing spaces?**

      - A) `LENGTH()`
      - B) `SIZE()`
      - C) `LEN()`
      - D) `COUNT()`
      - **Answer:** C) `LEN()`
      - **Explanation:** `LEN()` is the T-SQL function specifically designed to return the length of a string, excluding trailing blanks.

2.  **To calculate the total sum of `OrderAmount` for all orders, which aggregate function should be used?**

      - A) `AVG()`
      - B) `MAX()`
      - C) `SUM()`
      - D) `COUNT()`
      - **Answer:** C) `SUM()`
      - **Explanation:** `SUM()` calculates the total sum of a numeric column's values.

3.  **You need to display the current date and time in 'YYYY-MM-DD HH:MM:SS' format. Which function is most suitable for this formatting task?**

      - A) `GETDATE()`
      - B) `DATEDIFF()`
      - C) `CONVERT()` with style 120
      - D) `DATEADD()`
      - **Answer:** C) `CONVERT()` with style 120
      - **Explanation:** `CONVERT()` allows specifying a style code to format date/time values. Style 120 produces 'YYYY-MM-DD HH:MM:SS'. `FORMAT()` is also a good option for flexible formatting.

### Short Answer Questions

1.  Explain the difference between `@@IDENTITY` and `SCOPE_IDENTITY()` and when you would prefer to use one over the other.
2.  Describe a scenario where `COALESCE()` would be more advantageous than `ISNULL()`.
3.  How do aggregate functions differ from scalar functions in terms of their input and output? Provide an example of each.

### Practical Task

  - **Setup**: Create a table named `SalesData` with the following columns and insert at least 10 diverse records:

      - `SaleID` (INT, PK, Identity)
      - `ProductName` (NVARCHAR(100))
      - `SaleDate` (DATETIME)
      - `Quantity` (INT)
      - `UnitPrice` (DECIMAL(10, 2))
      - `DiscountPercentage` (DECIMAL(5, 2), NULL allowed, default 0)
      - `CustomerEmail` (VARCHAR(100), NULL allowed)

  - **Tasks**:

    1.  **Calculate the `TotalSaleAmount`** for each sale (`Quantity * UnitPrice * (1 - DiscountPercentage)`). Handle `NULL` `DiscountPercentage` by treating it as 0.
    2.  **Display the `ProductName` in uppercase** and the first 5 characters of the `CustomerEmail` (if not NULL). If `CustomerEmail` is NULL, display 'No Email'.
    3.  **Find the average `TotalSaleAmount`** for each `ProductName`, rounding the average to 2 decimal places.
    4.  **Determine the number of days** since each sale occurred until today's date.
    5.  **Identify the earliest and latest `SaleDate`** in the `SalesData` table.
    6.  **Count the total number of sales** and the number of sales where `CustomerEmail` is not NULL.
    7.  **Format the `SaleDate`** to display as 'Month Day, Year' (e.g., 'July 20, 2025').
