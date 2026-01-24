# Transactions

In the context of relational databases, a **transaction** is a single logical unit of work, which may consist of one or more database operations (e.g., `INSERT`, `UPDATE`, `DELETE`, `SELECT` statements). The crucial aspect of transactions is that they are treated as an indivisible sequence of operations: either all operations within the transaction succeed and are "committed" to the database, or if any operation fails, the entire transaction is "rolled back," leaving the database in its state prior to the transaction's start.

This all-or-nothing property is fundamental for maintaining data integrity and reliability, especially in environments where multiple users or applications are accessing and modifying the same data concurrently.

## ACID Properties

The reliability of database transactions is often described by the **ACID** properties:

1.  **Atomicity**:
    *   **"All or Nothing"**: A transaction is treated as a single, indivisible unit. All its operations must complete successfully, or none of them should. If any part of the transaction fails, the entire transaction is aborted, and the database is rolled back to its state before the transaction began.
    *   *Example*: A money transfer from Account A to Account B. This involves deducting from A and adding to B. If deducting from A succeeds but adding to B fails, the entire transaction is undone, ensuring money isn't lost or duplicated.

2.  **Consistency**:
    *   **"Valid State"**: A transaction must bring the database from one valid state to another. This means that any data written to the database must be valid according to all defined rules, constraints, triggers, and cascades. If a transaction violates any integrity constraints, it is rolled back.
    *   *Example*: If a transfer of money would make an account balance negative where negative balances are not allowed, the transaction is rolled back.

3.  **Isolation**:
    *   **"Concurrent Independence"**: The execution of concurrent transactions should result in a system state that would be achieved if transactions were executed serially (one after the other). Each transaction should appear to run in isolation from other concurrent transactions.
    *   *Example*: If two users try to withdraw money from the same account simultaneously, the database ensures that one transaction completes before the other, preventing a scenario where both withdrawals succeed but the account only had enough for one.

4.  **Durability**:
    *   **"Permanent Changes"**: Once a transaction has been committed, its changes are permanent and will survive any subsequent system failures (e.g., power outages, crashes). This is typically achieved by writing transaction logs to non-volatile storage before confirming the commit.
    *   *Example*: After the money transfer from Account A to Account B is committed, even if the database server crashes immediately after, the change in both account balances will persist once the system recovers.

## Transaction Life Cycle

A typical transaction involves the following states and operations:

*   **BEGIN TRANSACTION (or START TRANSACTION)**: Marks the beginning of a transaction. All subsequent operations until a `COMMIT` or `ROLLBACK` are part of this transaction.
*   **Operations**: `INSERT`, `UPDATE`, `DELETE`, `SELECT` statements.
*   **COMMIT**: Successfully saves all changes made during the transaction permanently to the database. Once committed, changes are visible to other transactions.
*   **ROLLBACK**: Undoes all changes made during the transaction, restoring the database to its state before the transaction began. This happens if an error occurs or if the user explicitly cancels the transaction.

## Importance of Transactions

Transactions are essential for:

*   **Maintaining Data Integrity**: They guarantee that data remains accurate and consistent, even in the face of errors or concurrent access.
*   **Reliability**: Provides a reliable mechanism for complex operations that involve multiple steps.
*   **Concurrency Control**: Ensures that multiple users can access and modify data simultaneously without interfering with each other's work or leading to inconsistent results.

## Example (Conceptual SQL)

A money transfer from one account to another:

```sql
BEGIN TRANSACTION;

-- Deduct amount from sender's account
UPDATE Accounts
SET Balance = Balance - 100
WHERE AccountID = 'SenderAccount';

-- Check if sender has sufficient balance (simplified check)
-- In a real scenario, this check might be more robust and happen before the UPDATE
-- If Balance < 0, then ROLLBACK

-- Add amount to receiver's account
UPDATE Accounts
SET Balance = Balance + 100
WHERE AccountID = 'ReceiverAccount';

-- If both updates succeed, commit the transaction
COMMIT;

-- If any error occurs during the transaction, automatically or manually rollback
-- (e.g., if a constraint is violated, or an explicit condition isn't met)
-- ROLLBACK;
```

If the `UPDATE` to `SenderAccount` succeeds but the `UPDATE` to `ReceiverAccount` fails for any reason (e.g., `ReceiverAccount` does not exist, or a network error), the `ROLLBACK` ensures that the deduction from `SenderAccount` is also undone, leaving both accounts in their original state. This upholds atomicity and consistency.

Understanding and correctly implementing transactions is crucial for building robust and reliable database applications.
