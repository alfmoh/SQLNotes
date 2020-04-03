`SELECT` statement consist of 4 components

	1. `Select` - Required
	2. `From` - Required
	3. `Where` - Optional
	4. `Order By` - Optional

```mssql
SELECT [Name]
FROM [HumanResources].[Department]
WHERE [Name] = 'Production'
ORDER BY [Name]
```



**Schemas**
	A name collection of database objects that form a namespace
Eg. `Person` and `HumanResources` are schemas.

```mssql
Person.Address
Person.BusinessEntity

HumanResources.JobCandidate
HumanResources.Shift
```

A schema is also a secure object so a D-B can grant explicit permissions to run those schemas

But `dbo.Customer` - `dbo` -> is a owner

Four parts naming conversion

​											1								2									3						4
​	`SELECT AdventureWorks2014.HumanResources.JobCandidate.Resume FROM HumanResources.JobCandidate`



**Aliasing**
	Giving another name to a column

`ProductionColumn` - Alias

```mssql
SELECT [Name] as ProductionColumn
FROM [HumanResources].[Department]
WHERE [Name] = 'Production'
ORDER BY [Name]
```



**Derived Column**
	A column that doesn't exist until we define one within the confines of our `SELECT` statement
`Purchase` - Derived Column
	**here, * is multiply not 'select all' **

```mssql
SELECT UnitPrice * OrderQty as Purchase
FROM Sales.SalesOrderDetail
```



**DATEDIFF** *function*
	Using the `DATEDIFF` function to get the ages of employees.

​	`YY` - Interested in the years
​	`BirthDate` - Starting date
​	`GetDate()` - Ending date
​	`Age` - Alias

```mssql
SELECT BirthDate,
		DATEDIFF(YY, BirthDate, GetDate()) as Age
FROM	HumanResources.Employee
```



**View**
	An optimized table-like, select-query-like object that is optimized and stored in the database but doesn't store any data.
	Views are often used to hide data. An organization can use view to restrict what the end user will see.
`vwReturnAllRows` - View

```mssql
CREATE VIEW vwReturnAllRows
AS
SELECT * FROM Customer
GO

SELECT * FROM vwReturnAllRows

/*
Output:
CustomerID	Lastname	Firstname	Email
1			Sheen       Charlie     sheen@example.com                                
2			John    	Doe         johndoe@abc.com     

*/
```

Alter views

```mssql
ALTER VIEW vwReturnAllRows
AS
SELECT Lastname, Firstname FROM Customer
GO

SELECT * FROM vwReturnAllRows

/*
Output:
Lastname	Firstname
Sheen       Charlie                             
John    	Doe         

*/
```

`SELECT * FROM vwReturnAllRows` returns only `Lastname` and `Firstname` and the `Email` and `CustomerID` columns have been hidden from the user.



**Operators**
	Examples: `=` `>=` 
		`<>` -Not equal to

```mssql
SELECT Name, ListPrice
FROM	Production.Product
WHERE ListPrice > 100
ORDER BY ListPrice desc
```

**LIKE**
	Like doesn't mean 'similar' in MSSQL, it means 'exactly like'. Works on both numbers and letters

```mssql
SELECT ListPrice
FROM Production.Product
WHERE ListPrice LIKE '20%' -- Where ListPrice that starts with '20'

SELECT ListPrice
FROM Production.Product
WHERE ListPrice LIKE '%64%' -- Where ListPrice contains '64'

SELECT ListPrice
FROM	Production.Product
WHERE ListPrice LIKE '_09%' -- Where ListPrice has 0 in the 2nd and 9 in 3rd place

SELECT ListPrice
FROM Production.Product
WHERE ListPrice LIKE '2______%' -- Where ListPrice starts with 2 and has at least 7 chars in length. Each _ represents a char, so 6(_)'s + 2 = 7 chars
	-- You can seperate the underscores with '%' . Example below returns the same result
SELECT ListPrice
FROM Production.Product
WHERE ListPrice LIKE '2_%_%_%_%_%_%'


SELECT * FROM HumanResources.Department
WHERE Name LIKE '%E' -- Where Name ends with 'E'


```



**Logical Comparisons**
	`AND` `OR` `NOT`

```mssql
SELECT * FROM HumanResources.Employee
WHERE MaritalStatus = 'M' AND VacationHours > 25

SELECT * FROM HumanResources.Employee
WHERE MaritalStatus = 'M' OR VacationHours > 25

SELECT * FROM HumanResources.Employee
WHERE NOT SalariedFlag = 1
```



**NULL**
	`IS NULL`
`NULL` is **not** equal to `NULL`

```mssql
SELECT * FROM Production.Product
WHERE Color IS NULL

SELECT * FROM Production.Product
WHERE Color IS NOT NULL

/*
Output: No Results
In SQL, NULL means nothing and can't be compared to anything
SO Title = NULL returns nothing.
*/
SELECT FirstName
FROM Person.Person
WHERE Title = NULL AND BusinessEntityID < 7
```

The SQL Server Database Engine uses a bitmap to track which columns in a row are null and which are not. The Bitmap contains a bit for each column with the bit set to 1 if the column is null, i.e, if the value is missing



**Extended Filtering** *using expressions*

```mssql
SELECT NationalIDNumber, LoginID
FROM HumanResources.Employee
WHERE BirthDate >= '1962-1-1' AND BirthDate <= '1985-12-31' -- Filtering using Expressions

-- Same as above using Between
SELECT NationalIDNumber, LoginID
FROM HumanResources.Employee
WHERE BirthDate BETWEEN '1962-1-1' AND '1985-12-31'


SELECT *
FROM Production.Product
WHERE ProductSubcategoryID IN (1,2,3) -- Where ID is in the list of (1,2,3)

-- Using IN in Sub-queries
SELECT * FROM Production.Product
WHERE ProductSubcategoryID IN
	(
		SELECT ProductSubcategoryID
		FROM Production.ProductSubcategory
		WHERE ProductCategoryID IN (1,2)
	)

```

Order of Operator processing

1. `NOT` 2. `AND` 3. `OR`

Filtering and Grouping Operations using Parenthesis

```mssql
/*
In this case, it doesn't really matter since AND is processed before OR anyway but in some cases, it may matter
*/
SELECT Name,
	ProductNumber,
	ListPrice,
	ProductSubCategoryID
FROM Production.Product
WHERE (ProductSubcategoryID = 1 AND ListPrice > 100)
OR    (ProductSubcategoryID = 2 AND ListPrice > 500)
```

