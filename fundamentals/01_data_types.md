# Module SQLF01: **Fundamentals of T-SQL - Data Types**

> **Module Objective**
> This module aims to introduce learners to the various system data types available in T-SQL, enabling them to choose the most appropriate data type for their data storage needs. Understanding data types is fundamental for efficient database design and accurate data manipulation.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Identify and describe the purpose of common T-SQL system data types.
2.  Differentiate between various data type categories such as numeric, character, date/time, and binary.
3.  Select appropriate data types for different kinds of data to optimize storage and performance in SQL Server.

## Reference Materials

  - **Books:**
      - *SQL Server 2019 Revealed: A Guide to the Latest Features and Enhancements* by Bob Ward
      - *Microsoft SQL Server 2017 Administration Inside Out* by William Stanek
  - **Online Resources:**
      - SQL Server Data Types (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/data-types/data-types-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/data-types/data-types-transact-sql?view=sql-server-ver16))
      - Choosing the Right SQL Server Data Type (SQLShack) ([https://www.sqlshack.com/choosing-the-right-sql-server-data-type/](https://www.google.com/search?q=https://www.sqlshack.com/choosing-the-right-sql-server-data-type/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to System Data Types**

  - **Definition**: System data types are predefined data types that come with SQL Server. They dictate the kind of values a column can hold, the amount of storage required, and how SQL Server handles comparisons and operations on the data.
  - **Importance**:
      - **Data Integrity**: Ensures that only valid data is stored in a column.
      - **Storage Efficiency**: Using the smallest appropriate data type conserves disk space.
      - **Performance**: Proper data type selection can improve query performance.
      - **Data Consistency**: Helps maintain uniformity across your database.
  - **Categories**: SQL Server data types are broadly categorized into Exact Numerics, Approximate Numerics, Date and Time, Character Strings, Unicode Character Strings, Binary Strings, and Other data types.

-----

### 2\. **Numeric Data Types**

  - **Exact Numerics**: Used for storing precise numerical values.
      - **`bit`**:
          - **Description**: Stores a boolean value, either 0, 1, or NULL.
          - **Storage**: 1 byte.
          - **Use Case**: Flags (e.g., `IsActive`, `HasPermission`).
      - **`INT`**:
          - **Description**: Stores whole numbers (integers) from -2,147,483,648 to 2,147,483,647.
          - **Storage**: 4 bytes.
          - **Use Case**: General-purpose integer columns (e.g., `EmployeeID`, `Quantity`).
      - **`BIGINT`**:
          - **Description**: Stores large whole numbers from -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807.
          - **Storage**: 8 bytes.
          - **Use Case**: High-volume counters, IDs in very large tables.
      - **`SMALLINT`**:
          - **Description**: Stores whole numbers from -32,768 to 32,767.
          - **Storage**: 2 bytes.
          - **Use Case**: Smaller integer values where `INT` would be overkill (e.g., `Age`, `MonthNumber`).
      - **`TINYINT`**:
          - **Description**: Stores whole numbers from 0 to 255.
          - **Storage**: 1 byte.
          - **Use Case**: Very small positive integers (e.g., `StatusID` where status codes are limited).
      - **`DECIMAL` / `NUMERIC`**:
          - **Description**: Stores exact numeric values with a fixed precision and scale. `DECIMAL(p,s)` where 'p' is total digits (precision, 1 to 38) and 's' is digits after decimal point (scale, 0 to p).
          - **Storage**: Varies based on precision (5-17 bytes).
          - **Use Case**: Monetary values, measurements requiring exact precision (e.g., `Price`, `Weight`).
      - **`FLOAT` / `REAL`**: (Approximate Numerics)
          - **Description**: Stores floating-point numbers. `FLOAT` is 8 bytes, `REAL` is 4 bytes. These are approximate, meaning they may not store exact values and can have rounding errors.
          - **Storage**: `FLOAT` (8 bytes), `REAL` (4 bytes).
          - **Use Case**: Scientific calculations, values where absolute precision isn't critical (e.g., `Latitude`, `Longitude`). Use `DECIMAL` for financial data.

-----

### 3\. **Other Key Data Type Categories**

#### 3.1. **Character Data Types**

  - **`CHAR(n)`**:
      - **Description**: Fixed-length non-Unicode string data. `n` defines the string length (1 to 8000). If the string is shorter than `n`, it's padded with spaces.
      - **Storage**: `n` bytes.
      - **Use Case**: Fixed-length codes, hashes (e.g., `StateCode`, `ZipCode` if always 5 digits).
  - **`VARCHAR(n)`**:
      - **Description**: Variable-length non-Unicode string data. `n` defines the maximum length (1 to 8000). Stores only the actual characters plus 2 bytes overhead.
      - **Storage**: Actual length in bytes + 2 bytes.
      - **Use Case**: Names, addresses, descriptions where length varies (e.g., `FirstName`, `ProductName`).
  - **`NCHAR(n)`**:
      - **Description**: Fixed-length **Unicode** string data. `n` defines the string length (1 to 4000). Each character takes 2 bytes.
      - **Storage**: `2 * n` bytes.
      - **Use Case**: Storing data in multiple languages.
  - **`NVARCHAR(n)`**:
      - **Description**: Variable-length **Unicode** string data. `n` defines the maximum length (1 to 4000). Each character takes 2 bytes.
      - **Storage**: Actual length in bytes + 2 bytes overhead.
      - **Use Case**: Most common choice for text data that might include international characters.
  - **`TEXT`**: (Deprecated for new development, use `VARCHAR(MAX)` instead)
      - **Description**: Variable-length non-Unicode data with a maximum length of 2GB.
      - **Storage**: Varies.
      - **Use Case**: Storing very large blocks of text.

#### 3.2. **Date/Time Data Types**

  - **`DATE`**:
      - **Description**: Stores a date only (year, month, day). Range from 0001-01-01 to 9999-12-31.
      - **Storage**: 3 bytes.
      - **Use Case**: Birthdates, transaction dates where time is irrelevant.
  - **`DATETIME`**:
      - **Description**: Stores both date and time (accuracy to 3.33 milliseconds). Range from 1753-01-01 to 9999-12-31.
      - **Storage**: 8 bytes.
      - **Use Case**: General purpose date and time storage.
  - **`DATETIME2`**:
      - **Description**: Stores both date and time with higher precision (up to 100 nanoseconds) and a larger date range (0001-01-01 to 9999-12-31).
      - **Storage**: 6-8 bytes (based on precision).
      - **Use Case**: Preferred over `DATETIME` for new development due to better precision and range.
  - **`SMALLDATETIME`**:
      - **Description**: Stores both date and time (accuracy to 1 minute). Range from 1900-01-01 to 2079-06-06.
      - **Storage**: 4 bytes.
      - **Use Case**: When less precision is acceptable and space is a concern.
  - **`TIME`**:
      - **Description**: Stores a time only (accuracy to 100 nanoseconds).
      - **Storage**: 3-5 bytes (based on precision).
      - **Use Case**: Storing business hours, event times.

#### 3.3. **Binary Data Types**

  - **`BINARY(n)`**:
      - **Description**: Fixed-length binary data. `n` is the length in bytes (1 to 8000). Padded with hexadecimal zeros if shorter.
      - **Storage**: `n` bytes.
      - **Use Case**: Storing exact binary sequences, hash values.
  - **`VARBINARY(n)`**:
      - **Description**: Variable-length binary data. `n` is the maximum length in bytes (1 to 8000).
      - **Storage**: Actual length in bytes + 2 bytes overhead.
      - **Use Case**: Storing small images, file contents, encrypted data.
  - **`IMAGE`**: (Deprecated for new development, use `VARBINARY(MAX)` instead)
      - **Description**: Variable-length binary data with a maximum length of 2GB.
      - **Storage**: Varies.
      - **Use Case**: Storing large binary objects like images, documents.

#### 3.4. **Other Data Types**

  - **`UNIQUEIDENTIFIER`**:
      - **Description**: Stores a globally unique identifier (GUID), a 16-byte binary number.
      - **Storage**: 16 bytes.
      - **Use Case**: Unique IDs across distributed systems, avoiding sequential key problems.
  - **`SQL_VARIANT`**:
      - **Description**: Stores values of various SQL Server supported data types, except `TEXT`, `NTEXT`, `IMAGE`, `VARBINARY(MAX)`, `VARCHAR(MAX)`, `NVARCHAR(MAX)`, `XML`, and `TIMESTAMP`.
      - **Storage**: Variable (max 8016 bytes), includes base data type information.
      - **Use Case**: When a column must store data of varying types (though often discouraged due to performance and type safety issues).

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create a new database and a table with columns using various data types. | SQL Server Mgmt Studio (SSMS) / Azure Data Studio | Table schema successfully created; understand syntax for data types.  |
| 2   | Insert sample data into the created table, observing data type constraints. | SQL Server Mgmt Studio (SSMS) / Azure Data Studio | Data inserted successfully; errors encountered for invalid data types.|

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which data type would you typically use to store a person's age (assuming it won't exceed 120)?**

      - A) `BIGINT`
      - B) `INT`
      - C) `SMALLINT`
      - D) `TINYINT`
      - **Answer:** C) `SMALLINT`
      - **Explanation:** While `INT` would also work, `SMALLINT` is more memory-efficient as it perfectly fits the typical range of human ages without waste, storing values up to 32,767. `TINYINT` maxes out at 255 which would be sufficient for age. However if age were to become negative due to some error `TINYINT` cannot store negative values.

2.  **Which of the following data types is specifically designed for storing international character sets (Unicode)?**

      - A) `VARCHAR`
      - B) `CHAR`
      - C) `NVARCHAR`
      - D) `TEXT`
      - **Answer:** C) `NVARCHAR`
      - **Explanation:** `NVARCHAR` (and `NCHAR`) store Unicode data, which supports a wider range of characters from different languages. `VARCHAR` and `CHAR` are for non-Unicode data.

3.  **For storing precise monetary values in SQL Server, which data type is generally recommended?**

      - A) `FLOAT`
      - B) `REAL`
      - C) `DECIMAL`
      - D) `DATETIME`
      - **Answer:** C) `DECIMAL`
      - **Explanation:** `DECIMAL` (or `NUMERIC`) stores exact numeric values, which is crucial for financial calculations to avoid rounding errors that can occur with approximate numeric types like `FLOAT` or `REAL`.

### Short Answer Questions

1.  Explain the key difference between `CHAR` and `VARCHAR` data types in terms of storage and performance considerations.
2.  When would you choose `DATETIME2` over `DATETIME` for storing date and time information, and what are the benefits?
3.  Describe a scenario where using a `UNIQUEIDENTIFIER` would be more beneficial than an integer-based primary key.

### Practical Task

  - Design a simple `Customers` table. Include columns for `CustomerID` (appropriate numeric ID), `FirstName` (variable-length string, international support), `LastName` (variable-length string, international support), `DateOfBirth` (date only), `LastPurchaseAmount` (precise monetary value), and `IsActive` (boolean flag). Write the `CREATE TABLE` statement and insert 5 sample rows, ensuring data types are correctly chosen and data fits within constraints.
