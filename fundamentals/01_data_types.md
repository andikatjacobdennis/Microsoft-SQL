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


## Key Concepts & Detailed Content

### 1\. **Introduction to System Data Types**

  - **Definition**: System data types are predefined data types that come with SQL Server. They dictate the kind of values a column can hold, the amount of storage required, and how SQL Server handles comparisons and operations on the data.
  - **Importance**:
      - **Data Integrity**: Ensures that only valid data is stored in a column.
      - **Storage Efficiency**: Using the smallest appropriate data type conserves disk space.
      - **Performance**: Proper data type selection can improve query performance.
      - **Data Consistency**: Helps maintain uniformity across your database.
  - **Categories**: SQL Server data types are broadly categorized into Exact Numerics, Approximate Numerics, Date and Time, Character Strings, Unicode Character Strings, Binary Strings, and Other data types.

### 2\. **Numeric Data Types**

These types are used for storing numerical values. They are broadly categorized into **Exact Numerics** (for precise values) and **Approximate Numerics** (for floating-point numbers where slight imprecision is acceptable).


#### **Exact Numerics**

Used for storing precise numerical values, such as integers or fixed-point decimals, where accuracy is paramount.

  * **`BIT`**

      * **Description**: Stores a Boolean value, which can be `0` (false), `1` (true), or `NULL`.
      * **Storage**: 1 byte.
      * **Use Case**: Ideal for flags or indicators, like `IsActive` or `HasPermission`.
      * **Example**:
        ```sql
        CREATE TABLE Users (
            UserID INT PRIMARY KEY,
            UserName NVARCHAR(100),
            IsActive BIT -- Stores 1 if user is active, 0 otherwise
        );
        INSERT INTO Users (UserID, UserName, IsActive) VALUES (1, 'Alice', 1);
        INSERT INTO Users (UserID, UserName, IsActive) VALUES (2, 'Bob', 0);
        ```

  * **`TINYINT`**

      * **Description**: Stores whole numbers from `0` to `255`.
      * **Storage**: 1 byte.
      * **Use Case**: Suitable for very small positive integers, such as `StatusCode` (e.g., 1=Pending, 2=Complete) or `Age` (if restricted to this range).
      * **Example**:
        ```sql
        CREATE TABLE OrderStatus (
            StatusID TINYINT PRIMARY KEY, -- 1=Pending, 2=Processing, 3=Shipped, 4=Delivered
            StatusName NVARCHAR(50)
        );
        INSERT INTO OrderStatus (StatusID, StatusName) VALUES (1, 'Pending');
        INSERT INTO OrderStatus (StatusID, StatusName) VALUES (3, 'Shipped');
        ```

  * **`SMALLINT`**

      * **Description**: Stores whole numbers from `-32,768` to `32,767`.
      * **Storage**: 2 bytes.
      * **Use Case**: For smaller integer values where an `INT` would be excessive, such as `MonthNumber` or `Quantity` for items that won't exceed this range.
      * **Example**:
        ```sql
        CREATE TABLE Products (
            ProductID INT PRIMARY KEY,
            ProductName NVARCHAR(100),
            StockQuantity SMALLINT -- Max stock up to 32,767
        );
        INSERT INTO Products (ProductID, ProductName, StockQuantity) VALUES (101, 'Laptop', 150);
        INSERT INTO Products (ProductID, ProductName, StockQuantity) VALUES (102, 'Mouse', 5000);
        ```

  * **`INT`**

      * **Description**: Stores whole numbers (integers) from `-2,147,483,648` to `2,147,483,647`.
      * **Storage**: 4 bytes.
      * **Use Case**: A general-purpose integer type, commonly used for IDs like `EmployeeID` or counts like `OrderCount`.
      * **Example**:
        ```sql
        CREATE TABLE Customers (
            CustomerID INT PRIMARY KEY, -- Standard customer ID
            FirstName NVARCHAR(50),
            LastName NVARCHAR(50)
        );
        INSERT INTO Customers (CustomerID, FirstName, LastName) VALUES (1001, 'John', 'Doe');
        INSERT INTO Customers (CustomerID, FirstName, LastName) VALUES (2005, 'Jane', 'Smith');
        ```

  * **`BIGINT`**

      * **Description**: Stores large whole numbers from `-9,223,372,036,854,775,808` to `9,223,372,036,854,775,807`.
      * **Storage**: 8 bytes.
      * **Use Case**: Essential for high-volume counters, IDs in very large tables, or calculations that might exceed `INT` limits.
      * **Example**:
        ```sql
        CREATE TABLE AuditLog (
            LogID BIGINT PRIMARY KEY IDENTITY(1,1), -- Auto-incrementing ID for a very large log table
            EventDescription NVARCHAR(255),
            EventTimestamp DATETIME2
        );
        INSERT INTO AuditLog (EventDescription, EventTimestamp) VALUES ('User login', GETDATE());
        INSERT INTO AuditLog (EventDescription, EventTimestamp) VALUES ('Data updated', GETDATE());
        ```

  * **`DECIMAL` / `NUMERIC`**

      * **Description**: Stores exact numeric values with a fixed precision and scale. Defined as `DECIMAL(p,s)` or `NUMERIC(p,s)`, where 'p' is the total number of digits (precision, 1 to 38) and 's' is the number of digits after the decimal point (scale, 0 to p). `NUMERIC` is functionally identical to `DECIMAL` in T-SQL.
      * **Storage**: Varies based on precision: 1-9 digits (5 bytes), 10-19 digits (9 bytes), 20-28 digits (13 bytes), 29-38 digits (17 bytes).
      * **Use Case**: Crucial for monetary values, measurements requiring exact precision, and any calculations where rounding errors are unacceptable (e.g., `Price`, `TaxRate`).
      * **Example**:
        ```sql
        CREATE TABLE Orders (
            OrderID INT PRIMARY KEY,
            OrderDate DATE,
            TotalPrice DECIMAL(10, 2) -- Max 10 digits total, 2 after decimal (e.g., 99999999.99)
        );
        INSERT INTO Orders (OrderID, OrderDate, TotalPrice) VALUES (1, '2023-01-15', 1234.56);
        INSERT INTO Orders (OrderID, OrderDate, TotalPrice) VALUES (2, '2023-01-16', 89.95);

        CREATE TABLE ExchangeRates (
            CurrencyPair NVARCHAR(7),
            Rate NUMERIC(18, 10) -- High precision for currency rates
        );
        INSERT INTO ExchangeRates (CurrencyPair, Rate) VALUES ('USD/EUR', 0.9234567890);
        ```


#### **Approximate Numerics**

Used for storing floating-point numbers. These types do not store exact values but rather an approximation, which can lead to rounding errors. They are generally *not* suitable for financial calculations.

  * **`REAL`**

      * **Description**: Stores single-precision floating-point numbers.
      * **Storage**: 4 bytes.
      * **Use Case**: For less precise scientific or engineering calculations where minor rounding errors are acceptable (e.g., `WindSpeed`, `Temperature` readings).
      * **Example**:
        ```sql
        CREATE TABLE SensorData (
            SensorID INT,
            ReadingTime DATETIME,
            Temperature REAL -- A temperature reading where minor imprecision is okay
        );
        INSERT INTO SensorData (SensorID, ReadingTime, Temperature) VALUES (1, GETDATE(), 25.73);
        INSERT INTO SensorData (SensorID, ReadingTime, Temperature) VALUES (2, GETDATE(), 98.6);
        ```

  * **`FLOAT`**

      * **Description**: Stores double-precision floating-point numbers. `FLOAT(n)` allows specifying the number of bits for the mantissa (24 for `REAL`, 53 for `FLOAT`). `FLOAT` without 'n' defaults to 53 bits.
      * **Storage**: 8 bytes.
      * **Use Case**: For scientific calculations or values where absolute precision isn't critical, such as `Latitude` and `Longitude`. **Always use `DECIMAL` for financial or monetary data.**
      * **Example**:
        ```sql
        CREATE TABLE Locations (
            LocationID INT PRIMARY KEY,
            LocationName NVARCHAR(100),
            Latitude FLOAT,  -- Geographical coordinates
            Longitude FLOAT
        );
        INSERT INTO Locations (LocationID, LocationName, Latitude, Longitude) VALUES (1, 'Eiffel Tower', 48.8584, 2.2945);
        INSERT INTO Locations (LocationID, LocationName, Latitude, Longitude) VALUES (2, 'Statue of Liberty', 40.6892, -74.0445);
        ```


### 3\. **Other Key Data Type Categories**

Beyond numeric types, SQL Server offers a variety of data types for handling different kinds of information, including text, dates, and binary data.


#### 3.1. **Character Data Types**

Used for storing text and string information.

  * **`CHAR(n)`**

      * **Description**: Fixed-length non-Unicode string data. `n` defines the string length (1 to 8000). If the string is shorter than `n`, it's padded with spaces.
      * **Storage**: `n` bytes.
      * **Use Case**: Ideal for fixed-length codes like `StateCode` or `ZipCode` (if always 5 digits).
      * **Example**:
        ```sql
        CREATE TABLE Countries (
            CountryCode CHAR(2) PRIMARY KEY, -- ISO 2-letter country code
            CountryName NVARCHAR(100)
        );
        INSERT INTO Countries (CountryCode, CountryName) VALUES ('US', 'United States');
        INSERT INTO Countries (CountryCode, CountryName) VALUES ('IN', 'India');
        ```

  * **`VARCHAR(n)`**

      * **Description**: Variable-length non-Unicode string data. `n` defines the maximum length (1 to 8000). Stores only the actual characters plus 2 bytes overhead.
      * **Storage**: Actual length in bytes + 2 bytes.
      * **Use Case**: Common for names, addresses, or descriptions where length varies (e.g., `FirstName`, `ProductName`).
      * **Example**:
        ```sql
        CREATE TABLE Employees (
            EmployeeID INT PRIMARY KEY,
            FirstName VARCHAR(50),      -- First name, varies in length
            LastName VARCHAR(50),       -- Last name, varies in length
            Department VARCHAR(100)
        );
        INSERT INTO Employees (EmployeeID, FirstName, LastName, Department) VALUES (1, 'Michael', 'Scott', 'Sales');
        INSERT INTO Employees (EmployeeID, FirstName, LastName, Department) VALUES (2, 'Dwight', 'Schrute', 'Sales');
        ```

  * **`NCHAR(n)`**

      * **Description**: Fixed-length **Unicode** string data. `n` defines the string length (1 to 4000). Each character takes 2 bytes.
      * **Storage**: `2 * n` bytes.
      * **Use Case**: Essential for storing data in multiple languages, ensuring proper representation of international characters.
      * **Example**:
        ```sql
        CREATE TABLE LanguageSettings (
            SettingID INT PRIMARY KEY,
            LanguageCode NCHAR(5), -- 'en-US', 'fr-FR', 'ja-JP' - fixed length for locale codes
            DisplayName NVARCHAR(100)
        );
        INSERT INTO LanguageSettings (SettingID, LanguageCode, DisplayName) VALUES (1, 'en-US', 'English (United States)');
        INSERT INTO LanguageSettings (SettingID, LanguageCode, DisplayName) VALUES (2, 'ja-JP', N'日本語'); -- Japanese characters
        ```

  * **`NVARCHAR(n)`**

      * **Description**: Variable-length **Unicode** string data. `n` defines the maximum length (1 to 4000). Each character takes 2 bytes.
      * **Storage**: Actual length in bytes + 2 bytes overhead.
      * **Use Case**: The most common and recommended choice for text data that might include international characters, offering flexibility in length.
      * **Example**:
        ```sql
        CREATE TABLE Products_MultiLang (
            ProductID INT PRIMARY KEY,
            ProductName_EN NVARCHAR(255),
            ProductName_FR NVARCHAR(255), -- Product name in multiple languages
            Description_DE NVARCHAR(MAX) -- For potentially very long descriptions
        );
        INSERT INTO Products_MultiLang (ProductID, ProductName_EN, ProductName_FR, Description_DE)
        VALUES (1, 'Smart Watch', N'Montre Intelligente', N'Die intelligente Uhr bietet...');
        ```

  * **`TEXT`** (Deprecated)

      * **Description**: Variable-length non-Unicode data with a maximum length of 2GB.
      * **Storage**: Varies.
      * **Use Case**: While historically used for very large blocks of text, it's deprecated for new development. You should use `VARCHAR(MAX)` instead.
      * **Example**:
        ```sql
        -- Use VARCHAR(MAX) instead for new tables:
        -- CREATE TABLE OldArticles (ArticleID INT, ArticleContent TEXT);
        -- INSERT INTO OldArticles VALUES (1, 'This is a very long article content...');
        ```


#### 3.2. **Date/Time Data Types**

Used for storing dates, times, or combinations thereof.

  * **`DATE`**

      * **Description**: Stores a date only (year, month, day). Range from `0001-01-01` to `9999-12-31`.
      * **Storage**: 3 bytes.
      * **Use Case**: Perfect for `Birthdates` or transaction dates where the time component is irrelevant.
      * **Example**:
        ```sql
        CREATE TABLE Birthdays (
            PersonID INT PRIMARY KEY,
            PersonName NVARCHAR(100),
            DateOfBirth DATE -- Stores only the date
        );
        INSERT INTO Birthdays (PersonID, PersonName, DateOfBirth) VALUES (1, 'Maria', '1990-05-20');
        INSERT INTO Birthdays (PersonID, PersonName, DateOfBirth) VALUES (2, 'David', '1985-11-12');
        ```

  * **`DATETIME`**

      * **Description**: Stores both date and time, with accuracy to 3.33 milliseconds. Range from `1753-01-01` to `9999-12-31`.
      * **Storage**: 8 bytes.
      * **Use Case**: A general-purpose date and time storage type, widely used in legacy systems.
      * **Example**:
        ```sql
        CREATE TABLE Events (
            EventID INT PRIMARY KEY,
            EventName NVARCHAR(100),
            EventDateTime DATETIME -- Stores date and time with milliseconds
        );
        INSERT INTO Events (EventID, EventName, EventDateTime) VALUES (1, 'Conference Start', '2025-07-20 09:00:00.000');
        INSERT INTO Events (EventID, EventName, EventDateTime) VALUES (2, 'Workshop End', '2025-07-20 17:30:45.333');
        ```

  * **`DATETIME2`**

      * **Description**: Stores both date and time with higher precision (up to 100 nanoseconds) and a larger date range (`0001-01-01` to `9999-12-31`). It allows optional precision specification, e.g., `DATETIME2(7)`.
      * **Storage**: 6-8 bytes (based on precision).
      * **Use Case**: Preferred over `DATETIME` for new development due to its superior precision and wider date range, especially for logging or highly precise time tracking.
      * **Example**:
        ```sql
        CREATE TABLE LogEntries (
            LogEntryID INT PRIMARY KEY IDENTITY(1,1),
            LogMessage NVARCHAR(MAX),
            LogTimestamp DATETIME2(7) -- High precision timestamp for logging
        );
        INSERT INTO LogEntries (LogMessage, LogTimestamp) VALUES ('Application started.', SYSDATETIME());
        INSERT INTO LogEntries (LogMessage, LogTimestamp) VALUES ('Error occurred.', SYSDATETIME());
        ```

  * **`SMALLDATETIME`**

      * **Description**: Stores both date and time, accurate to 1 minute. Range from `1900-01-01` to `2079-06-06`.
      * **Storage**: 4 bytes.
      * **Use Case**: When less precision is acceptable and space efficiency is a concern.
      * **Example**:
        ```sql
        CREATE TABLE MeetingSchedules (
            MeetingID INT PRIMARY KEY,
            Subject NVARCHAR(255),
            ScheduledTime SMALLDATETIME -- Time accurate to the minute, within limited range
        );
        INSERT INTO MeetingSchedules (MeetingID, Subject, ScheduledTime) VALUES (1, 'Project Kickoff', '2025-07-25 10:30');
        INSERT INTO MeetingSchedules (MeetingID, Subject, ScheduledTime) VALUES (2, 'Team Sync', '2025-07-26 14:00');
        ```

  * **`TIME`**

      * **Description**: Stores a time only, with accuracy up to 100 nanoseconds.
      * **Storage**: 3-5 bytes (based on precision).
      * **Use Case**: For storing specific times like `BusinessHours` or `EventTimes` where a date isn't needed.
      * **Example**:
        ```sql
        CREATE TABLE BusinessHours (
            DayOfWeek INT PRIMARY KEY,
            OpeningTime TIME(0), -- Stores time without fractional seconds
            ClosingTime TIME(0)
        );
        INSERT INTO BusinessHours (DayOfWeek, OpeningTime, ClosingTime) VALUES (1, '09:00:00', '17:00:00'); -- Sunday
        INSERT INTO BusinessHours (DayOfWeek, OpeningTime, ClosingTime) VALUES (2, '08:30:00', '18:00:00'); -- Monday
        ```


#### 3.3. **Binary Data Types**

Used for storing raw binary data.

  * **`BINARY(n)`**

      * **Description**: Fixed-length binary data. `n` is the length in bytes (1 to 8000). If the data is shorter, it's padded with hexadecimal zeros.
      * **Storage**: `n` bytes.
      * **Use Case**: Ideal for storing exact binary sequences, such as cryptographic hash values (e.g., SHA-256 hashes).
      * **Example**:
        ```sql
        CREATE TABLE Hashes (
            FileID INT PRIMARY KEY,
            FileName NVARCHAR(255),
            FileHash BINARY(32) -- Stores a SHA-256 hash (32 bytes)
        );
        -- Note: You'd typically calculate the hash in your application before inserting
        INSERT INTO Hashes (FileID, FileName, FileHash) VALUES (1, 'document.pdf', 0x1A2B3C4D5E6F7A8B9C0D1E2F3A4B5C6D7E8F9A0B1C2D3E4F5A6B7C8D9E0F1A2B);
        ```

  * **`VARBINARY(n)`**

      * **Description**: Variable-length binary data. `n` is the maximum length in bytes (1 to 8000). Stores only the actual length plus 2 bytes overhead.
      * **Storage**: Actual length in bytes + 2 bytes.
      * **Use Case**: For storing small images, file contents, or encrypted data where the size can vary.
      * **Example**:
        ```sql
        CREATE TABLE Thumbnails (
            ImageID INT PRIMARY KEY,
            ImageName NVARCHAR(100),
            ThumbnailData VARBINARY(MAX) -- Small images or dynamically sized binary data
        );
        -- Example: Inserting a placeholder, actual data would be loaded from a file/stream
        INSERT INTO Thumbnails (ImageID, ImageName, ThumbnailData) VALUES (1, 'product_a_thumb.jpg', 0xFFD8FFE000104A46494600010100000100010000FFDB004300);
        ```

  * **`IMAGE`** (Deprecated)

      * **Description**: Variable-length binary data with a maximum length of 2GB.
      * **Storage**: Varies.
      * **Use Case**: Historically used for storing large binary objects like images or documents, but deprecated. Use `VARBINARY(MAX)` for new development.
      * **Example**:
        ```sql
        -- Use VARBINARY(MAX) instead for new tables:
        -- CREATE TABLE DocumentImages (DocumentID INT, DocumentImage IMAGE);
        -- INSERT INTO DocumentImages VALUES (1, 0x...very long binary data...);
        ```


#### 3.4. **Other Data Types**

Specialized data types for unique scenarios.

  * **`UNIQUEIDENTIFIER`**

      * **Description**: Stores a globally unique identifier (GUID), which is a 16-byte binary number.
      * **Storage**: 16 bytes.
      * **Use Case**: Ideal for unique IDs across distributed systems, particularly to avoid sequential key problems and for merge replication.
      * **Example**:
        ```sql
        CREATE TABLE Sessions (
            SessionID UNIQUEIDENTIFIER PRIMARY KEY DEFAULT NEWID(), -- Automatically generated GUID
            UserID INT,
            LoginTime DATETIME2
        );
        INSERT INTO Sessions (UserID, LoginTime) VALUES (1001, GETDATE());
        INSERT INTO Sessions (UserID, LoginTime) VALUES (1002, GETDATE());
        SELECT SessionID, UserID FROM Sessions; -- Will show GUIDs like: '8F1C3D9A-2E7F-4B5C-9A0F-1B2C3D4E5F6A'
        ```

  * **`SQL_VARIANT`**

      * **Description**: Stores values of various SQL Server supported data types, with some exceptions (e.g., `TEXT`, `NTEXT`, `IMAGE`, `VARBINARY(MAX)`, `VARCHAR(MAX)`, `NVARCHAR(MAX)`, `XML`, `TIMESTAMP`). It also stores metadata about the original data type.
      * **Storage**: Variable (max 8016 bytes), includes base data type information.
      * **Use Case**: While it allows a column to store data of varying types, it's often discouraged due to potential performance issues and reduced type safety. It might be used in highly flexible configuration tables.
      * **Example**:
        ```sql
        CREATE TABLE ConfigurationSettings (
            SettingName NVARCHAR(100) PRIMARY KEY,
            SettingValue SQL_VARIANT -- Can store different types of settings
        );
        INSERT INTO ConfigurationSettings (SettingName, SettingValue) VALUES ('MaxUsers', 500); -- Stored as INT
        INSERT INTO ConfigurationSettings (SettingName, SettingValue) VALUES ('EnableLogging', 1); -- Stored as BIT
        INSERT INTO ConfigurationSettings (SettingName, SettingValue) VALUES ('WelcomeMessage', 'Hello World!'); -- Stored as NVARCHAR
        INSERT INTO ConfigurationSettings (SettingName, SettingValue) VALUES ('Threshold', 123.45); -- Stored as DECIMAL
        ```

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create a new database and a table with columns using various data types. | SQL Server Mgmt Studio (SSMS) / Azure Data Studio | Table schema successfully created; understand syntax for data types.  |
| 2   | Insert sample data into the created table, observing data type constraints. | SQL Server Mgmt Studio (SSMS) / Azure Data Studio | Data inserted successfully; errors encountered for invalid data types.|


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
