## **SQL Transactions**

#### **Q1. Simple Transaction**

Create two tables:

```sql
CREATE TABLE Accounts (
    account_id INT PRIMARY KEY,
    owner VARCHAR(50),
    balance DECIMAL(10,2)
);

INSERT INTO Accounts VALUES
(1, 'Alice', 500.00),
(2, 'Bob', 300.00);
```

Questions:
1. Write a transaction that transfers **$50 from Alice to Bob**, ensuring that either both balances update or neither does.

#### **Q2. Dirty Read**

Run these two transactions in different sessions:

**Transaction A:**

```sql
START TRANSACTION;
UPDATE Accounts SET balance = balance - 100 WHERE account_id = 1;
-- Do not commit yet
```

**Transaction B:**

```sql
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
START TRANSACTION;
SELECT * FROM Accounts;
```

Questions:
1. What value does Transaction B see for Alice’s balance?
2. What happens if Transaction A rolls back afterward?

#### **Q3. Repeatable Read**

Use the following sequence:

1. In Transaction A, `START TRANSACTION` and query Alice’s balance.
```sql
START TRANSACTION;
SELECT balance FROM Accounts WHERE account_id = 1;
```
2. In Transaction B,
```sql
UPDATE Accounts SET balance = balance + 20 WHERE account_id = 1;
```
3. Re-run the same `SELECT` in Transaction A.
```sql
SELECT balance FROM Accounts WHERE account_id = 1;
```

Questions:
1. Under **REPEATABLE READ** (MySQL default), what does Transaction A see?
2. Under **READ COMMITTED**, what does it see?

#### **Q4. Serializability Challenge**

You have two transactions:

```sql
-- Transaction T1
UPDATE Accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE Accounts SET balance = balance + 100 WHERE account_id = 2;

-- Transaction T2
UPDATE Accounts SET balance = balance - 200 WHERE account_id = 2;
UPDATE Accounts SET balance = balance + 200 WHERE account_id = 1;
```

If these transactions execute concurrently:

1. Describe one possible **non-serializable** interleaving.
2. What anomaly/inconsistency could occur?
3. How does setting `SERIALIZABLE` isolation prevent this?
