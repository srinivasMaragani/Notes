# SQL Server Security & Authentication Notes (Practical Version)

# 1. SQL Server Architecture

```text
SQL Server Instance
│
├── System Databases
│   ├── master
│   ├── tempdb
│   └── msdb
│
├── Security
│   ├── Logins
│   └── Server Roles
│
└── User Databases
    ├── TrainingDB
    └── HRDB
        │
        ├── Users
        └── Database Roles
```

***

# 2. Important SQL Server Vocabulary

## SQL Server

The actual database engine.

Responsible for:

-   Storing data
    
-   Executing queries
    
-   Managing security
    
-   Managing transactions
    

***

## SSMS

SQL Server Management Studio.

GUI tool used to:

-   Connect to SQL Server
    
-   Run queries
    
-   Create databases
    
-   Manage users and roles
    

Think:

```text
SQL Server = Engine

SSMS = Dashboard
```

***

## Instance

A running installation of SQL Server.

Examples:

```text
localhost

localhost\SQLEXPRESS
```

Each instance has:

-   Its own databases
    
-   Its own logins
    
-   Its own settings
    

***

## localhost / .

Both refer to your own machine.

Examples:

```text
localhost
.
(local)
```

Usually all connect to the same SQL Server instance.

***

# 3. Authentication

Authentication answers:

```text
Who are you?
```

before SQL Server allows access.

***

## Windows Authentication

Uses your Windows account.

Example:

```text
DESKTOP-PC\Srinivas
```

Advantages:

-   Most secure
    
-   No separate password management
    
-   Common in enterprises
    

Check current login:

```sql
SELECT SUSER_NAME();
```

***

## SQL Server Authentication

Uses SQL Server-managed credentials.

Example:

```text
Login: trainee
Password: Password@123
```

Create login:

```sql
CREATE LOGIN trainee
WITH PASSWORD='Password@123';
```

***

# 4. Login vs User

This is one of the most important concepts.

***

## Login

Server-level identity.

Created in SQL Server instance.

```sql
CREATE LOGIN trainee
WITH PASSWORD='Password@123';
```

Think:

```text
Building Entry Pass
```

***

## User

Database-level identity.

Created inside a database.

```sql
USE TrainingDB;

CREATE USER trainee
FOR LOGIN trainee;
```

Think:

```text
Room Access Inside Building
```

***

# 5. Server Roles

Server roles affect the entire SQL Server instance.

Think:

```text
Whole Building Permissions
```

***

## public

Assigned automatically to every login.

Provides minimal access.

Cannot be removed.

***

## sysadmin

Most powerful role.

Can do everything:

```sql
CREATE DATABASE
DROP DATABASE
CREATE LOGIN
ALTER SERVER
```

Equivalent concepts:

```text
Windows → Administrator

Linux → root

SQL Server → sysadmin
```

Assign:

```sql
ALTER SERVER ROLE sysadmin
ADD MEMBER trainee;
```

***

## dbcreator

Can:

-   Create databases
    
-   Alter databases
    
-   Drop databases
    

Assign:

```sql
ALTER SERVER ROLE dbcreator
ADD MEMBER trainee;
```

Practice:

```sql
CREATE DATABASE HRDB;
```

***

## securityadmin

Can:

-   Create logins
    
-   Disable logins
    
-   Reset passwords
    

Assign:

```sql
ALTER SERVER ROLE securityadmin
ADD MEMBER trainee;
```

Practice:

```sql
CREATE LOGIN report_user
WITH PASSWORD='Password@123';
```

***

## processadmin

Can terminate sessions.

Useful when:

-   Queries hang
    
-   Blocking occurs
    
-   Deadlocks happen
    

Practice:

```sql
EXEC sp_who2;
```

```sql
KILL 58;
```

***

# 6. Database Roles

Database roles affect only one database.

Think:

```text
Permissions Inside One Database
```

***

## db_owner

Full control over database.

Can:

-   Read data
    
-   Write data
    
-   Create tables
    
-   Drop tables
    
-   Create procedures
    
-   Manage permissions
    

***

## db_datareader

Read-only access.

Can:

```sql
SELECT * FROM Employees;
```

Cannot:

```sql
INSERT
UPDATE
DELETE
```

Assign:

```sql
ALTER ROLE db_datareader
ADD MEMBER trainee;
```

***

## db_datawriter

Can:

```sql
INSERT
UPDATE
DELETE
```

Assign:

```sql
ALTER ROLE db_datawriter
ADD MEMBER trainee;
```

***

## db_ddladmin

Can execute DDL commands.

Examples:

```sql
CREATE TABLE
ALTER TABLE
DROP TABLE
```

Assign:

```sql
ALTER ROLE db_ddladmin
ADD MEMBER trainee;
```

***

# 7. Server Roles vs Database Roles

## Server Roles

Control:

```text
Entire SQL Server Instance
```

Examples:

```sql
CREATE DATABASE
CREATE LOGIN
ALTER SERVER
```

Examples:

```text
sysadmin
dbcreator
securityadmin
processadmin
```

***

## Database Roles

Control:

```text
Single Database
```

Examples:

```sql
SELECT
INSERT
UPDATE
DELETE
CREATE TABLE
```

Examples:

```text
db_owner
db_datareader
db_datawriter
db_ddladmin
```

***

# 8. System Databases

***

## master

Most important system database.

Stores:

-   Logins
    
-   Server configuration
    
-   Database metadata
    

Think:

```text
Brain of SQL Server
```

Switch:

```sql
USE master;
```

***

## tempdb

Temporary workspace.

Used for:

-   Temporary tables
    
-   Sorting
    
-   Intermediate query results
    

Example:

```sql
CREATE TABLE #TempEmployees
(
    Id INT,
    Name VARCHAR(50)
);
```

Everything disappears when SQL Server restarts.

***

## msdb

Stores:

-   Backup history
    
-   SQL Agent jobs
    
-   Maintenance plans
    

Think:

```text
Scheduler Database
```

***

# 9. dbo

Stands for:

```text
Database Owner
```

Default schema.

Example:

```sql
dbo.Employees
```

Most tables are created under dbo.

***

# 10. Practical Lab

## Step 1 Create Database

```sql
CREATE DATABASE TrainingDB;
```

***

## Step 2 Create Table

```sql
USE TrainingDB;

CREATE TABLE Employees
(
    Id INT PRIMARY KEY,
    Name VARCHAR(100),
    Salary DECIMAL(10,2)
);
```

***

## Step 3 Insert Data

```sql
INSERT INTO Employees
VALUES
(1,'Srinivas',50000),
(2,'Ravi',60000);
```

***

## Step 4 Create Login

```sql
USE master;

CREATE LOGIN trainee
WITH PASSWORD='Password@123';
```

***

## Step 5 Create User

```sql
USE TrainingDB;

CREATE USER trainee
FOR LOGIN trainee;
```

***

## Step 6 Give Read Permission

```sql
ALTER ROLE db_datareader
ADD MEMBER trainee;
```

Login as trainee.

Test:

```sql
SELECT * FROM Employees;
```

Works.

Try:

```sql
INSERT INTO Employees
VALUES (3,'Anil',45000);
```

Fails.

***

## Step 7 Give Write Permission

```sql
ALTER ROLE db_datawriter
ADD MEMBER trainee;
```

Now INSERT, UPDATE and DELETE work.

***

## Step 8 Give DDL Permission

```sql
ALTER ROLE db_ddladmin
ADD MEMBER trainee;
```

Now:

```sql
CREATE TABLE Test
(
    Id INT
);
```

works.

***

# Interview Questions

1.  Difference between Login and User?
    
2.  Difference between Windows Authentication and SQL Authentication?
    
3.  Difference between Server Roles and Database Roles?
    
4.  What is sysadmin?
    
5.  What is db_datareader?
    
6.  What is db_datawriter?
    
7.  What is db_ddladmin?
    
8.  What is master database?
    
9.  What is tempdb?
    
10.  What is dbo?