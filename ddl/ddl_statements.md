# Module SQLDDL01: **Data Definition Language (DDL) Statements**

> **Module Objective**
> This module aims to equip learners with the knowledge and skills to define, modify, and manage the structure of database objects using Data Definition Language (DDL) commands in T-SQL. Understanding DDL is fundamental for database design and administration.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Create new databases, tables, indexes, views, stored procedures, functions, and triggers.
2.  Modify the structure of existing database objects using `ALTER` statements.
3.  Understand the difference between `TRUNCATE TABLE` and `DELETE` statements.
4.  Remove database objects using `DROP` statements.

## Reference Materials

  - **Books:**
      - *SQL Server 2022 Querying for Dummies* by Michael Meys
      - *Microsoft SQL Server 2019 T-SQL Querying (Exam 70-761)* by Grant Fritchey and Kevin Boles
  - **Online Resources:**
      - CREATE DATABASE (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver16))
      - ALTER TABLE (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/statements/alter-table-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/statements/alter-table-transact-sql?view=sql-server-ver16))
      - DROP TABLE (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/statements/drop-table-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/statements/drop-table-transact-sql?view=sql-server-ver16))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to Data Definition Language (DDL)**

  - **Definition**: Data Definition Language (DDL) is a subset of SQL that deals with creating, modifying, and deleting database objects such as schemas, tables, indexes, views, sequences, and more. DDL commands are used to define the database structure or schema.
  - **Key Characteristics**:
      - **Auto-Commit**: DDL commands typically perform an implicit `COMMIT` operation, meaning changes are immediately and permanently saved.
      - **Metadata Changes**: DDL statements modify the database's metadata (information about the database structure) stored in system tables.
      - **Non-Transactional**: DDL operations generally cannot be rolled back, except in specific scenarios with explicit transactions and certain database features (e.g., DDL triggers).

-----

### 2\. **`CREATE` Statements**

  - **Definition**: The `CREATE` statement is used to define and establish new database objects.

  - **`CREATE DATABASE`**:

      - **Description**: Creates a new database.
      - **Syntax**:
        ```sql
        CREATE DATABASE database_name
        [ ON
            [ <filespec> [ ,...n ] ]
            [ , <filegroup> [ ,...n ] ]
        ]
        [ LOG ON <filespec> [ ,...n ] ]
        [ COLLATE collation_name ]
        [ WITH <option> [ ,...n ] ] ;
        ```
      - **Example**:
        ```sql
        CREATE DATABASE MyCompanyDB;
        GO -- Separates batches, useful for DDL commands
        USE MyCompanyDB;
        GO
        ```

  - **`CREATE TABLE`**:

      - **Description**: Creates a new table in the database.
      - **Syntax**:
        ```sql
        CREATE TABLE [ schema_name. ] table_name (
            { column_name data_type [ ( length | precision, scale ) ]
              [ COLLATE collation_name ]
              [ [ CONSTRAINT constraint_name ]
                { NULL | NOT NULL } ]
              [ [ CONSTRAINT constraint_name ]
                { PRIMARY KEY | UNIQUE }
                  [ CLUSTERED | NONCLUSTERED ]
                  [ WITH FILLFACTOR = fillfactor ]
                  [ ON { filegroup | "default" } ] ]
              [ [ CONSTRAINT constraint_name ]
                FOREIGN KEY REFERENCES ref_table [ ( ref_column ) ]
                  [ ON DELETE { NO ACTION | CASCADE | SET NULL | SET DEFAULT } ]
                  [ ON UPDATE { NO ACTION | CASCADE | SET NULL | SET DEFAULT } ] ]
              [ [ CONSTRAINT constraint_name ]
                DEFAULT constant_expression FOR column_name ]
              [ [ CONSTRAINT constraint_name ]
                CHECK ( search_condition ) ]
              [ IDENTITY [ ( seed , increment ) ] ]
              [ ROWGUIDCOL ]
            } [ ,...n ]
        ) [ ON { filegroup | "default" } ] ;
        ```
      - **Example**:
        ```sql
        CREATE TABLE Employees (
            EmployeeID INT PRIMARY KEY IDENTITY(1,1),
            FirstName NVARCHAR(50) NOT NULL,
            LastName NVARCHAR(50) NOT NULL,
            Email VARCHAR(100) UNIQUE,
            HireDate DATE DEFAULT GETDATE(),
            Salary DECIMAL(10, 2) CHECK (Salary > 0),
            DepartmentID INT NULL
        );
        ```

  - **`CREATE INDEX`**:

      - **Description**: Creates an index on a table or view. Indexes improve the speed of data retrieval operations.
      - **Syntax**:
        ```sql
        CREATE [ UNIQUE ] [ CLUSTERED | NONCLUSTERED ] INDEX index_name
        ON { table_or_view_name } ( column [ ASC | DESC ] [ ,...n ] )
        [ INCLUDE ( column_name [ ,...n ] ) ]
        [ WITH ( <option> [ ,...n ] ) ]
        [ ON { partition_scheme_name ( column_name ) | filegroup | "default" } ] ;
        ```
      - **Example**:
        ```sql
        CREATE NONCLUSTERED INDEX IX_Employees_LastName
        ON Employees (LastName ASC);
        ```

  - **`CREATE VIEW`**:

      - **Description**: Creates a virtual table whose contents are defined by a query.
      - **Syntax**:
        ```sql
        CREATE VIEW [ schema_name . ] view_name [ ( column [ ,...n ] ) ]
        [ WITH <view_attribute> [ ,...n ] ]
        AS select_statement
        [ WITH CHECK OPTION ] ;
        ```
      - **Example**:
        ```sql
        CREATE VIEW ActiveEmployees AS
        SELECT EmployeeID, FirstName, LastName, Email
        FROM Employees
        WHERE HireDate IS NOT NULL;
        ```

  - **`CREATE PROCEDURE`**:

      - **Description**: Creates a stored procedure, a precompiled collection of SQL statements.
      - **Syntax**: (Simplified)
        ```sql
        CREATE PROCEDURE [ schema_name. ] procedure_name
            [ { @parameter_name [ AS ] data_type [ VARYING ]
                [ = default ] [ OUT | OUTPUT | READONLY ]
              } [ ,...n ]
            ]
        AS
        BEGIN
            sql_statement [ ; ] [ ...n ]
        END ;
        ```
      - **Example**:
        ```sql
        CREATE PROCEDURE GetEmployeeByID
            @EmployeeID INT
        AS
        BEGIN
            SELECT EmployeeID, FirstName, LastName
            FROM Employees
            WHERE EmployeeID = @EmployeeID;
        END;
        ```

  - **`CREATE FUNCTION`**:

      - **Description**: Creates a user-defined function (UDF) that encapsulates reusable logic.
      - **Syntax**: (Simplified for scalar function)
        ```sql
        CREATE FUNCTION [ schema_name. ] function_name
        ( { @parameter_name [ AS ] data_type [ = default ] } [ ,...n ] )
        RETURNS data_type
        [ WITH <option> [ ,...n ] ]
        AS
        BEGIN
            RETURN scalar_expression
        END ;
        ```
      - **Example**:
        ```sql
        CREATE FUNCTION CalculateAge (@DOB DATE)
        RETURNS INT
        AS
        BEGIN
            RETURN DATEDIFF(YEAR, @DOB, GETDATE()) -
                   CASE WHEN MONTH(@DOB) > MONTH(GETDATE()) OR
                             (MONTH(@DOB) = MONTH(GETDATE()) AND DAY(@DOB) > DAY(GETDATE()))
                        THEN 1 ELSE 0 END;
        END;
        ```

  - **`CREATE TRIGGER`**:

      - **Description**: Creates a special type of stored procedure that automatically executes when a specific event occurs in the database (e.g., `INSERT`, `UPDATE`, `DELETE`).
      - **Syntax**: (Simplified)
        ```sql
        CREATE TRIGGER [ schema_name. ] trigger_name
        ON { table | view }
        [ WITH ENCRYPTION | EXECUTE AS ]
        { FOR | AFTER | INSTEAD OF } { [ INSERT ] [ , ] [ UPDATE ] [ , ] [ DELETE ] }
        AS
        BEGIN
            sql_statement [ ; ] [ ...n ]
        END ;
        ```
      - **Example**:
        ```sql
        CREATE TRIGGER trg_EmployeeInsertAudit
        ON Employees
        AFTER INSERT
        AS
        BEGIN
            INSERT INTO EmployeeAuditLog (EmployeeID, ChangeType, ChangeDate)
            SELECT EmployeeID, 'INSERT', GETDATE() FROM INSERTED;
        END;
        ```

-----

### 3\. **`ALTER` Statements**

  - **Definition**: The `ALTER` statement is used to modify the structure of an existing database object.

  - **`ALTER DATABASE`**:

      - **Description**: Modifies a database, e.g., changing its name, file properties, or options.
      - **Syntax**:
        ```sql
        ALTER DATABASE database_name
        { MODIFY NAME = new_database_name
        | COLLATE collation_name
        | SET <option> { ON | OFF }
        | ADD FILE <filespec>
        | REMOVE FILE file_name
        | ...
        } ;
        ```
      - **Example**:
        ```sql
        ALTER DATABASE MyCompanyDB SET RECOVERY SIMPLE;
        ALTER DATABASE MyCompanyDB MODIFY NAME = NewCompanyDB;
        ```

  - **`ALTER TABLE`**:

      - **Description**: Modifies the definition of an existing table.
      - **Syntax**:
        ```sql
        ALTER TABLE table_name
        { ADD { column_definition | table_constraint }
        | DROP { COLUMN column_name | CONSTRAINT constraint_name }
        | ALTER COLUMN column_name data_type [ ( length | precision, scale ) ] [ COLLATE collation_name ] [ NULL | NOT NULL ]
        | ADD CONSTRAINT constraint_name DEFAULT constant_expression FOR column_name
        | ...
        } ;
        ```
      - **Example**:
        ```sql
        ALTER TABLE Employees
        ADD PhoneNumber VARCHAR(20);

        ALTER TABLE Employees
        ALTER COLUMN Email NVARCHAR(150); -- Change data type and length

        ALTER TABLE Employees
        ADD CONSTRAINT FK_Employees_Departments
        FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID);

        ALTER TABLE Employees
        DROP COLUMN PhoneNumber;
        ```

  - **`ALTER INDEX`**:

      - **Description**: Rebuilds, reorganizes, or disables an existing index.
      - **Syntax**:
        ```sql
        ALTER INDEX { index_name | ALL }
        ON { object_name | database_name.schema_name.object_name }
        { REBUILD [ WITH ( <option> [ ,...n ] ) ]
        | REORGANIZE [ WITH ( LOB_COMPACTION = { ON | OFF } ) ]
        | DISABLE
        | ...
        } ;
        ```
      - **Example**:
        ```sql
        ALTER INDEX IX_Employees_LastName ON Employees REBUILD;
        ```

  - **`ALTER VIEW`**:

      - **Description**: Modifies an existing view.
      - **Syntax**: Same as `CREATE VIEW`, but replaces `CREATE` with `ALTER`.
      - **Example**:
        ```sql
        ALTER VIEW ActiveEmployees AS
        SELECT EmployeeID, FirstName, LastName, Email, HireDate
        FROM Employees
        WHERE HireDate IS NOT NULL AND EmployeeID > 100; -- Added another condition
        ```

  - **`ALTER PROCEDURE`**:

      - **Description**: Modifies an existing stored procedure.
      - **Syntax**: Same as `CREATE PROCEDURE`, but replaces `CREATE` with `ALTER`.
      - **Example**:
        ```sql
        ALTER PROCEDURE GetEmployeeByID
            @EmployeeID INT,
            @IncludeSalary BIT = 0 -- Added new parameter
        AS
        BEGIN
            SELECT EmployeeID, FirstName, LastName,
                   CASE WHEN @IncludeSalary = 1 THEN Salary ELSE NULL END AS Salary
            FROM Employees
            WHERE EmployeeID = @EmployeeID;
        END;
        ```

  - **`ALTER FUNCTION`**:

      - **Description**: Modifies an existing user-defined function.
      - **Syntax**: Same as `CREATE FUNCTION`, but replaces `CREATE` with `ALTER`.
      - **Example**:
        ```sql
        ALTER FUNCTION CalculateAge (@DOB DATE)
        RETURNS INT
        AS
        BEGIN
            RETURN DATEDIFF(YEAR, @DOB, GETDATE()); -- Simplified calculation
        END;
        ```

  - **`ALTER TRIGGER`**:

      - **Description**: Modifies an existing trigger.
      - **Syntax**: Same as `CREATE TRIGGER`, but replaces `CREATE` with `ALTER`.
      - **Example**:
        ```sql
        ALTER TRIGGER trg_EmployeeInsertAudit
        ON Employees
        AFTER INSERT
        AS
        BEGIN
            INSERT INTO EmployeeAuditLog (EmployeeID, ChangeType, ChangeDate, ChangedBy)
            SELECT EmployeeID, 'INSERT', GETDATE(), SUSER_SNAME() FROM INSERTED; -- Added ChangedBy
        END;
        ```

-----

### 4\. **`TRUNCATE` Statement**

  - **`TRUNCATE TABLE`**:
      - **Definition**: Removes all rows from a table without logging the individual row deletions. It deallocates the data pages used by the table, making it a faster operation than `DELETE` for large tables.
      - **Syntax**: `TRUNCATE TABLE [ database_name. [ schema_name ] . | schema_name . ] table_name ;`
      - **Key Differences from `DELETE`**:
          - **Logging**: `TRUNCATE TABLE` is minimally logged, making it faster. `DELETE` is fully logged.
          - **Transactional**: `TRUNCATE TABLE` cannot be rolled back easily (though it is technically transactional, it's often treated as non-rollbackable in practice due to minimal logging). `DELETE` is fully transactional and can be rolled back.
          - **Identity Column**: `TRUNCATE TABLE` resets the identity seed to its original seed value. `DELETE` does not reset the identity seed.
          - **Triggers**: `TRUNCATE TABLE` does not fire `DELETE` triggers. `DELETE` does.
          - **Constraints**: `TRUNCATE TABLE` cannot be used on tables referenced by a `FOREIGN KEY` constraint with `ON DELETE CASCADE` unless the foreign key constraint is also truncated. `DELETE` can be used.
      - **Example**:
        ```sql
        TRUNCATE TABLE EmployeeAuditLog; -- Empties the table and resets identity
        ```

-----

### 5\. **`DROP` Statements**

  - **Definition**: The `DROP` statement is used to remove an existing database object from the database. This action is generally irreversible.

  - **`DROP DATABASE`**:

      - **Description**: Deletes an entire database.
      - **Syntax**: `DROP DATABASE { database_name } [ ,...n ] ;`
      - **Example**:
        ```sql
        DROP DATABASE MyCompanyDB;
        ```

  - **`DROP TABLE`**:

      - **Description**: Deletes an entire table, including all its data, indexes, triggers, and constraints.
      - **Syntax**: `DROP TABLE { database_name. [ schema_name ] . | schema_name . } table_name [ ,...n ] ;`
      - **Example**:
        ```sql
        DROP TABLE Employees;
        ```

  - **`DROP INDEX`**:

      - **Description**: Deletes an index.
      - **Syntax**: `DROP INDEX [ IF EXISTS ] { index_name ON <object> | <object>.index_name } [ ,...n ] ;`
      - **Example**:
        ```sql
        DROP INDEX IX_Employees_LastName ON Employees;
        ```

  - **`DROP VIEW`**:

      - **Description**: Deletes a view.
      - **Syntax**: `DROP VIEW [ IF EXISTS ] { database_name. [ schema_name ] . | schema_name . } view_name [ ,...n ] ;`
      - **Example**:
        ```sql
        DROP VIEW ActiveEmployees;
        ```

  - **`DROP PROCEDURE`**:

      - **Description**: Deletes a stored procedure.
      - **Syntax**: `DROP PROCEDURE [ IF EXISTS ] { schema_name. } procedure_name [ ,...n ] ;`
      - **Example**:
        ```sql
        DROP PROCEDURE GetEmployeeByID;
        ```

  - **`DROP FUNCTION`**:

      - **Description**: Deletes a user-defined function.
      - **Syntax**: `DROP FUNCTION [ IF EXISTS ] { schema_name. } function_name [ ,...n ] ;`
      - **Example**:
        ```sql
        DROP FUNCTION CalculateAge;
        ```

  - **`DROP TRIGGER`**:

      - **Description**: Deletes a trigger.
      - **Syntax**: `DROP TRIGGER [ IF EXISTS ] { [ schema_name . ] trigger_name } [ ,...n ] ON { table | view } ;`
      - **Example**:
        ```sql
        DROP TRIGGER trg_EmployeeInsertAudit ON Employees;
        ```

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create a new database, then create a table with a primary key, unique constraint, and a check constraint. | SSMS / Azure Data Studio | Database and table created successfully with defined constraints.     |
| 2   | Alter the created table to add a new column, modify an existing column's data type, and add a foreign key constraint. | SSMS / Azure Data Studio | Table structure updated as expected; foreign key relationship established. |
| 3   | Create a simple view based on your table. Then, truncate the table and observe the effect on data. | SSMS / Azure Data Studio | View shows no data after truncation; understand `TRUNCATE` behavior. |
| 4   | Drop the view, then drop the table, and finally drop the database created in task 1. | SSMS / Azure Data Studio | All created objects are successfully removed from the SQL Server instance. |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which DDL statement is used to remove all rows from a table and reset the identity column, but does not fire `DELETE` triggers?**

      - A) `DELETE FROM TableName;`
      - B) `DROP TABLE TableName;`
      - C) `TRUNCATE TABLE TableName;`
      - D) `REMOVE ALL FROM TableName;`
      - **Answer:** C) `TRUNCATE TABLE TableName;`
      - **Explanation:** `TRUNCATE TABLE` is designed for fast deletion of all rows, resets identity, and is minimally logged, unlike `DELETE`. `DROP TABLE` removes the table structure entirely.

2.  **You need to add a new column named `PhoneNumber` with `VARCHAR(20)` data type to an existing table called `Customers`. Which DDL statement would you use?**

      - A) `CREATE COLUMN PhoneNumber VARCHAR(20) ON Customers;`
      - B) `ADD COLUMN PhoneNumber VARCHAR(20) TO Customers;`
      - C) `ALTER TABLE Customers ADD PhoneNumber VARCHAR(20);`
      - D) `UPDATE TABLE Customers SET PhoneNumber VARCHAR(20);`
      - **Answer:** C) `ALTER TABLE Customers ADD PhoneNumber VARCHAR(20);`
      - **Explanation:** The `ALTER TABLE` statement is used to modify the structure of an existing table, and `ADD COLUMN` is the clause for adding new columns.

3.  **Which of the following statements about DDL commands is generally true?**

      - A) They are fully transactional and can always be rolled back.
      - B) They primarily deal with manipulating data within tables.
      - C) They implicitly commit changes to the database.
      - D) They are used to retrieve data from the database.
      - **Answer:** C) They implicitly commit changes to the database.
      - **Explanation:** DDL commands are typically auto-committed, meaning changes are saved immediately and permanently. They deal with structure, not data manipulation (DML), and are not primarily for data retrieval (DQL).

### Short Answer Questions

1.  Explain the main differences between `TRUNCATE TABLE` and `DELETE FROM TableName` in terms of logging, transactional behavior, and identity column handling.
2.  Describe a scenario where you would use `ALTER VIEW` instead of `CREATE VIEW` with a `DROP` and `CREATE` sequence.
3.  When creating a table, what is the purpose of the `IDENTITY(seed, increment)` property, and why is it useful?

### Practical Task

  - **Scenario**: You are setting up a simple database for a small library.
  - **Tasks**:
    1.  **Create a new database** named `LibraryDB`.
    2.  **Create a table** named `Books` in `LibraryDB` with the following columns:
          - `BookID` (Primary Key, auto-incrementing)
          - `Title` (NVARCHAR, not null)
          - `Author` (NVARCHAR, not null)
          - `PublicationYear` (INT, allow nulls)
          - `ISBN` (VARCHAR(13), unique constraint)
          - `IsAvailable` (BIT, default to 1)
    3.  **Alter the `Books` table** to add a new column `Genre` (NVARCHAR(50)).
    4.  **Create a non-clustered index** on the `Books` table for the `Author` column to speed up searches by author.
    5.  **Create a view** named `AvailableBooks` that shows `BookID`, `Title`, `Author`, and `PublicationYear` for books where `IsAvailable` is 1.
    6.  **Create a simple stored procedure** named `AddBook` that takes `Title`, `Author`, `PublicationYear`, and `ISBN` as parameters and inserts a new book into the `Books` table.
    7.  **Insert 3-5 sample books** using the `AddBook` procedure.
    8.  **Run a `TRUNCATE TABLE Books;`** statement. Observe the effect. (You might need to temporarily disable foreign key constraints if you add them later and they reference this table).
    9.  **Drop the `AvailableBooks` view**, then **drop the `Books` table**, and finally **drop the `LibraryDB` database**.
