# Module SQLPT01: **Performance Tuning - Indexing**

> **Module Objective**
> This module aims to provide a comprehensive understanding of indexing in SQL Server. Learners will explore various index types, their underlying structures, and how to effectively design and implement indexes to significantly improve query performance, reduce I/O, and optimize data retrieval operations.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Understand the fundamental concept of database indexes and their role in performance.
2.  Differentiate between Clustered and Non-clustered indexes, including their storage characteristics.
3.  Implement various index types: Unique, Filtered, Columnstore, and Covering indexes.
4.  Utilize `INCLUDE` columns to enhance non-clustered indexes.
5.  Analyze when and where to apply different index strategies for optimal query performance.

## Reference Materials

  - **Books:**
      - *SQL Server 2019 Query Performance Tuning Distilled* by Grant Fritchey
      - *SQL Server 2019 Internals* by Kalen Delaney, et al.
  - **Online Resources:**
      - Indexes (SQL Server) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/relational-databases/indexes/indexes?view=sql-server-ver16](https://www.google.com/search?q=https://learn.microsoft.com/en-us/sql/relational-databases/indexes/indexes%3Fview%3Dsql-server-ver16))
      - Clustered and Nonclustered Indexes Described (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver16))
      - SQL Server Indexing: A Complete Guide (SQLShack) ([https://www.sqlshack.com/sql-server-indexing-a-complete-guide/](https://www.google.com/search?q=https://www.sqlshack.com/sql-server-indexing-a-complete-guide/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to Indexes**

  - **Definition**: An index is a database object that provides a fast way to look up data in a table. It's similar to an index in a book, which helps you quickly find information without reading the entire book. In a database, an index speeds up data retrieval operations by providing a quick access path to rows based on the values in one or more columns.
  - **Purpose and Benefits**:
      - **Faster Data Retrieval**: Significantly speeds up `SELECT` queries, especially those with `WHERE`, `ORDER BY`, `GROUP BY`, and `JOIN` clauses.
      - **Reduced I/O**: Fewer data pages need to be read from disk because the index points directly to the relevant data.
      - **Improved Performance for DML (sometimes)**: Can speed up `UPDATE` and `DELETE` operations if the `WHERE` clause uses an indexed column.
  - **Drawbacks**:
      - **Increased Storage Space**: Indexes consume disk space.
      - **Slower DML Operations (sometimes)**: `INSERT`, `UPDATE`, and `DELETE` operations can be slower because the indexes must also be updated.
      - **Maintenance Overhead**: Indexes need to be maintained (rebuilt, reorganized) to remain efficient.
  - **Underlying Structure**: SQL Server indexes are typically implemented as B-trees. A B-tree is a self-balancing tree data structure that maintains sorted data and allows searches, sequential access, insertions, and deletions in logarithmic time.

-----

### 2\. **Core Index Types**

#### 2.1. **Clustered Index**

  - **Definition**: A clustered index determines the physical order in which the data rows are stored in the table. Because the data rows themselves are stored in sorted order based on the clustered index key, there can be only **one clustered index per table**.
  - **Characteristics**:
      - **Data Storage**: The leaf level of a clustered index *is* the data rows of the table.
      - **Physical Order**: The table data is physically sorted and stored according to the clustered index key.
      - **Primary Key**: By default, creating a `PRIMARY KEY` constraint on a table automatically creates a unique clustered index on that column(s) if one doesn't already exist.
      - **Row Locators**: For non-clustered indexes, the row locator is the clustered index key.
  - **Use Case**:
      - Columns frequently used in `ORDER BY` clauses.
      - Columns used in `RANGE` queries (e.g., `WHERE OrderDate BETWEEN 'X' AND 'Y'`).
      - `IDENTITY` columns (often good candidates for primary key and thus clustered index).
      - Columns used in `JOIN` conditions, especially for the "one" side of a one-to-many relationship.
  - **Example**:
    ```sql
    CREATE TABLE Orders (
        OrderID INT PRIMARY KEY CLUSTERED, -- Creates a clustered index by default
        OrderDate DATE,
        CustomerID INT,
        TotalAmount DECIMAL(10, 2)
    );

    -- Explicitly creating a clustered index if PK is non-clustered or not defined
    CREATE CLUSTERED INDEX IX_Orders_OrderDate
    ON Orders (OrderDate ASC);
    ```
  - **Diagram**:
    ```
    +-----------------+
    | Root Node       |
    +-----------------+
            |
            V
    +-----------------+
    | Intermediate    |
    | Level Nodes     |
    +-----------------+
            |
            V
    +-----------------+
    | Leaf Level      |  <-- Actual Data Pages (physically sorted)
    | (Data Rows)     |
    +-----------------+
    ```

#### 2.2. **Non-clustered Index**

  - **Definition**: A non-clustered index is a separate structure from the data rows. It contains the index key values and pointers (row locators) to the actual data rows in the table. A table can have multiple non-clustered indexes (up to 999 in SQL Server).
  - **Characteristics**:
      - **Separate Storage**: The leaf level of a non-clustered index contains the index key values and row locators (either a clustered index key or a Row ID (RID) for heaps).
      - **Logical Order**: The index itself is sorted, but the underlying data rows are not physically ordered by the non-clustered index.
      - **Pointers**: The index entries point to the data rows.
  - **Use Case**:
      - Columns frequently used in `WHERE` clauses for exact matches.
      - Columns used in `JOIN` conditions.
      - Columns used in `ORDER BY` or `GROUP BY` that are not part of the clustered index.
      - Foreign key columns.
  - **Example**:
    ```sql
    CREATE NONCLUSTERED INDEX IX_Orders_CustomerID
    ON Orders (CustomerID ASC);

    CREATE NONCLUSTERED INDEX IX_Orders_TotalAmount
    ON Orders (TotalAmount DESC);
    ```
  - **Diagram**:
    ```
    +-----------------+
    | Root Node       |
    +-----------------+
            |
            V
    +-----------------+
    | Intermediate    |
    | Level Nodes     |
    +-----------------+
            |
            V
    +-----------------+
    | Leaf Level      |  <-- Index Pages (Key + Row Locator)
    | (Index Entries) |
    +-----------------+
            |
            | (Pointers to Data)
            V
    +-----------------+
    | Data Pages      |  <-- Actual Data Rows (physical order determined by clustered index or heap)
    +-----------------+
    ```

-----

### 3\. **Advanced Index Types and Features**

#### 3.1. **Unique Index**

  - **Definition**: Ensures that all values in the indexed column(s) are unique. This enforces data integrity. A `PRIMARY KEY` constraint automatically creates a unique index (clustered by default, non-clustered if a clustered index already exists).
  - **Use Case**: Enforcing uniqueness on columns other than the primary key (e.g., `EmailAddress`, `NationalID`).
  - **Example**:
    ```sql
    CREATE UNIQUE NONCLUSTERED INDEX UQ_Employees_Email
    ON Employees (Email);
    ```

#### 3.2. **Filtered Index**

  - **Definition**: A non-clustered index that includes a `WHERE` clause to filter the rows that participate in the index. This reduces the size of the index and improves query performance for specific, frequently queried subsets of data.
  - **Benefits**: Smaller index size, reduced maintenance overhead, improved query performance for the filtered data.
  - **Use Case**:
      - Frequently querying a small, highly selective subset of data (e.g., `WHERE IsActive = 1`).
      - Indexing sparse columns (columns with many `NULL` values).
  - **Example**:
    ```sql
    CREATE NONCLUSTERED INDEX IX_Orders_ActiveHighValue
    ON Orders (TotalAmount DESC)
    WHERE OrderStatus = 'Active' AND TotalAmount > 1000;
    ```

#### 3.3. **Columnstore Index**

  - **Definition**: A technology for storing, managing, and querying data by organizing it in columns rather than rows. This is highly effective for data warehousing workloads (OLAP) and analytical queries that involve large aggregations and scans over many columns.
  - **Types**:
      - **Clustered Columnstore Index (CCI)**: The primary storage for the table. The entire table is stored in a columnstore format. Only one CCI per table.
      - **Non-clustered Columnstore Index (NCCI)**: A secondary index on a traditional rowstore table.
  - **Benefits**: Massive compression, significantly faster analytical queries (up to 10x or more), ideal for fact tables in data warehouses.
  - **Drawbacks**: Not suitable for OLTP (transactional) workloads with frequent `INSERT`/`UPDATE`/`DELETE` operations.
  - **Use Case**: Data warehousing, large analytical reports, business intelligence.
  - **Example**:
    ```sql
    -- Clustered Columnstore Index
    CREATE CLUSTERED COLUMNSTORE INDEX CCI_FactSales
    ON FactSales;

    -- Non-clustered Columnstore Index on a rowstore table
    CREATE NONCLUSTERED COLUMNSTORE INDEX NCCI_SalesAnalysis
    ON Sales (SalesDate, ProductKey, CustomerKey, Quantity, SalesAmount);
    ```

#### 3.4. **Covering Index (Index with Included Columns)**

  - **Definition**: A non-clustered index that includes all the columns needed to satisfy a query (both in the `SELECT` list and the `WHERE` clause) without having to access the underlying clustered index or heap. This avoids "bookmark lookups" and improves performance.
  - **`Included Columns` (`INCLUDE`)**: Allows adding non-key columns to the leaf level of a non-clustered index. These columns are stored only at the leaf level, not in the intermediate levels of the B-tree, reducing index size.
  - **Benefits**: Eliminates the need for SQL Server to go back to the base table or clustered index to retrieve additional columns, significantly reducing I/O.
  - **Use Case**: Queries that frequently select a few specific columns and filter on others.
  - **Example**: Retrieve `ProductName` and `Price` for products filtered by `Category`.
    ```sql
    -- Assume Products table: ProductID (PK), ProductName, Category, Price
    CREATE NONCLUSTERED INDEX IX_Products_Category_Covering
    ON Products (Category)
    INCLUDE (ProductName, Price); -- ProductName and Price are included at the leaf level
    ```
    Now, a query like `SELECT ProductName, Price FROM Products WHERE Category = 'Electronics';` can be fully satisfied by reading only the index, without touching the base table.

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create a table `Customers` without a clustered index (a heap). Insert 10,000 rows. Create a non-clustered index on `LastName`. Run a `SELECT` query on `LastName` and view the execution plan to see the index usage. | SSMS / Azure Data Studio | Index is used; understanding of heap vs. index scan.                  |
| 2   | Alter the `Customers` table to add a `PRIMARY KEY` on `CustomerID` (this will create a clustered index). Re-run the `SELECT` query and observe the execution plan change. | SSMS / Azure Data Studio | Execution plan shows clustered index seek/scan.                       |
| 3   | Create a `Unique Non-clustered Index` on the `Email` column of your `Customers` table. Attempt to insert a duplicate email and observe the error. | SSMS / Azure Data Studio | Uniqueness constraint enforced; error on duplicate insert.            |
| 4   | Create a `Filtered Index` on an `Orders` table for `Active` orders with `TotalAmount > 500`. Query this specific subset of data and check the execution plan. | SSMS / Azure Data Studio | Filtered index is used for relevant queries, showing efficiency.      |
| 5   | Create a `Non-clustered Index` on `OrderDate` for an `Orders` table, `INCLUDE`ing `TotalAmount` and `CustomerID`. Run a query that selects these columns and filters by `OrderDate`, and verify it's a covering index in the execution plan. | SSMS / Azure Data Studio | Query is satisfied by the index alone (covering index scan/seek).     |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **How many clustered indexes can a table have in SQL Server?**

      - A) Zero
      - B) One
      - C) Up to 5
      - D) Unlimited
      - **Answer:** B) One
      - **Explanation:** A clustered index defines the physical storage order of the data rows, so a table can only have one physical order.

2.  **Which type of index physically sorts the data rows in the table?**

      - A) Non-clustered index
      - B) Unique index
      - C) Clustered index
      - D) Filtered index
      - **Answer:** C) Clustered index
      - **Explanation:** The leaf level of a clustered index *is* the data, and it's stored in the order of the clustered index key.

3.  **What is the purpose of the `INCLUDE` clause in a `CREATE NONCLUSTERED INDEX` statement?**

      - A) To add more key columns to the index.
      - B) To specify columns that are stored at the leaf level of the index but are not part of the index key.
      - C) To encrypt the index data.
      - D) To define a filter for the index.
      - **Answer:** B) To specify columns that are stored at the leaf level of the index but are not part of the index key.
      - **Explanation:** `INCLUDE` columns are non-key columns stored only at the leaf level, allowing the index to "cover" a query without adding those columns to the index key, thus reducing index size and improving performance.

### Short Answer Questions

1.  Explain the main trade-off when deciding whether to add an index to a table.
2.  Describe a scenario where a `Filtered Index` would be significantly more beneficial than a regular non-clustered index.
3.  What is a "bookmark lookup" in the context of non-clustered indexes, and how does a "covering index" help avoid it?

### Practical Task

  - **Setup**: Create a table named `SalesTransactions` with the following columns:
      - `TransactionID` (INT, PK, Identity)
      - `ProductID` (INT)
      - `CustomerID` (INT)
      - `TransactionDate` (DATETIME)
      - `Amount` (DECIMAL(10,2))
      - `IsProcessed` (BIT, default 0)
      - `Notes` (NVARCHAR(255), NULL allowed)
  - **Insert a large amount of sample data**: Use a loop or a script to insert at least 100,000 rows. Ensure a good mix of `IsProcessed` values (e.g., 90% `0`, 10% `1`).
  - **Tasks**:
    1.  **Initial Query Performance**: Run a query to select `TransactionID`, `Amount`, `TransactionDate` for all transactions where `IsProcessed = 0` and `TransactionDate` is in the last 30 days. Note the execution time and I/O (using `SET STATISTICS IO ON; SET STATISTICS TIME ON;`).
    2.  **Create a Non-Clustered Index**: Create a non-clustered index on `TransactionDate` and `IsProcessed`. Re-run the query from Task 1 and compare performance.
    3.  **Create a Covering Index**: Create a new non-clustered index on `TransactionDate` including `Amount` and `TransactionID`, filtered for `IsProcessed = 0`. Run the query from Task 1 again and compare. Observe if it becomes a covering index in the execution plan.
    4.  **Create a Unique Index**: Add a `TransactionReference` (VARCHAR(50)) column to `SalesTransactions`. Create a unique non-clustered index on this column. Attempt to insert a duplicate `TransactionReference` and observe the error.
    5.  **Clean up**: Drop all created indexes, and then drop the `SalesTransactions` table.
