SQL FUNCTION

CREATE TABLE Departments_FUNCTION (
    DepartmentID INT PRIMARY KEY,
    DepartmentName VARCHAR(50) NOT NULL
);

INSERT INTO Departments_FUNCTION (DepartmentID, DepartmentName) VALUES
(1, 'HR'),
(2, 'IT'),
(3, 'Finance');

CREATE TABLE Employees_FUNCTION (
    EmployeeID INT PRIMARY KEY,
    FirstName VARCHAR(50) NOT NULL,       
    LastName VARCHAR(50) NOT NULL,
    DepartmentID INT,
    Salary DECIMAL(10, 2),
    HireDate DATE,
    FOREIGN KEY (DepartmentID) REFERENCES [dbo].[Departments_JOIN](DepartmentID)
);

INSERT INTO Employees_Function (EmployeeID, FirstName, LastName, DepartmentID, Salary, HireDate) VALUES
(1, 'John', 'Doe', 1, 50000.00, '2020-01-15'),
(2, 'Jane', 'Smith', 2, 60000.00, '2019-05-10'),
(3, 'Alice', 'Johnson', 1, 55000.00, '2021-03-22'),
(4, 'Bob', 'Brown', 3, 45000.00, '2018-11-30');



String Function

LEN(string)

LEFT(string, number_of_characters)
RIGHT(string, number_of_characters)
SUBSTRING(string, start_position, length)

CHARINDEX(substring, string, [start_position]) --[] means optional
PATINDEX(pattern, string)

REPLACE(string, old_substring, new_substring)
STUFF(string, start_position, length, new_string) --4 arguments needed

REVERSE(string)

UPPER(string)
LOWER(string)
LTRIM(string) --Leading spaces
RTRIM(string) --trailing spaces
TRIM(string)  -- both leading and trailing spaces

CONCAT(string1, string2, ...)
CONCAT_WS(separator, string1, string2, ...)

QUOTENAME(string, [quote_character])
SPACE(number_of_spaces)
REPLICATE(string, number_of_times)

Ex:

  SELECT FirstName,LEN(FirstName) FROM Employees;
  SELECT FirstName,LEFT(FirstName,2) FROM Employees;
  SELECT LastName,Right(LastName,2) FROM Employees;
  SELECT FirstName,SUBSTRING(FirstName,2,1) FROM Employees;
  SELECT FirstName,CHARINDEX('HN',Firstname,0) FROM Employees;
  SELECT FirstName,PATINDEX('%n%',FirstName) FROM Employees;
  SELECT FirstName,REPLACE(FirstName,'hn','HN') FROM Employees;
  SELECT FirstName,STUFF(FirstName,3,0,'NH') FROM Employees;
  SELECT FirstName,REVERSE(FirstName) FROM Employees;
  SELECT FirstName,UPPER(FirstName) FROM Employees;
  SELECT FirstName,Lower(FirstName) FROM Employees;
  SELECT FirstName,LTRIM('     Hello') FROM Employees;
  SELECT LastName,RTRIM('Hello    ') FROM Employees;
  SELECT FirstName,TRIM('   Hello   ') FROM Employees;
  SELECT FirstName,TRIM('x' FROM 'xxxHelloxxxx') FROM Employees;
  SELECT CONCAT(FirstName,' ',LastName) FROM Employees; --1) NULL CAN BE IGNORED 2)Numbers also concatenated
  SELECT FirstName+12345+LastName FROM Employees; -- NULL CAN'T BE IGNORED 
  SELECT CONCAT_WS('-',FirstName,LastName) FROM Employees;
  SELECT FirstName+SPACE(5)+LastName FROM Employees;
  SELECT REPLICATE(FirstName,5) From Employees;
  SELECT QUOTENAME(FirstName) AS QuotedName FROM Employees;--default [], we can pass '',"",<,,(),{}
