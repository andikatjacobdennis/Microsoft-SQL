# Module SQLTRN01: **Transactions**

> **Module Objective**
> This module aims to provide a fundamental understanding of database transactions in T-SQL. Learners will grasp the concept of atomicity, consistency, isolation, and durability (ACID properties), and learn how to use `BEGIN TRANSACTION`, `COMMIT TRANSACTION`, `ROLLBACK TRANSACTION`, and `SAVE TRANSACTION` to ensure data integrity and reliability in multi-step operations.

## Learning Outcomes

By the end of this module, learners will be able to:

1.  Understand the definition and importance of database transactions.
2.  Explain the ACID properties of transactions.
3.  Initiate, commit, and roll back transactions using T-SQL commands.
4.  Utilize `SAVE TRANSACTION` for partial rollbacks within a transaction.
5.  Apply transactions to ensure data integrity in multi-step DML operations.

## Reference Materials

  - **Books:**
      - *SQL Server 2022 Querying for Dummies* by Michael Meys
      - *T-SQL Fundamentals* by Itzik Ben-Gan
  - **Online Resources:**
      - Transactions (Transact-SQL) (Microsoft Learn) ([https://learn.microsoft.com/en-us/sql/t-sql/language-elements/transactions-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/transactions-transact-sql?view=sql-server-ver16))
      - ACID Properties in DBMS (GeeksforGeeks) ([https://www.geeksforgeeks.org/acid-properties-in-dbms/](https://www.geeksforgeeks.org/acid-properties-in-dbms/))
      - SQL Server Transactions (SQLShack) ([https://www.sqlshack.com/sql-server-transactions/](https://www.google.com/search?q=https://www.sqlshack.com/sql-server-transactions/))

-----

## Key Concepts & Detailed Content

### 1\. **Introduction to Transactions**

  - **Definition**: A transaction is a single logical unit of work performed on a database. It consists of one or more SQL statements that are treated as a single, indivisible operation. Either all statements within the transaction succeed and are permanently saved (committed), or if any statement fails, all changes made by the transaction are undone (rolled back).
  - **Purpose**: To ensure data integrity and consistency, especially in scenarios involving multiple interdependent data modifications.
  - **Implicit vs. Explicit Transactions**:
      - **Implicit Transactions**: SQL Server can operate in a mode where each individual SQL statement is automatically treated as a transaction and committed immediately. This is the default behavior in some client tools.
      - **Explicit Transactions**: Defined by the user using `BEGIN TRANSACTION` and explicitly committed or rolled back. This is crucial for multi-step operations.

-----

### 2\. **Transaction Control Commands**

#### 2.1. **`BEGIN TRANSACTION`**

  - **Description**: Marks the starting point of an explicit local transaction. All DML statements executed after `BEGIN TRANSACTION` are part of this transaction until `COMMIT TRANSACTION` or `ROLLBACK TRANSACTION` is issued.
  - **Syntax**: `BEGIN { TRAN | TRANSACTION } [ transaction_name | @transaction_variable ] ;`
  - **Example**:
    ```sql
    BEGIN TRANSACTION;
    -- DML statements here
    ```

#### 2.2. **`COMMIT TRANSACTION`**

  - **Description**: Saves all changes made during the current transaction permanently to the database. Once committed, the changes cannot be undone by a `ROLLBACK`.
  - **Syntax**: `COMMIT { TRAN | TRANSACTION } [ transaction_name | @transaction_variable ] ;`
  - **Example**:
    ```sql
    BEGIN TRANSACTION;
    INSERT INTO Accounts (AccountID, Balance) VALUES (1, 1000);
    UPDATE Products SET Stock = Stock - 1 WHERE ProductID = 5;
    COMMIT TRANSACTION; -- All changes are saved
    ```

#### 2.3. **`ROLLBACK TRANSACTION`**

  - **Description**: Undoes all changes made during the current transaction. It restores the database to the state it was in before the `BEGIN TRANSACTION` statement was executed.
  - **Syntax**: `ROLLBACK { TRAN | TRANSACTION } [ transaction_name | @transaction_variable | savepoint_name | @savepoint_variable ] ;`
  - **Example**:
    ```sql
    BEGIN TRANSACTION;
    INSERT INTO Accounts (AccountID, Balance) VALUES (2, 500);
    UPDATE Products SET Stock = Stock - 1000 WHERE ProductID = 5; -- This might fail due to check constraint

    IF @@ERROR <> 0 -- Check for error (or use TRY...CATCH)
    BEGIN
        ROLLBACK TRANSACTION; -- Undo the INSERT as well
        PRINT 'Transaction failed and rolled back.';
    END
    ELSE
    BEGIN
        COMMIT TRANSACTION;
        PRINT 'Transaction committed.';
    END
    ```

#### 2.4. **`SAVE TRANSACTION`**

  - **Description**: Creates a savepoint within a transaction. This allows for partial rollbacks, where you can roll back to a specific savepoint without rolling back the entire transaction.
  - **Syntax**: `SAVE TRAN { savepoint_name | @savepoint_variable } ;`
  - **Example**:
    ```sql
    BEGIN TRANSACTION;
    INSERT INTO Customers (CustomerID, FirstName, LastName) VALUES (1, 'Alice', 'Smith');

    SAVE TRANSACTION FirstInsert; -- Create a savepoint

    INSERT INTO Orders (OrderID, CustomerID, OrderDate) VALUES (101, 1, GETDATE());

    -- Simulate an error or condition where only the last part should be undone
    IF 1 = 1 -- Simulating a condition for rollback
    BEGIN
        ROLLBACK TRANSACTION FirstInsert; -- Only rolls back the Orders insert
        PRINT 'Rolled back to FirstInsert savepoint.';
    END

    INSERT INTO Payments (PaymentID, OrderID, Amount) VALUES (1001, 101, 50.00); -- This will now fail if Order 101 was rolled back

    COMMIT TRANSACTION; -- Commits the Customers insert (and Payments if it succeeded)
    ```
  - **Diagram**:
    ```
    BEGIN TRAN -------------------------------------------------> COMMIT TRAN
        |                                                              ^
        | INSERT A                                                     |
        |                                                              |
        | SAVE TRAN SavePoint1                                         |
        |                                                              |
        | UPDATE B                                                     |
        |                                                              |
        | ROLLBACK TRAN SavePoint1 (Only undoes UPDATE B)              |
        |                                                              |
        | INSERT C                                                     |
        |                                                              |
        +--------------------------------------------------------------+
    ```

-----

### 3\. **ACID Properties of Transactions**

  - **Definition**: ACID is an acronym that defines a set of properties that guarantee that database transactions are processed reliably. These properties are crucial for ensuring data validity during transactions.

#### 3.1. **Atomicity**

  - **Explanation**: Guarantees that all operations within a transaction are treated as a single, indivisible unit. Either all of them succeed, or none of them do. There is no "partial" completion. If any part of the transaction fails, the entire transaction is aborted, and the database is rolled back to its state before the transaction began.
  - **Analogy**: A money transfer from account A to account B. It must either fully debit A and credit B, or do neither. It cannot debit A without crediting B.

#### 3.2. **Consistency**

  - **Explanation**: Ensures that a transaction brings the database from one valid state to another. All data integrity rules (constraints, triggers, cascades) must be maintained. If a transaction violates any of these rules, it is rolled back.
  - **Example**: If a table has a `NOT NULL` constraint on a column, a transaction attempting to insert a `NULL` value into that column will be rolled back, maintaining the consistency rule.

#### 3.3. **Isolation**

  - **Explanation**: Guarantees that concurrent transactions execute independently without interference from each other. The intermediate state of one transaction is not visible to other concurrent transactions until the first transaction is committed. This prevents issues like dirty reads, non-repeatable reads, and phantom reads.
  - **Example**: If Transaction A is updating a row, Transaction B cannot see the partially updated row until Transaction A commits its changes. SQL Server uses locking mechanisms to achieve isolation.
  - **Isolation Levels**: SQL Server provides different isolation levels (e.g., `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE`, `SNAPSHOT`) to control the degree of isolation, balancing concurrency with data consistency.

#### 3.4. **Durability**

  - **Explanation**: Guarantees that once a transaction has been committed, its changes are permanent and will survive any subsequent system failures (e.g., power outages, crashes). Committed data is written to persistent storage (disk) and is recoverable.
  - **Mechanism**: SQL Server's transaction log plays a vital role in ensuring durability. All changes are first written to the transaction log, and then to the data files. In case of a crash, the log can be used to recover committed transactions and roll back uncommitted ones.

-----

## Lab Exercises / Hands-On Practice

| \#   | Task                                                                     | Tool          | Outcome                                                               |
| --- | ------------------------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| 1   | Create a simple table `BankAccounts` with `AccountID` and `Balance`. Perform an `INSERT` and `UPDATE` within a transaction, then `COMMIT`. | SSMS / Azure Data Studio | Changes are permanently saved to the table.                           |
| 2   | Perform another `INSERT` and `UPDATE` on `BankAccounts` within a transaction, but this time, `ROLLBACK` the transaction. | SSMS / Azure Data Studio | Changes are undone; table reverts to its state before the transaction. |
| 3   | Simulate a money transfer between two accounts using a single transaction. Include a `SAVE TRANSACTION` point. | SSMS / Azure Data Studio | Transfer is atomic; demonstrate partial rollback to a savepoint.      |
| 4   | Demonstrate a transaction that fails due to a constraint violation, and ensure it's fully rolled back using `TRY...CATCH`. | SSMS / Azure Data Studio | Transaction is aborted; database state remains consistent.            |

-----

## Assessments

### Knowledge Checks (MCQs)

1.  **Which of the ACID properties ensures that all operations within a transaction are treated as a single, indivisible unit?**

      - A) Consistency
      - B) Isolation
      - C) Durability
      - D) Atomicity
      - **Answer:** D) Atomicity
      - **Explanation:** Atomicity means "all or nothing" – either all parts of the transaction succeed, or none do.

2.  **You have started a transaction with `BEGIN TRANSACTION`. Which statement would you use to make all changes permanent in the database?**

      - A) `SAVE TRANSACTION`
      - B) `ROLLBACK TRANSACTION`
      - C) `COMMIT TRANSACTION`
      - D) `END TRANSACTION`
      - **Answer:** C) `COMMIT TRANSACTION`
      - **Explanation:** `COMMIT TRANSACTION` is used to permanently save all changes made within the current explicit transaction.

3.  **What is the primary purpose of `SAVE TRANSACTION`?**

      - A) To save the transaction log to disk.
      - B) To create a point within a transaction to which you can partially roll back.
      - C) To mark the beginning of a new transaction.
      - D) To commit a transaction without affecting other concurrent transactions.
      - **Answer:** B) To create a point within a transaction to which you can partially roll back.
      - **Explanation:** `SAVE TRANSACTION` creates a named savepoint, allowing you to roll back only a portion of the transaction to that specific point, rather than the entire transaction.

### Short Answer Questions

1.  Describe a real-world scenario (e.g., an e-commerce order) where using a transaction is absolutely critical to maintain data integrity.
2.  Explain how the `Isolation` property of ACID helps prevent issues in a multi-user database environment.
3.  What happens if a `BEGIN TRANSACTION` is executed, but neither `COMMIT TRANSACTION` nor `ROLLBACK TRANSACTION` is explicitly called before the batch ends?

### Practical Task

  - **Setup**: Create two tables:
      - `Accounts`: `AccountID` (INT PK), `AccountHolder` (NVARCHAR(100)), `Balance` (DECIMAL(18, 2) CHECK (Balance \>= 0))
      - `Transactions`: `TransactionID` (INT PK IDENTITY), `FromAccountID` (INT FK), `ToAccountID` (INT FK), `Amount` (DECIMAL(18, 2)), `TransactionDate` (DATETIME DEFAULT GETDATE())
  - **Insert initial data**:
      - Account 1: `AccountID = 101`, `AccountHolder = 'Alice'`, `Balance = 1000.00`
      - Account 2: `AccountID = 102`, `AccountHolder = 'Bob'`, `Balance = 500.00`
  - **Tasks**:
    1.  **Implement a Money Transfer Procedure**:
          - Write a T-SQL script that simulates transferring $200 from `AccountID 101` to `AccountID 102`.
          - This transfer must be a single transaction.
          - It involves:
              - Decreasing `Balance` in `AccountID 101`.
              - Increasing `Balance` in `AccountID 102`.
              - Inserting a record into the `Transactions` table.
          - **Crucially, use a `TRY...CATCH` block**:
              - If `AccountID 101` has insufficient funds (e.g., `Balance - Amount < 0`), `RAISERROR` with a custom message and `ROLLBACK` the transaction.
              - If any other error occurs, `ROLLBACK` the transaction and `PRINT` the error message.
              - If successful, `COMMIT` the transaction and `PRINT` a success message.
    2.  **Test the transfer**:
          - Execute the script with a valid transfer ($200 from 101 to 102). Verify balances and transaction record.
          - Execute the script with an amount that would cause insufficient funds (e.g., $1500 from 101). Verify that the transaction is rolled back and no changes occur.
    3.  **Demonstrate `SAVE TRANSACTION`**:
          - Start a new transaction.
          - Insert a new account: `AccountID = 103`, `AccountHolder = 'Charlie'`, `Balance = 750.00`.
          - Create a `SAVE TRANSACTION` point named `AfterNewAccount`.
          - Attempt to transfer $100 from `AccountID 103` to `AccountID 101`.
          - If this transfer fails for any reason (e.g., intentionally make `AccountID 101` not exist for a moment), `ROLLBACK TRANSACTION AfterNewAccount`.
          - Finally, `COMMIT TRANSACTION` the outer transaction.
          - Verify if `AccountID 103` was created (it should be, as the rollback was partial).
    4.  **Clean up**: Drop the `Transactions` and `Accounts` tables.
