# Coding Guidelines for MSSQL #

## Use "Pascal" notation for SQL server Objects Like Tables, Views, Stored Procedures ##

Exemplo

```SQL
Booking
BookingItem
GetBooking
```

## If you have big subset of table group than it makes sense to give prefix for this table group ##

```SQL
Booking
BookingItem
BookingItemTraveler
BookingItemStatement
```

## Use following naming convention for Stored Procedure. < action type >< table name > Where action is: Get, Delete, Update, Insert and Save ## 

```SQL
GetBooking
SaveBooking
```

## Never use triggers ##

## Indexes : IX_< tablename >_< columns separated by_ > ## 

```SQL
IX_UserDetails_UserID 
```

## Primary Key : PK_< tablename > ##

```SQL
PK_UserDetails 
PK_ Emails 
```

## Foreign Key : FK_< tablename_1 >_< tablename_2 > ##

```SQL
FK_UserDetails_Emails 
```

## Default: DF_< table name >_< column name > ## 

```SQL
DF_ UserDetails _UserName
```

## Normalize Database structure based on 3rd Normalization Form. Normalization is the process of designing a data model to efficiently store data in a database ##

```SQL
SQL SERVER – Rules of Third Normal Form and Normalization Advantage – 3NF
I always ask question about Third Normal Form in interviews I take.
Q. What is Third Normal Form and what is its advantage?
A. Third Normal Form (3NF) is most preferable normal form in RDBMS. Normalization is the process of designing a data model to efficiently store data in a database. The rules of 3NF are mentioned here

•	Make a separate table for each set of related attributes, and give each table a primary key.
•	If an attribute depends on only part of a multi-valued key, remove it to a separate table
•	If attributes do not contribute to a description of the key, remove them to a separate table.
Normalization is very close to concept of object oriented schema’s and it stores one data at only one place by removing all the redundant data. It also helps to draw the schema easier. Normalization comes at the cost of performance.
```

## Avoid use of SELECT * in SQL queries. Instead practice writing required column names after SELECT statement ##

```SQL
SELECT Username, Password 
FROM UserDetails 
```

## Avoid using temporary tables and derived tables as it uses more disks I/O. Instead use CTE (Common Table Expression); its scope is limited to the next statement in SQL query ##

```SQL
SQL SERVER – 2005 T-SQL Paging Query Technique Comparison (OVER and ROW_NUMBER()) – CTE vs. Derived Table
I have received few emails and comments about my post SQL SERVER – T-SQL Paging Query Technique Comparison – SQL 2000 vs SQL 2005. The main question was is this can be done using CTE? Absolutely! What about Performance? It is same! Please refer above mentioned article for history of paging.

SQL 2005 Paging Method Using Derived Table
USE AdventureWorks
GO
DECLARE @StartRow INT
DECLARE @EndRow INT
SET @StartRow = 120
SET @EndRow = 140
SELECT FirstName, LastName, EmailAddress
FROM (
SELECT PC.FirstName, PC.LastName, PC.EmailAddress,
ROW_NUMBER() OVER(
ORDER BY PC.FirstName, PC.LastName,PC.ContactID) AS RowNumber
FROM Person.Contact PC) PersonContact
WHERE RowNumber > @StartRow
AND RowNumber < @EndRow
ORDER BY FirstName, LastName, EmailAddress
GO

SQL 2005 Paging Method Using CTE
USE AdventureWorks
GO
DECLARE @StartRow INT
DECLARE @EndRow INT
SET @StartRow = 120;
SET @EndRow = 140;
WITH PersonContact AS
(
SELECT PC.FirstName, PC.LastName, PC.EmailAddress,
ROW_NUMBER() OVER(
ORDER BY PC.FirstName, PC.LastName,PC.ContactID) AS RowNumber
FROM Person.Contact PC)
SELECT FirstName, LastName, EmailAddress
FROM PersonContact
WHERE RowNumber > @StartRow
AND RowNumber < @EndRow
ORDER BY FirstName, LastName, EmailAddress
GO

Following Image of Execution Plan displays that the performance for both of them is same with regarding to each other in one batch. This MAY NOT be true when there is complex query in issue. For most of the time, it is will be same.
```

## Use SET NOCOUNT ON at the beginning of SQL Batches, Stored Procedures and Triggers. This improves the performance of Stored Procedure ##

```SQL
SQL SERVER – Simple Cursor to Select Tables in Database with Static Prefix and Date Created
Following cursor query runs through database and find all the table with certain prefixed (‘b_’,’delete_’). It also checks if the Table is more than certain days old or created before certain days, it will delete it. We can have any other opertation on that table like delete, print or reindex.

SET NOCOUNT ON
DECLARE @lcl_name VARCHAR(100)
DECLARE cur_name CURSOR FOR
SELECT name
FROM sysobjects
WHERE type = 'U'
AND crdate <= DATEADD(m,-1,GETDATE())
AND name LIKE 'b_%'
OPEN cur_name
FETCH NEXT FROM cur_name INTO @lcl_name
WHILE @@Fetch_status = 0
BEGIN
SELECT @lcl_name = 'sp_depends ' +@lcl_name
PRINT @lcl_name
--  EXEC (@lcl_name )
FETCH NEXT FROM cur_name INTO @lcl_name
END
CLOSE cur_name
DEALLOCATE cur_name
SET NOCOUNT OFF
```

## Properly format SQL queries using indents ##

```SQL
SELECT Username, Password FROM UserDetails ud INNER JOIN Employee e ON e.EmpID = ud.UserID 
Example: Correct Format 
SELECT Username, Password 
FROM UserDetails ud 
INNER JOIN Employee e ON e.EmpID = ud.UserID 
```

## Practice writing Upper Case for all SQL keywords ##

```SQL
SELECT, UPDATE, INSERT, WHERE, INNER JOIN, AND, OR, LIKE.
```

## There must be PRIMARY KEY in all the tables of database with column name < table > Id. It is common practice to use Primary Key as IDENTITY column ##

## If “One Table” references “Another Table” than the column name used in reference should use the following rule : ##

```SQL
Column of Another Table : < OneTableName > Id 

If User table references Employee table than the column name used in reference should be UserID where User is table name and ID primary column of User table and UserID is reference column of Employee table. 
```

## Columns with Default value constraint should not allow NULLs ##

## Practice using PRIMARY key in WHERE condition of UPDATE or DELETE statements as this will avoid error possibilities ##

## Always create stored procedure in same database where its relevant table exists otherwise it will reduce network performance ##

## Avoid server‐side Cursors as much as possible, instead use SELECT statement. If you need to use cursor then replace it with WHILE loop (or read next suggestion) ##

## Instead of using LOOP to insert data from Table B to Table A, try to use SELECT statement with INSERT statement ## 

```SQL
INSERT INTO Table A (column1, column2) 
SELECT column1, column2 
FROM Table B 
WHERE ….
```

## Don’t use spaces within the name of database objects ##

```SQL
This may create issues with front‐end data access tools and applications. If you need spaces in your database object name then will accessing it surround the database object name with square brackets. 
```

## Do not use reserved words for naming database objects, as that can lead to some unpredictable situations ##

## Practice writing comments in stored procedures, triggers and SQL batches, whenever something is not very obvious, as it won’t impact the performance ##

## Do not use wild card characters at the beginning of word while search using LIKE keyword as it results in Index scan ##

## Indent code for better readability ##

## While using JOINs in your SQL query always prefix column name with the table name. If additionally require then prefix Table name with ServerName, DatabaseName, DatabaseOwner ##

## Default constraint must be defined at the column level. All other constraints must be defined at the table level ##

## Avoid using rules of database objects instead use constraints ##

## Do not use the RECOMPILE option for Stored Procedure as it reduces the performance ##

## Always put the DECLARE statements at the starting of the code in the stored procedure. This will make the query optimizer to reuse query plans ##

## Put the SET statements in beginning (after DECLARE) before executing code in the stored procedure ## 

## When working with branch conditions or complicated expressions, use parenthesis to increase readability ##

```SQL
IF ((SELECT 1 
FROM TableName 
WHERE 1=2) ISNULL) 
```

## If there is no need of resultset then use syntax that doesn’t return a resultset ##

```SQL
IF EXISTS (SELECT 1 
FROM UserDetails 
WHERE UserID = 50) 
Rather than, 

IF EXISTS (SELECT COUNT (UserID) 
FROM UserDetails 
WHERE UserID = 50) 
```
## Use graphical execution plan in Query Analyzer or SHOWPLAN_TEXT or SHOWPLAN_ALL commands to analyze SQL queries. Your queries should do an “Index Seek” instead of an “Index Scan” or a “Table Scan” ##

## Do not prefix stored procedure names with “SP_”, as “SP_” is reserved for system stored procedures ##

```SQL
SP< App Name >_ [ < Group Name > _ ] < Action > < table / logical instance > 
```

## Do not query / manipulate the data directly in your front end application, instead create stored procedures, and let your applications to access stored procedure ## 

## Avoid using ntext, text, and image data types in new development work. Use nvarchar (max), varchar (max), and varbinary (max) instead ##

## Do not store binary or image files (Binary Large Objects or BLOBs) inside the database. Instead, store the path to the binary or image file in the database and use that as a pointer to the actual file stored on a server ##

## Use the CHAR datatype for a non‐nullable column, as it will be the fixed length column, NULL value will also block the defined bytes ##

## Avoid using dynamic SQL statements ##

```SQL
Dynamic SQL tends to be slower than static SQL, as SQL Server generate execution plan every time at runtime. 
```

## Minimize the use of Nulls. Because they incur more complexity in queries and updates. ISNULL and COALESCE functions are helpful in dealing with NULL values ##

## Use Unicode datatypes, like NCHAR, NVARCHAR or NTEXT if it needed, as they use twice as much space as non‐Unicode datatypes ##

## Always use column list in INSERT statements of SQL queries. This will avoid problem when table structure changes ## 

## Perform all referential integrity checks and data validations using constraints instead of triggers, as they are faster. Limit the use of triggers only for auditing, custom tasks, and validations that cannot be performed using constraints ##

## Always access tables in the same order in all stored procedure and triggers consistently. This will avoid deadlocks ##

## Do not call functions repeatedly in stored procedures, triggers, functions and batches, instead call the function once and store the result in a variable, for later use ##

## With Begin and End Transaction always use global variable @@ERROR, immediately after data manipulation statements (INSERT/UPDATE/DELETE), so that if there is an Error the transaction can be rollback ##

## Excessive usage of GOTO can lead to hard‐to‐read and understand code ##

## Do not use column numbers in the ORDER BY clause; it will reduce the readability of SQL query ##

```SQL
Wrong Statement 
SELECT UserID, UserName, Password 
FROM UserDetails 
ORDER BY 2 

Correct Statement 
SELECT UserID, UserName, Password 
FROM UserDetails 
ORDER BY UserName 
```

## Effective indexes are one of the best ways to improve performance in a database application ##

## BULK INSERT command helps to import a data file into a database table or view in a user‐specified format ##

## Use Policy Management to make or define and enforce your own policies fro configuring and managing SQL Server across the enterprise, eg. Policy that Prefixes for stored procedures should be sp ##

## When some particular records are retrieved frequently, apply Filtered Index to improve query performace, faster retrieval and reduce index maintenance costs ## 

## Using the NOLOCK query optimizer hint is considered good practice in order to improve concurrency on a busy system ##

## EXCEPT or NOT EXIST clause can be used in place of LEFT JOIN or NOT IN for better peformance ##

```SQL
SELECT EmpNo, EmpName 
FROM EmployeeRecord 
WHERE Salary > 1000 AND Salary 
NOT IN (SELECT Salary 
FROM EmployeeRecord 
WHERE Salary > 2000); 
(Recomended) 
SELECT EmpNo, EmpName 
FROM EmployeeRecord 
WHERE Salery > 1000 
EXCEPT 
SELECT EmpNo, EmpName 
FROM EmployeeRecord 
WHERE Salery > 2000 
ORDER BY EmpName; 
```
