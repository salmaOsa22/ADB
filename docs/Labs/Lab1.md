# Transaction Management and SQL Implementation

**Objective:**

* Understand the ACID properties.
* Implement SQL transactions.
* Understand transaction control.

## Understanding SQL Transactions

**A Transaction** is a sequence of ^^read^^ and ^^write^^ operations performed as a single logical unit of work.

**SQL Server operates in the following transaction modes:**

1. Autocommit transactions: Each individual statement is a transaction.
2. Explicit transactions: Each transaction is explicitly started with the `BEGIN TRANSACTION` statement and explicitly ended with a `COMMIT` or `ROLLBACK` statement **(which is concerned in this course)**.
3. Implicit transactions: A new transaction is implicitly started when the prior transaction completes, but each transaction is explicitly completed with a `COMMIT` or `ROLLBACK` statement.

### SQL statement for transaction control

### Transaction properties and how SQL supports them

**Transaction properties (ACID):**

1. **A - Atomicity:** All actions in transaction happen, or none happen. “*All or nothing…*”
2. **C - Consistency:** If each transaction is consistent and the database starts in a consistent state, it ends in a consistent state. “*It looks correct to me…*”
3. **I - Isolation:** Execution of one transaction is isolated from other transactions. “*All by myself…*”
4. **D - Durability:** Once a transaction commits, its effects are permanent. “*I will survive…*”

**How SQL Supports Each Property?**

| properties  | SQL Feature                          |
| ----------- | ------------------------------------ |
| Atomicity   | Use `START TRANSACTION`, followed by `COMMIT` or `ROLLBACK`  |
| Consistency | Enforced through constraints (e.g., `PRIMARY KEY`, `FOREIGN KEY`, `CHECK`)  |
| Isolation   | Set using `SET TRANSACTION ISOLATION LEVEL`    |
| Durability  |  Ensured by `COMMIT` and Logging mechanisms (e.g., write-ahead logs, redo logs) |


### Why Concurrency Control is Needed

Several problems can occur when concurrent transactions execute ^^without proper control^^, especially due to conflicting operations such as **Read-Write**, **Write-Read**, and **Write-Write** conflicts.

**Here is an illustrated problem with SQL transactions to simulate the problem:**

#### 1. Lost update problem

#### 2. The Temporary Update (or Dirty Read) Problem

This occurs when one transaction ^^reads^^ data that has been ^^written^^ by another transaction that hasn’t committed yet. If the first transaction is later **rolled back**, the second transaction ends up working with invalid or temporary data — a dirty read.

**Example Scenario (Simulated):**

In an online electronics store:

1.  Ahmed initiates a purchase of 200 laptops. A transaction begins and temporarily reduces the stock quantity to 0, but the transaction is not yet committed.

2.  Meanwhile, Mohamed checks the available stock and sees 0 laptops — based on User A’s uncommitted change.

3. Then, Ahmed cancels the purchase, and the transaction is rolled back. The stock returns to its original quantity (e.g., 200).

4. However, Mohamed made a decision based on incorrect (temporary) data — the system gave them a dirty read.

**SQL Transaction for the scienario:**

1. Create the `Products` table to simulate the scenario
    ```SQL
    -- Create ProductDB Database
    CREATE DATABASE IF NOT EXISTS ProductDB;
    USE ProductDB;

    -- Create Products table
    CREATE TABLE Products (
        Id INT PRIMARY KEY,
        ProductName VARCHAR(100),
        Quantity INT
    );

    -- Insert Sample data
    INSERT INTO Products (Id, ProductName, Quantity) VALUES
    (1, 'Mobile', 100),
    (2, 'Tablet', 50),
    (3, 'Laptop', 200);
    ```
2.  In Session 1: Connect using the previous connection from [**Lab0**](Lab0.md) (named local), and open a new SQL query tab
    ```SQL
    USE ProductDB;

    -- Transaction 1: Simulate purchase
    START TRANSACTION;
        -- Pay 200 units from product with ID = 3 (assumed to be a laptop)
        UPDATE Products 
        SET Quantity = Quantity - 200
        WHERE Id = 3;
        -- Simulate waiting for payment confirmation
        DO SLEEP(15);
            -- Payment failed — rollback the transaction
    ROLLBACK;
    ```
    **We use SLEEP to simulate concurrent transactions. Note that `DO SLEEP(15)` works only in MySQL 8.0.29 or later**
3. Go to the Home tab and open the `local` connection again. (This step simulates a second session connected to the database.) Then, open a new SQL query tab to continue.
    ```SQL
    USE ProductDB;

    -- Transaction 2: Retrieve Laptop record
    SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
    START TRANSACTION;
        SELECT *
        FROM Products 
        WHERE Id=3;
    COMMIT;
    ```
4. In Session 1: Run the transaction and wait
    <figure markdown="span">
    ![Windows Setting](images/transaction 1-before rollback.png){ width="400" }
    </figure>
5. While Session 1 is still sleeping: Run Session 2
    <figure markdown="span">
    ![Windows Setting](images/transaction 2.png){ width="400" }
    </figure>
6. After Session 1 completes and rolls back
    <figure markdown="span">
    ![Windows Setting](images/transaction 1-after rollback.png){ width="400" }
    </figure>
7. Conclusion: Session 2 was able to see the updated quantity (0 laptops) before Session 1 rolled back. This leads to inconsistent or misleading data, which is why isolation levels like `READ COMMITTED or REPEATABLE READ are important in real-world applications.

#### 3. The Incorrect Summary Problem

## Assignment: Simulate The Unrepeatable Read Problem
!!! attention "Due Date on 14/6/2025"

*   Read page 752 on the primary book (FUNDAMENTALS OF Database Systems)
*   <span style="color: red;">See the requirement about the structures of the lab [here](general_instructions.md)</span>

### What to assign: 

1. A paragraph describing the scenario you will use to simulate the Unrepeatable Read problem.

2. A screenshot of the table you created for the simulation.

3. A screenshot showing the result of the first transaction after execution.

4. A screenshot showing the result of the second transaction after execution.

5. A paragraph discussing what happened, and how it demonstrates the Unrepeatable Read issue.
