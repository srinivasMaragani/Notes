# MSSQL Partitioning: From Beginner to Advanced

***

# Phase 1: Foundational Understanding

## What is Partitioning?

Partitioning is a technique that divides a large table into smaller logical pieces called **partitions**, while still appearing as a single table to applications and users.

Think of it like:

📚 **Library Analogy**

Without partitioning:

-   All books are stored on one giant shelf.
    

With partitioning:

-   Books are organized by year:
    
    -   Shelf 2023
        
    -   Shelf 2024
        
    -   Shelf 2025
        

When someone asks for a 2025 book, the librarian goes directly to the 2025 shelf instead of scanning every shelf.

The same happens in SQL Server.

***

## What Problem Does Partitioning Solve?

As tables grow:

```sql
Orders
---------
500 Million Rows
```

Problems:

-   Slow scans
    
-   Long maintenance windows
    
-   Large index rebuild times
    
-   Difficult archival processes
    
-   Heavy I/O operations
    

Partitioning helps by:

✅ Faster data access

✅ Easier maintenance

✅ Faster archival

✅ Better manageability

***

## How Partitioning Works in MSSQL

Important:

Partitioning **does not create multiple tables**.

SQL Server still sees:

```sql
Orders
```

Internally it stores rows in partitions:

```text
Orders

Partition 1 -> 2023 Data
Partition 2 -> 2024 Data
Partition 3 -> 2025 Data
Partition 4 -> Future Data
```

Users still query:

```sql
SELECT *
FROM Orders
WHERE OrderDate >= '2025-01-01'
```

SQL Server automatically accesses only relevant partitions.

This is called:

### Partition Elimination

One of partitioning's biggest benefits.

***

# Common Partitioning Strategies

***

## 1. Range Partitioning

Most common in SQL Server.

Data divided based on value ranges.

Example:

```text
Sales Table

2023 Data → Partition 1
2024 Data → Partition 2
2025 Data → Partition 3
```

Partition key:

```sql
OrderDate
```

Best for:

-   Sales
    
-   Transactions
    
-   Logs
    
-   Audit tables
    

***

## 2. Hash Partitioning

Rows distributed using a hash function.

Example:

```text
CustomerID

Hash(CustomerID) % 4

Result:
Partition 1
Partition 2
Partition 3
Partition 4
```

Purpose:

-   Even distribution
    

Useful in distributed databases.

⚠ SQL Server does not provide native hash partitioning like PostgreSQL or Oracle.

Usually simulated using computed columns.

***

## 3. List Partitioning

Rows grouped by category values.

Example:

```text
Region

North
South
East
West
```

Partition based on region.

Example:

```text
North -> Partition 1
South -> Partition 2
East -> Partition 3
West -> Partition 4
```

⚠ SQL Server does not provide native LIST partitioning.

Typically implemented using:

-   Computed columns
    
-   Multiple filegroups
    
-   Partition functions
    

***

# MSSQL Partitioning Components

***

## 1. Partition Function

Defines:

> How rows are divided.

Example:

```sql
CREATE PARTITION FUNCTION pf_SalesYear(DATE)
AS RANGE RIGHT
FOR VALUES
(
    '2024-01-01',
    '2025-01-01'
);
```

Creates partitions:

```text
Partition 1 < 2024
Partition 2 2024
Partition 3 2025+
```

***

## 2. Partition Scheme

Defines:

> Where partitions are stored.

Example:

```sql
CREATE PARTITION SCHEME ps_SalesYear
AS PARTITION pf_SalesYear
ALL TO ([PRIMARY]);
```

Maps partitions to filegroups.

***

## 3. Filegroups

Physical storage locations.

Example:

```text
FG_2023
FG_2024
FG_2025
```

Benefits:

-   Backup individual partitions
    
-   Restore faster
    
-   Archive easily
    

***

# Architecture Diagram

```text
Table
  |
  v

Partition Function
  |
  v

Partition Scheme
  |
  v

Filegroups
  |
  v

Physical Storage
```

***

# Phase 2: Practical Implementation

***

# Step 1: Create Sample Database

```sql
CREATE DATABASE PartitionDemo;
GO

USE PartitionDemo;
GO
```

***

# Step 2: Create Filegroups

```sql
ALTER DATABASE PartitionDemo
ADD FILEGROUP FG2023;

ALTER DATABASE PartitionDemo
ADD FILEGROUP FG2024;

ALTER DATABASE PartitionDemo
ADD FILEGROUP FG2025;
```

Add files:

```sql
ALTER DATABASE PartitionDemo
ADD FILE
(
    NAME = Sales2023,
    FILENAME = 'C:\Data\Sales2023.ndf'
)
TO FILEGROUP FG2023;
```

Repeat for others.

***

# Step 3: Create Partition Function

```sql
CREATE PARTITION FUNCTION pf_OrderDate (DATE)
AS RANGE RIGHT
FOR VALUES
(
    '2024-01-01',
    '2025-01-01'
);
```

Result:

```text
Partition 1 -> before 2024

Partition 2 -> 2024

Partition 3 -> 2025+
```

***

# Step 4: Create Partition Scheme

```sql
CREATE PARTITION SCHEME ps_OrderDate
AS PARTITION pf_OrderDate
TO
(
    FG2023,
    FG2024,
    FG2025
);
```

***

# Step 5: Create Partitioned Table

```sql
CREATE TABLE Orders
(
    OrderID INT IDENTITY,
    OrderDate DATE NOT NULL,
    Amount DECIMAL(10,2)
)
ON ps_OrderDate(OrderDate);
```

Important:

```sql
ON ps_OrderDate(OrderDate)
```

makes the table partitioned.

***

# Step 6: Insert Data

```sql
INSERT INTO Orders
VALUES
('2023-05-01',1000),
('2024-06-10',2000),
('2025-03-15',3000);
```

Rows automatically go into correct partitions.

***

# Step 7: View Partition Distribution

```sql
SELECT
    $PARTITION.pf_OrderDate(OrderDate) AS PartitionNumber,
    COUNT(*) AS RowsCount
FROM Orders
GROUP BY
    $PARTITION.pf_OrderDate(OrderDate);
```

Output:

```text
Partition 1 = 1 row
Partition 2 = 1 row
Partition 3 = 1 row
```

***

# Step 8: Query Partitioned Table

Normal query:

```sql
SELECT *
FROM Orders
WHERE OrderDate >= '2025-01-01';
```

SQL Server performs:

### Partition Elimination

Only partition 3 is scanned.

***

# Step 9: Add New Partition

Suppose 2026 arrives.

```sql
ALTER PARTITION SCHEME ps_OrderDate
NEXT USED FG2025;
```

```sql
ALTER PARTITION FUNCTION pf_OrderDate()
SPLIT RANGE ('2026-01-01');
```

Now:

```text
P1 < 2024
P2 2024
P3 2025
P4 2026+
```

***

# Step 10: Merge Old Partition

Remove boundary:

```sql
ALTER PARTITION FUNCTION pf_OrderDate()
MERGE RANGE ('2024-01-01');
```

Partitions merge together.

***

# Step 11: Monitor Partition Usage

View partitions:

```sql
SELECT
    p.partition_number,
    p.rows
FROM sys.partitions p
WHERE object_id = OBJECT_ID('Orders');
```

***

# Step 12: Check Query Plans

Enable execution plan:

```sql
Ctrl + M
```

Run:

```sql
SELECT *
FROM Orders
WHERE OrderDate='2025-01-15';
```

Look for:

```text
Partition Elimination
```

This confirms optimization.

***

# Phase 3: Progressive Problem Set

***

# Beginner Problem 1

## Scenario

An e-commerce company stores sales data by year.

***

## Data Scale

```text
1 Million Rows
```

***

## Requirement

Create partitions:

```text
2023
2024
2025
```

***

## Solution

### Function

```sql
CREATE PARTITION FUNCTION pf_Sales(DATE)
AS RANGE RIGHT
FOR VALUES
(
    '2024-01-01',
    '2025-01-01'
);
```

### Scheme

```sql
CREATE PARTITION SCHEME ps_Sales
AS PARTITION pf_Sales
ALL TO ([PRIMARY]);
```

### Table

```sql
CREATE TABLE Sales
(
    SaleID INT,
    SaleDate DATE,
    Amount MONEY
)
ON ps_Sales(SaleDate);
```

### Verify

```sql
SELECT
$PARTITION.pf_Sales(SaleDate),
COUNT(*)
FROM Sales
GROUP BY
$PARTITION.pf_Sales(SaleDate);
```

### Why?

Sales are usually queried by date.

Range partitioning is ideal.

***

# Beginner Problem 2

## Scenario

Audit logs generated daily.

***

## Scale

```text
50 Million Rows
```

***

## Requirement

Partition by month.

***

## Solution

```sql
CREATE PARTITION FUNCTION pf_Log(DATE)
AS RANGE RIGHT FOR VALUES
(
'2025-02-01',
'2025-03-01',
'2025-04-01',
'2025-05-01'
);
```

```sql
CREATE PARTITION SCHEME ps_Log
AS PARTITION pf_Log
ALL TO ([PRIMARY]);
```

```sql
CREATE TABLE AuditLog
(
    LogID BIGINT,
    LogDate DATE,
    Message VARCHAR(200)
)
ON ps_Log(LogDate);
```

### Benefit

Monthly cleanup becomes easy.

***

# Intermediate Problem 1

## Scenario

Customer table distributed evenly.

***

## Scale

```text
100 Million Customers
```

***

## Requirement

Simulate hash partitioning.

***

## Solution

### Computed Column

```sql
ALTER TABLE Customers
ADD PartitionKey AS (CustomerID % 4);
```

### Function

```sql
CREATE PARTITION FUNCTION pf_Hash(INT)
AS RANGE LEFT FOR VALUES
(0,1,2);
```

### Scheme

```sql
CREATE PARTITION SCHEME ps_Hash
AS PARTITION pf_Hash
ALL TO ([PRIMARY]);
```

### Table

```sql
CREATE TABLE Customers
(
    CustomerID INT,
    Name VARCHAR(100),
    PartitionKey AS (CustomerID % 4)
)
ON ps_Hash(PartitionKey);
```

### Why?

Produces near-even distribution.

***

# Intermediate Problem 2

## Scenario

Orders by region.

***

## Scale

```text
30 Million Rows
```

***

## Requirement

Simulate list partitioning.

```text
North
South
East
West
```

***

## Solution

```sql
ALTER TABLE Orders
ADD RegionCode AS
(
CASE Region
WHEN 'North' THEN 1
WHEN 'South' THEN 2
WHEN 'East' THEN 3
WHEN 'West' THEN 4
END
);
```

Partition on:

```sql
RegionCode
```

using a partition function.

### Why?

SQL Server lacks native list partitioning.

Computed columns simulate it.

***

# Advanced Problem 1

## Scenario

Bank transaction table.

***

## Scale

```text
1 Billion Rows
```

***

## Requirement

Archive old years.

***

## Solution

Create yearly partitions.

```text
2022
2023
2024
2025
```

Move old partition using:

```sql
ALTER TABLE Transactions
SWITCH PARTITION 1
TO TransactionsArchive;
```

### Benefit

Instant archival.

No row movement.

Very fast.

***

# Advanced Problem 2

## Scenario

Application logs.

***

## Scale

```text
5 Billion Rows
```

***

## Requirement

Delete old logs monthly.

***

## Solution

Partition by month.

Instead of:

```sql
DELETE FROM Logs
WHERE LogDate < '2024-01-01';
```

Use:

```sql
ALTER PARTITION FUNCTION pf_Logs()
MERGE RANGE ('2024-01-01');
```

or switch partition out.

### Benefit

Seconds instead of hours.

Minimal transaction log growth.

***

# Advanced Problem 3 (Real Enterprise Scenario)

## Scenario

Large e-commerce platform.

***

## Scale

```text
10 Billion Orders
```

***

## Requirements

-   Fast reporting
    
-   Historical archive
    
-   Easy maintenance
    
-   High availability
    

***

## Design

```text
Partition by OrderDate

2022 -> FG2022
2023 -> FG2023
2024 -> FG2024
2025 -> FG2025
```

Partition Function  
↓

Partition Scheme  
↓

Separate Filegroups  
↓

Partition Switching  
↓

Archive Database

````

### Maintenance Process

Monthly:

```sql
SPLIT RANGE
````

Yearly:

```sql
SWITCH PARTITION
```

Archive:

```sql
MERGE RANGE
```

### Benefits

-   Fast reporting
    
-   Reduced maintenance
    
-   Faster backups
    
-   Easy archival
    
-   Better partition elimination
    

***

# MSSQL Partitioning Mastery Roadmap

Practice in this order:

1.  Create partition functions
    
2.  Create partition schemes
    
3.  Create yearly partitions
    
4.  Query using partition elimination
    
5.  Add partitions using SPLIT
    
6.  Remove partitions using MERGE
    
7.  Inspect `sys.partitions`
    
8.  Implement monthly log partitioning
    
9.  Implement partition switching
    
10.  Design a billion-row archival strategy
     

Once you can confidently perform **SPLIT, MERGE, SWITCH, and partition elimination analysis**, you're using the same core partitioning techniques employed in large SQL Server systems handling hundreds of millions or billions of rows.