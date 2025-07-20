# Non-Repeatable Read vs Phantom Read

Both non-repeatable reads and phantom reads are concurrency anomalies that can occur in database systems when multiple transactions interact with the same data. Here's a clear explanation of each:

## Non-Repeatable Read
- **Definition**: Occurs when a transaction reads the same row twice but gets different data each time because another transaction modified that row between the two reads.
- **Scenario**:
  1. Transaction A reads a row
  2. Transaction B modifies that same row and commits
  3. Transaction A reads the same row again and sees different data
- **Example**: 
  - First read: `SELECT balance FROM accounts WHERE id = 1` → returns 100
  - Another transaction updates balance to 50
  - Second read: `SELECT balance FROM accounts WHERE id = 1` → returns 50

## Phantom Read
- **Definition**: Occurs when a transaction executes the same query twice but gets a different set of rows because another transaction inserted or deleted rows that match the query criteria.
- **Scenario**:
  1. Transaction A executes a query with certain conditions
  2. Transaction B inserts or deletes rows that satisfy those conditions and commits
  3. Transaction A executes the same query again and sees different rows
- **Example**:
  - First query: `SELECT * FROM accounts WHERE balance > 50` → returns 2 rows
  - Another transaction inserts a new account with balance=60
  - Second query: `SELECT * FROM accounts WHERE balance > 50` → returns 3 rows

## Key Differences
| Aspect          | Non-Repeatable Read | Phantom Read |
|-----------------|---------------------|--------------|
| **What changes** | Data in existing rows | Set of rows returned |
| **Operation**   | UPDATE on existing rows | INSERT or DELETE of rows |
| **Scope**       | Same row read twice | Same query executed twice |

## Prevention
- **Non-repeatable reads** can be prevented with row-level locks or snapshot isolation
- **Phantom reads** require range locks or predicate locks, or using serializable isolation level

Both issues are related to transaction isolation levels in databases, with different isolation levels preventing different types of these anomalies.