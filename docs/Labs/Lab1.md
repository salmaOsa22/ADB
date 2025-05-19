# Transaction Management and SQL Implementation

**Objective:**

* Understand the ACID properties.
* Implement SQL transactions.
* Understand transaction control.

## Understanding SQL Transactions

**A Transaction** is a sequence of ^^read^^ and ^^write^^ operations performed as a single logical unit of work.

**SQL Server operates in the following transaction modes:**

1. Autocommit transactions: Each individual statement is a transaction.
2. Explicit transactions: Each transaction is explicitly started with the `BEGIN TRANSACTION` statement and explicitly ended with a `COMMIT` or `ROLLBACK` statement (which is concerned in this course).
3. Implicit transactions: A new transaction is implicitly started when the prior transaction completes, but each transaction is explicitly completed with a `COMMIT` or `ROLLBACK` statement.

### SQL statement for transaction control

### Transaction properties and how SQL supports them

### Why Concurrency Control is Needed

Several problems can occur when concurrent transactions execute in an ^^uncontrolled^^ manner.

**Here is an illustrated problem with SQL transactions to simulate the problem:**

1. Lost update problem

2. The Temporary Update (or Dirty Read) Problem

3. The Incorrect Summary Problem

## Assignment: Simulate The Unrepeatable Read Problem