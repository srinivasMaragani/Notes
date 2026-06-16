# Database Concepts

## Database Languages

SQL database operations are broadly classified into the following categories:

- **DDL (Data Definition Language)**
- **DML (Data Manipulation Language)**
- **DQL (Data Query Language)**
- **DCL (Data Control Language)**
- **TCL (Transaction Control Language)**

---

# DCL (Data Control Language)

**DCL (Data Control Language)** is used to control access permissions and privileges in a database.

DCL commands manage:

- User permissions
- Security policies
- Access control
- Rights to database objects

These privileges are required to perform operations such as:

- Creating tables
- Creating views
- Creating sequences
- Accessing or modifying data

---

## Types of Privileges

### 1. System Privileges

System privileges grant permissions at the **server level**.

#### Built-in Server-Level Roles

| Role Name | Description |
|------------|------------|
| `sysadmin` | Full administrative privileges on the SQL Server instance. Complete control over all databases and server settings. |
| `serveradmin` | Can manage server-wide configurations and shut down the server. |
| `setupadmin` | Can manage linked servers and startup procedures. |
| `processadmin` | Can view and terminate server processes. |
| `diskadmin` | Can manage database files, including attach and detach operations. |
| `dbcreator` | Can create, alter, and drop databases. |
| `securityadmin` | Manages logins, permissions, and server security. |
| `bulkadmin` | Can execute `BULK INSERT` operations. |
| `public` | Default role assigned to all logins with basic server access. |

---

### 2. Object Privileges

Object privileges grant permissions at the **database level**.

#### Built-in Database Roles

| Role Name | Description |
|------------|------------|
| `db_owner` | Full control over the database. |
| `db_accessadmin` | Can add or remove database users. |
| `db_securityadmin` | Manages database roles and permissions. |
| `db_ddladmin` | Can execute DDL commands such as `CREATE`, `ALTER`, and `DROP`. |
| `db_backupoperator` | Can back up the database. |
| `db_datareader` | Can read all data in the database. |
| `db_datawriter` | Can insert, update, and delete data. |
| `db_denydatareader` | Explicitly denies read access to database data. |
| `db_denydatawriter` | Explicitly denies write access to database data. |
| `db_executor` | Can execute stored procedures and functions. |
| `public` | Default role for all database users. |

---

## User Creation Workflow

### Step 1: Create a Login (Server Level)

```sql
USE master;

CREATE LOGIN chaitanya
WITH PASSWORD = 'cocoon@777';
```

---

### Step 2: Create a User (Database Level)

```sql
USE master;

CREATE USER chaitanya
FOR LOGIN chaitanya;
```

> **Note:** In Azure SQL Database, server-level roles are not available in the same way as on SQL Server instances.

---

### Step 3: Assign a Database Role

```sql
USE [db_name];

ALTER ROLE db_ddladmin
ADD MEMBER chaitanya;
```

This grants the user permission to execute DDL operations such as:

- CREATE
- ALTER
- DROP

---

# TCL (Transaction Control Language)

**Transaction Control Language (TCL)** is used to manage transactions and maintain data integrity within a database.

A transaction is a group of SQL statements that are executed as a single unit of work.

TCL ensures:

- Data consistency
- Data integrity
- Recovery from failures
- Controlled commit and rollback operations

---

## TCL Commands

### 1. COMMIT

The `COMMIT` command permanently saves all changes made during the current transaction.

Once committed:

- Changes become permanent.
- Changes cannot be undone using `ROLLBACK`.

#### Syntax

```sql
COMMIT;
```

#### Example

```sql
BEGIN TRANSACTION;

UPDATE employees
SET salary = salary + 5000
WHERE employee_id = 101;

COMMIT;
```

---

### 2. ROLLBACK

The `ROLLBACK` command undoes all changes made during the current transaction that have not yet been committed.

Useful when:

- An error occurs
- Validation fails
- Changes need to be discarded

#### Syntax

```sql
ROLLBACK;
```

#### Example

```sql
BEGIN TRANSACTION;

UPDATE employees
SET salary = salary + 5000
WHERE employee_id = 101;

ROLLBACK;
```

The salary update is cancelled and the database returns to its previous state.

---

### 3. SAVEPOINT

The `SAVEPOINT` command creates a checkpoint within a transaction.

You can roll back to a specific savepoint without rolling back the entire transaction.

#### Syntax

```sql
SAVEPOINT savepoint_name;
```

#### Example

```sql
BEGIN TRANSACTION;

INSERT INTO employees(name)
VALUES ('John');

SAVEPOINT sp1;

UPDATE employees
SET salary = 50000
WHERE name = 'John';

ROLLBACK TO SAVEPOINT sp1;

COMMIT;
```

Result:

- The employee record is inserted.
- The salary update is rolled back.
- The insert remains committed.

---

# Transaction Flow

```text
BEGIN TRANSACTION
        │
        ▼
 Execute SQL Statements
        │
        ▼
   SAVEPOINT (Optional)
        │
        ▼
   More SQL Statements
        │
        ▼
 ┌───────────────┐
 │ Success ?     │
 └───────┬───────┘
         │
   Yes   │   No
         │
         ▼
     COMMIT
         │
         ▼
     Changes Saved

 OR

     ROLLBACK
         │
         ▼
 Changes Reverted
```

---

# Quick Revision

| Command | Purpose |
|----------|----------|
| `GRANT` | Give permissions to users. |
| `REVOKE` | Remove permissions from users. |
| `COMMIT` | Permanently save transaction changes. |
| `ROLLBACK` | Undo uncommitted changes. |
| `SAVEPOINT` | Create a checkpoint within a transaction. |

---

# Interview Questions

### DCL

1. What is DCL?
2. Difference between system privileges and object privileges?
3. What is the difference between a Login and a User?
4. What is the purpose of `db_owner` and `db_ddladmin` roles?
5. What are the functions of `GRANT` and `REVOKE`?

### TCL

1. What is a transaction?
2. Difference between `COMMIT` and `ROLLBACK`?
3. What is a `SAVEPOINT`?
4. Can a committed transaction be rolled back?
5. What is ACID in transactions?