# MSSQL Indexes & Pages - Detailed Notes (Beginner → Internal Understanding)

***

# 1. Why Indexes Exist

## The Real Problem

Suppose a table contains:

```sql
CREATE TABLE Employees
(
    EmployeeID INT,
    Name VARCHAR(100)
);
```

Data:

| EmployeeID | Name |
| --- | --- |
| 1   | Ram |
| 2   | Ravi |
| 3   | Kumar |
| ... | ... |
| 10000000 | Srinivas |

Query:

```sql
SELECT *
FROM Employees
WHERE EmployeeID = 9000000;
```

Question:

> How does SQL Server know where EmployeeID=9000000 is stored?

Without an index:

```text
It doesn't know.
```

SQL Server must search.

***

# 2. How SQL Server Stores Data

SQL Server does NOT store rows directly.

It stores rows inside **Pages**.

***

## Page

A page is the smallest unit SQL Server reads from disk.

Page size:

```text
8 KB
```

Example:

```text
Employees Table

Page-1
-------
1
2
3

Page-2
-------
4
5
6

Page-3
-------
7
8
9
```

SQL Server reads pages, not individual rows.

***

# 3. What Happens Without an Index

Query:

```sql
SELECT *
FROM Employees
WHERE EmployeeID = 9;
```

SQL Server may do:

```text
Read Page-1
Not found

Read Page-2
Not found

Read Page-3
Found
```

This is called:

```text
Table Scan
```

or

```text
Clustered Index Scan
```

depending on table structure.

***

# 4. What Is An Index?

An index is a separate structure created by SQL Server.

Example:

```sql
CREATE INDEX IX_EmployeeID
ON Employees(EmployeeID);
```

SQL Server creates:

```text
EmployeeID     Location
----------     --------
1              Row Location
2              Row Location
3              Row Location
```

Purpose:

```text
Find data location quickly.
```

***

# 5. Index Is NOT The Table

Many beginners think:

```text
Index = Data
```

Wrong.

```text
Table = Actual Data

Index = Navigation Structure
```

***

# 6. Why Not Search Entire Index?

Suppose:

```text
10 Million Rows
```

Index also has:

```text
10 Million Entries
```

Wouldn't searching index also be slow?

Answer:

```text
No
```

Because SQL Server uses a B-Tree.

***

# 7. What Is A B-Tree?

B = Balanced

Tree = Hierarchical Structure

Example:

```text
              500
             /   \
            /     \
         <500    >500
```

Purpose:

```text
Reduce search work dramatically.
```

***

# 8. B-Tree Components

## Root Page

Top-most page.

```text
            Root
             500
```

Every search starts here.

***

## Child Page

Any page below another page.

```text
Root
 |
 +-- Child
```

***

## Leaf Page

Lowest level page.

```text
Root
 |
 Leaf
```

Contains actual index entries.

***

## Intermediate Page

Between Root and Leaf.

```text
Root
 |
Intermediate
 |
Leaf
```

***

# 9. Page Roles

Page role depends on position.

Not how it was created.

***

## Root

```text
No parent
```

***

## Leaf

```text
No children
```

***

## Intermediate

```text
Has parent
Has children
```

***

# 10. Page Split

Assume:

```text
Page Capacity = 10 rows
```

Current page:

```text
10
20
30
40
50
60
70
80
90
100
```

Insert:

```text
110
```

Page overflows.

***

## Before Split

```text
Page A

10
20
30
40
50
60
70
80
90
100
110
```

***

## After Split

```text
Page A

10
20
30
40
50
```

```text
Page B

60
70
80
90
100
110
```

***

# 11. Why Split 50-50?

Common Question:

> Why not keep 10 rows in Page A and put only 110 in Page B?

Example:

```text
Page A

10
20
30
40
50
60
70
80
90
100

Page B

110
```

Problem:

Insert:

```text
55
```

Page A already full.

Another split required immediately.

***

SQL Server leaves free space:

```text
Page A

10
20
30
40
50
```

```text
Page B

60
70
80
90
100
110
```

Future inserts can fit.

***

# 12. Root Creation

After first split:

```text
Page A
10
20
30
40
50

Page B
60
70
80
90
100
110
```

Need navigation.

Root created:

```text
            Root
             60
            /  \
           /    \

       PageA   PageB
```

Meaning:

```text
<60    -> PageA

>=60   -> PageB
```

***

# 13. Root Stores Separators

Important:

Root does NOT store:

```text
10|20|30|40|50|60
```

Root stores:

```text
60
```

Only.

Purpose:

```text
Navigation
```

***

# 14. More Splits

Suppose:

```text
Page B fills
```

Split:

```text
Page A
10 20 30 40 50

Page B
60 70 80

Page C
90 100 110 120
```

Root becomes:

```text
60 | 90
```

Structure:

```text
             Root

          60 | 90

        /    |    \

       A     B     C
```

***

# 15. Root Overflow

Suppose Root can hold:

```text
10 separators
```

Eventually:

```text
60|90|120|150|180|210|240|270|300|330
```

Insert one more.

Root overflows.

***

# 16. Root Split

Old Root:

```text
60|90|120|150|180|210|240|270|300|330
```

Split:

```text
Left Parent

60|90|120|150|180
```

```text
Right Parent

240|270|300|330
```

New Root created:

```text
                 Root
                  210

                /     \

         LeftParent   RightParent
```

***

# 17. Tree Growth

Trees grow upward.

Not downward.

Example:

### Stage 1

```text
Root + Leaf
```

### Stage 2

```text
Root
 |
Leaves
```

### Stage 3

```text
Root
 |
Intermediate
 |
Leaves
```

***

# 18. Duplicate Values

Example:

```sql
CREATE INDEX IX_Department
ON Employees(Department);
```

Data:

```text
HR
HR
HR
IT
IT
Sales
```

Duplicates are allowed.

***

## Leaf Level

Conceptually:

```text
HR -> Row1

HR -> Row2

HR -> Row3

IT -> Row4
```

SQL Server stores hidden row locators.

***

# 19. Example: 11 HR + 5 IT

Page Capacity:

```text
10
```

Data:

```text
HR HR HR HR HR HR HR HR HR HR HR
IT IT IT IT IT
```

***

## First Split

```text
Page A

HR
HR
HR
HR
HR
```

```text
Page B

HR
HR
HR
HR
HR
HR
```

Root created.

***

## After IT Inserts

Possible structure:

```text
Page A

HR
HR
HR
HR
HR
```

```text
Page B

HR
HR
HR
```

```text
Page C

HR
HR
HR
IT
IT
IT
IT
IT
```

Root:

```text
HR | IT
```

(Conceptually. Actual internal separator includes hidden unique information.)

***

# 20. Strings In Indexes

Example:

```sql
CREATE INDEX IX_Name
ON Employees(Name);
```

Data:

```text
Anil
John
Kumar
Ravi
Srinivas
```

Sorted alphabetically.

***

Search:

```sql
SELECT *
FROM Employees
WHERE Name='Ravi';
```

SQL Server compares:

```text
Ravi > Kumar
```

and navigates accordingly.

***

# 21. Real Search Example

Tree:

```text
                     Root
                  400 | 700

                /     |     \

             A        B       C
```

Find:

```text
800
```

Root:

```text
800 > 700
```

Go right.

Read page C.

Find value.

Only a few page reads.

***

# 22. Fill Factor

DBA can leave extra free space.

Example:

```sql
CREATE INDEX IX_EmployeeID
ON Employees(EmployeeID)
WITH (FILLFACTOR = 80);
```

Meaning:

```text
Use only 80% page space.

Leave 20% empty.
```

Purpose:

```text
Reduce page splits.
```

***

# 23. Important Interview Questions

### What is a page?

```text
8 KB storage unit used by SQL Server.
```

***

### Why do indexes improve performance?

```text
They reduce page reads.
```

***

### What is a B-Tree?

```text
Balanced tree used to organize index entries.
```

***

### What is Root Page?

```text
Top-most page.
```

***

### What is Leaf Page?

```text
Bottom-most page.
```

***

### What is Intermediate Page?

```text
Page between Root and Leaf.
```

***

### What is Page Split?

```text
When a full page is divided into two pages.
```

***

### Why split 50-50?

```text
To leave free space for future inserts.
```

***

### Can indexes contain duplicate values?

```text
Yes.
SQL Server uses hidden row locators internally.
```

***

# Practice Exercises

## Exercise 1

Assume:

```text
Page Capacity = 4
```

Insert:

```text
10
20
30
40
50
```

Questions:

1.  When does split happen?
    
2.  What will Page A contain?
    
3.  What will Page B contain?
    
4.  What separator goes into Root?
    

***

## Exercise 2

Assume:

```text
Page Capacity = 5
```

Insert:

```text
HR
HR
HR
HR
HR
HR
```

Questions:

1.  When is Root created?
    
2.  How many leaf pages exist?
    
3.  Why are duplicate values allowed?
    

***

## Exercise 3

Draw the B-Tree after inserting:

```text
10
20
30
40
50
60
70
80
90
100
```

Assume:

```text
Page Capacity = 3
Root Capacity = 3 Separators
```

Track:

-   Leaf splits
    
-   Root updates
    
-   Root split
    
-   Intermediate page creation
    

This exercise will make the entire B-Tree growth process crystal clear.