## 1. String Functions

String functions evaluate an input string value and return a string or numeric value.

### Syntax Reference

-   `LEN(string)`: Returns the number of characters, _excluding trailing spaces_ but _including leading spaces_.
    
-   `LEFT(string, number_of_characters)`: Extracts a specified number of characters from the left side.
    
-   `RIGHT(string, number_of_characters)`: Extracts a specified number of characters from the right side.
    
-   `SUBSTRING(string, start_position, length)`: Extracts a substring starting at a 1-based position.
    
-   `CHARINDEX(substring, string, [start_position])`: Returns the starting position of a substring (returns 0 if not found).
    
-   `PATINDEX('%pattern%', string)`: Returns the starting position of the first occurrence of a pattern wildcards.
    
-   `REPLACE(string, old_substring, new_substring)`: Replaces all occurrences of a substring within a string.
    
-   `STUFF(string, start_position, length, new_string)`: Deletes a specified length of characters and inserts a new string at the start position.
    
-   `REVERSE(string)`: Reverses the character order of a string.
    
-   `UPPER(string)`: Converts text to uppercase.
    
-   `LOWER(string)`: Converts text to lowercase.
    
-   `LTRIM(string)`: Removes leading spaces.
    
-   `RTRIM(string)`: Removes trailing spaces.
    
-   `TRIM([characters FROM] string)`: Removes leading and trailing spaces (or specified characters in modern MSSQL versions).
    
-   `CONCAT(string1, string2, ...)`: Joins multiple strings. Automatically ignores `NULL` tokens (converts them to empty strings).
    
-   `CONCAT_WS(separator, string1, string2, ...)`: Concatenates strings with a specified separator. Ignores `NULL` values.
    
-   `QUOTENAME(string, [quote_character])`: Adds delimiters (default `[]`) to make an input string a valid SQL Server delimited identifier.
    
-   `SPACE(number_of_spaces)`: Returns a string of repeated spaces.
    
-   `REPLICATE(string, number_of_times)`: Repeats a string a specified number of times.
    

### Practical Code Examples

SQL

```
-- Length and Trimming
SELECT FirstName, LEN(FirstName) AS LengthFromNotes FROM Employees_FUNCTION;
SELECT FirstName, LEFT(FirstName, 2) AS LeftTwo FROM Employees_FUNCTION;
SELECT LastName, RIGHT(LastName, 2) AS RightTwo FROM Employees_FUNCTION;
SELECT FirstName, SUBSTRING(FirstName, 2, 1) AS SubStr FROM Employees_FUNCTION;

-- Searching and Indexes
SELECT FirstName, CHARINDEX('HN', FirstName, 0) AS MatchPos FROM Employees_FUNCTION;
SELECT FirstName, PATINDEX('%n%', FirstName) AS PatternPos FROM Employees_FUNCTION;

-- String Manipulation
SELECT FirstName, REPLACE(FirstName, 'hn', 'HN') AS Replaced FROM Employees_FUNCTION;
SELECT FirstName, STUFF(FirstName, 3, 0, 'NH') AS Stuffed FROM Employees_FUNCTION;
SELECT FirstName, REVERSE(FirstName) AS RevName FROM Employees_FUNCTION;
SELECT FirstName, UPPER(FirstName) AS UpName FROM Employees_FUNCTION;
SELECT FirstName, LOWER(FirstName) AS LowName FROM Employees_FUNCTION;

-- Whitespace Handlers
SELECT FirstName, LTRIM(' Hello') AS LTrimmed FROM Employees_FUNCTION;
SELECT LastName, RTRIM('Hello ') AS RTrimmed FROM Employees_FUNCTION;
SELECT FirstName, TRIM(' Hello ') AS Trimmed FROM Employees_FUNCTION;
SELECT FirstName, TRIM('x' FROM 'xxxHelloxxxx') AS CleanedCustom FROM Employees_FUNCTION;

-- Concatenation Methods (The NULL behavior differences)
SELECT CONCAT(FirstName, ' ', LastName) AS FullNameConcat FROM Employees_FUNCTION; -- NULLs treated as empty string
SELECT FirstName + 12345 + LastName AS NativePlus FROM Employees_FUNCTION;          -- Fails or yields NULL if components are NULL
SELECT CONCAT_WS('-', FirstName, LastName) AS WithSeparator FROM Employees_FUNCTION;
SELECT FirstName + SPACE(5) + LastName AS SpacedName FROM Employees_FUNCTION;
SELECT REPLICATE(FirstName, 5) AS Multiplexed FROM Employees_FUNCTION;
SELECT QUOTENAME(FirstName) AS QuotedName FROM Employees_FUNCTION; -- Generates [John], [Jane], etc.
```

## 2. Numeric & Mathematical Functions

Used for calculations, processing floating-point errors, geometric mappings, and deterministic rounding boundaries.

### Syntax Reference

-   `ABS(numeric_expression)`: Returns the absolute, positive magnitude of a number.
    
-   `CEILING(numeric_expression)`: Returns the smallest integer greater than or equal to the expression.
    
-   `FLOOR(numeric_expression)`: Returns the largest integer less than or equal to the expression.
    
-   `ROUND(numeric_expression, length, [function])`: Rounds to a specified decimal length. If `function` parameter is non-zero (e.g., 1), it _truncates_ instead of rounds.
    
-   `POWER(numeric_expression, power)`: Raises an expression to a specified power.
    
-   `SQRT(float_expression)`: Returns the square root of a floating-point value.
    
-   `RAND([seed])`: Returns a pseudo-random `float` value from 0 through 1. Provide a static seed for consistent evaluation.
    
-   `SIGN(numeric_expression)`: Returns +1 for positive numbers, -1 for negative numbers, and 0 for zero.
    

### Practical Code Examples

SQL

```
-- Basic Rounding Mechanics vs Truncation
SELECT 
    SaleAmount,
    ROUND(SaleAmount, 1) AS RoundedOneDecimal,
    ROUND(SaleAmount, 1, 1) AS TruncatedOneDecimal, -- Notice 3rd argument usage
    CEILING(SaleAmount) AS CeilValue,
    FLOOR(SaleAmount) AS FloorValue
FROM Sales_FUNCTION;

-- Algebraic & Random Transformations
SELECT 
    Salary,
    SQRT(Salary) AS SquareRootSalary,
    POWER(Salary, 2) AS ExponentialSalary,
    ABS(Salary * -1) AS StructuralPositive,
    SIGN(Salary) AS SalarySignIndicator,
    RAND() AS DynamicRuntimeSeed
FROM Employees_FUNCTION;
```

## 3. Date & Time Functions

Crucial for analytical queries, timestamp isolation, temporal comparisons, and generation of calendar attributes.

### Syntax Reference

-   `GETDATE()`: Returns current system database timestamp as a `DATETIME` value.
    
-   `SYSDATETIME()`: Returns highly precise system timestamp as a `DATETIME2(7)` value.
    
-   `SYSDATETIMEOFFSET()`: Returns current system time including the operating system's timezone offset from UTC.
    
-   `DATEADD(datepart, number, date)`: Appends an explicit interval step integer to a target date component.
    
-   `DATEDIFF(datepart, startdate, enddate)`: Computes bounded crossing limits between two dates based on a specified time interval (e.g. crossing year lines, month lines).
    
-   `DATENAME(datepart, date)`: Output string containing the descriptive name of a specified date segment (e.g., 'January', 'Monday').
    
-   `DATEPART(datepart, date)`: Output an integer corresponding directly to the requested numeric date metric.
    
-   `EOMONTH(start_date, [month_to_add])`: Yields the exact final calendar date of the evaluated target month.
    

### Practical Code Examples

SQL

```
-- Baseline Current Clock Engine Checks
SELECT 
    GETDATE() AS TraditionalDateTime,
    SYSDATETIME() AS HighPrecisionDateTime2,
    SYSDATETIMEOFFSET() AS TimeZoneAwareOffset;

-- Operational Multi-Unit Calculations
SELECT 
    FirstName,
    HireDate,
    DATEDIFF(YEAR, HireDate, GETDATE()) AS ContinuousYearsEmployed,
    DATEDIFF(MONTH, HireDate, GETDATE()) AS TotalMonthsEmployed,
    DATEADD(DAY, 90, HireDate) AS EndOfProbationPeriod,
    EOMONTH(HireDate) AS FinalDayOfHiringMonth,
    EOMONTH(HireDate, 1) AS NextMonthClosingDay
FROM Employees_FUNCTION;

-- Calendar Extraction Matrix
SELECT 
    SaleDate,
    DATEPART(YEAR, SaleDate) AS ChronoYearInt,
    DATEPART(MONTH, SaleDate) AS ChronoMonthInt,
    DATENAME(MONTH, SaleDate) AS LiteralMonthString,
    DATENAME(WEEKDAY, SaleDate) AS LiteralWeekdayString
FROM Sales_FUNCTION;
```

## 4. Aggregate Functions

Aggregates process a vertical slice of values within a table column, computing a scalar summary value. They are used in conjunction with a `GROUP BY` clause.

### Syntax Reference

-   `COUNT(expression | *)`: Evaluates row presence counts. `COUNT(*)` retains NULL-driven structural rows; `COUNT(ColumnName)` filters out null records.
    
-   `SUM(column)`: Compiles summary arithmetic totals across active vertical distributions.
    
-   `AVG(column)`: Identifies mean values. _Warning: Skips null positions entirely without shifting denominator parameters unless explicitly neutralized._
    
-   `MIN(column)`: Captures minimum localized elements. Works on numbers, strings, and dates.
    
-   `MAX(column)`: Captures peak localized elements. Works on numbers, strings, and dates.
    
-   `STRING_AGG(expression, separator) WITHIN GROUP (ORDER BY expression)`: Concatenates row values into a single string, separated by a specified delimiter.
    

### Practical Code Examples

SQL

```
-- Comprehensive Base Metric Summary Aggregation
SELECT 
    COUNT(*) AS GrandTotalRowCount,
    COUNT(DepartmentID) AS NonNullDepartmentRows,
    SUM(Salary) AS TotalPayrollExpenditure,
    AVG(Salary) AS MeanSalaryCalculation,
    MIN(Salary) AS FloorSalaryLevel,
    MAX(Salary) AS PeakSalaryLevel
FROM Employees_FUNCTION;

-- Grouped Aggregates with Structural String Synthesis
SELECT 
    DepartmentID,
    COUNT(EmployeeID) AS StaffHeadcount,
    SUM(Salary) AS DepartmentalPayroll,
    STRING_AGG(TRIM(FirstName), ', ') WITHIN GROUP (ORDER BY Salary DESC) AS SortedStaffList
FROM Employees_FUNCTION
WHERE DepartmentID IS NOT NULL
GROUP BY DepartmentID;
```

## 5. Logical, Conditional & Null-Handling Functions

These functions allow developers to handle missing data (`NULL`) or embed processing rules directly within data pipelines.

### Syntax Reference

-   `ISNULL(check_expression, replacement_value)`: Validates if an element contains a null value. If so, swaps it with a specified replacement value. Highly performant but constrained to output the exact data type of the check expression.
    
-   `COALESCE(expression1, expression2, ... expression_N)`: Evaluates a series of expressions from left to right and returns the first non-null alternative value. Evaluated via the standard standard SQL engine.
    
-   `IIF(boolean_expression, true_value, false_value)`: Shorthand construct for a basic `CASE WHEN` logical assessment.
    
-   `NULLIF(expression1, expression2)`: Returns `NULL` if two expressions match. Otherwise, outputs the first expression. Useful for preventing divide-by-zero errors.
    

### Practical Code Examples

SQL

```
-- Compensation Calculation handling NULLs
SELECT 
    FirstName,
    Salary,
    Commission,
    ISNULL(Commission, 0.00) AS StandardizedComm,
    Salary + ISNULL(Commission, 0.00) AS EffectiveGrossPay,
    COALESCE(Commission, Salary * 0.05, 0.00) AS FallbackCompensationStrategy
FROM Employees_FUNCTION;

-- Shorthand Evaluation Logic
SELECT 
    FirstName,
    Salary,
    IIF(Salary >= 60000.00, 'Tier-1 Executive', 'Tier-2 Associate') AS InternalSalaryClassification
FROM Employees_FUNCTION;

-- Guarding Core Computations against Runtime Zero Failures
SELECT 
    SaleID,
    SaleAmount,
    -- Simulating division safety check via NULLIF
    SaleAmount / NULLIF(0, 0) AS ZeroProtectedColumn
FROM Sales_FUNCTION;
```

## 6. Data Conversion & Type Validation Functions

These functions handle casting between conflicting structural types (e.g., changing string text arrays into operational calendar variables).

### Syntax Reference

-   `CAST(expression AS target_data_type [(length)])`: Standard SQL notation used to cast an explicit variable type across target domains.
    
-   `CONVERT(target_data_type [(length)], expression, [style])`: Specific to SQL Server. Contains a custom `style` integer parameter for explicit date/time string serialization formatting.
    
-   `TRY_CAST(expression AS target_data_type)` / `TRY_CONVERT(...)`: Safe conversion functions that return `NULL` instead of generating a query-terminating runtime crash if a text format cannot be cast to the target data type.
    
-   `ISNUMERIC(expression)`: Inspects if an evaluated structural character element can resolve into a valid number. Returns 1 for numeric formats, and 0 for failures.
    

### Practical Code Examples

SQL

```
-- Explicit Conversion Transformations with Custom Formatting Styling
SELECT 
    FirstName,
    CAST(Salary AS VARCHAR(20)) + ' USD' AS TextualSalaryString,
    CONVERT(VARCHAR(30), HireDate, 101) AS USStandardFormat,    -- MM/DD/YYYY
    CONVERT(VARCHAR(30), HireDate, 103) AS EuroStandardFormat,  -- DD/MM/YYYY
    CONVERT(VARCHAR(30), HireDate, 112) AS ISOStandardFormat    -- YYYYMMDD
FROM Employees_FUNCTION;

-- Resilient Fault-Tolerant Conversion Architecture Examples
SELECT 
    TRY_CAST('2026-06-16' AS DATE) AS ValidatedParsedDate,
    TRY_CAST('CorruptedTextDataString' AS DECIMAL(10,2)) AS HandledGracefulFailure,
    ISNUMERIC('1250.45') AS IsNumericCheckTrue,
    ISNUMERIC('EmployeeCode_XYZ') AS IsNumericCheckFalse;
```

## 7. Advanced Window Functions (Analytical Engines)

Window functions perform calculations across a set of table rows that are related to the current row, without collapsing the individual source records. They require the `OVER()` clause.

### Syntax Reference

-   `ROW_NUMBER()`: Assigns a unique sequential integer to rows within a partition, starting at 1 for the first row in each partition.
    
-   `RANK()`: Assigns a sequential rank integer starting at 1. If values match, they share the same rank, and the sequence skips the next rank values (e.g., 1, 2, 2, 4).
    
-   `DENSE_RANK()`: Assigns a sequential rank integer starting at 1. If values match, they share the same rank, but the sequence does _not_ skip any rank numbers (e.g., 1, 2, 2, 3).
    
-   `LEAD(column, [offset], [default])`: Provides access to a row at a given physical offset _after_ the current row within the same result set.
    
-   `LAG(column, [offset], [default])`: Provides access to a row at a given physical offset _before_ the current row within the same result set.
    

### Practical Code Examples

SQL

```
-- Competitive Salary Evaluation Over Row Scopes
SELECT 
    DepartmentID,
    FirstName,
    Salary,
    ROW_NUMBER() OVER(PARTITION BY DepartmentID ORDER BY Salary DESC) AS RowNumIndex,
    RANK() OVER(PARTITION BY DepartmentID ORDER BY Salary DESC) AS PeerRankGapScore,
    DENSE_RANK() OVER(PARTITION BY DepartmentID ORDER BY Salary DESC) AS PeerDenseRankNoGap
FROM Employees_FUNCTION
WHERE DepartmentID IS NOT NULL;

-- Chronological Sales Shift Analysis Tracking
SELECT 
    SaleID,
    EmployeeID,
    SaleAmount,
    SaleDate,
    LAG(SaleAmount, 1, 0.00) OVER(PARTITION BY EmployeeID ORDER BY SaleDate ASC) AS PreviousEmployeeSaleAmount,
    LEAD(SaleAmount, 1, NULL) OVER(PARTITION BY EmployeeID ORDER BY SaleDate ASC) AS NextEmployeeSaleAmount
FROM Sales_FUNCTION;
```

"""
# Pracite Steps :
CREATE TABLE Employees_FUNCTION
(
    EmployeeID INT IDENTITY(1,1) PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    DepartmentID INT,
    Salary DECIMAL(10,2),
    Commission DECIMAL(10,2) NULL,
    HireDate DATE
);

INSERT INTO Employees_FUNCTION
(
    FirstName,
    LastName,
    DepartmentID,
    Salary,
    Commission,
    HireDate
)
VALUES
('John','Smith',1,50000,5000,'2020-01-15'),
('Jane','Williams',1,70000,NULL,'2018-05-20'),
('Robert','Brown',2,60000,3000,'2019-08-10'),
('Emily','Davis',2,75000,NULL,'2017-11-01'),
('Michael','Wilson',3,55000,2500,'2021-03-18'),
('Sarah','Taylor',3,65000,NULL,'2016-07-25'),
('David','Anderson',1,80000,8000,'2015-12-12'),
('Lisa','Thomas',2,62000,NULL,'2022-02-14'),
('James','Jackson',3,58000,1500,'2020-09-05'),
('Linda','White',2,90000,10000,'2014-04-08');

CREATE TABLE Sales_FUNCTION
(
    SaleID INT IDENTITY(1,1) PRIMARY KEY,
    EmployeeID INT,
    SaleAmount DECIMAL(10,2),
    SaleDate DATE
);

INSERT INTO Sales_FUNCTION
(
    EmployeeID,
    SaleAmount,
    SaleDate
)
VALUES
(1,2500,'2025-01-10'),
(1,3000,'2025-02-15'),
(1,4500,'2025-03-12'),

(2,5000,'2025-01-20'),
(2,5200,'2025-02-18'),

(3,1500,'2025-01-08'),
(3,1800,'2025-02-08'),

(4,7000,'2025-01-25'),
(4,6500,'2025-02-25'),

(5,3500,'2025-01-11'),
(5,4000,'2025-02-11');


