## AdventureWorks 2019 SQL Server Project: Analyzing Data, Answering Business Questions and Implementing Solutions
![stephen-dawson-qwtCeJ5cLYs-unsplash](https://github.com/Melrowze/Microsoft-SQL-Server-Data-Analysis-Project-/assets/44920093/776e258c-2de0-4e2a-924e-02bb8a08c543)
## Description:
---
Welcome to my Microsoft SQL Server project, centered around the AdventureWorks 2019 database. Throughout this project, I employed a diverse range of SQL techniques and functions to extract valuable insights and address business challenges. 
<br>Here are some key highlights of my approach:

1. **Data Integration and Analysis:** Leveraging the JOIN operation, I seamlessly combined data from multiple tables within the AdventureWorks database, enabling comprehensive analysis and exploration.

2. **String Manipulation:** Through the use of string concatenation, I enhanced the presentation of data and facilitated a deeper understanding of relational information.

3. **Temporal Analysis:** By utilizing functions such as DATEDIFF and DATEPART, I conducted temporal analysis to measure time intervals, identify patterns, and gain insights into historical trends.

4. **Conditional Logic and Subqueries:** Through the implementation of CASE statements and subqueries, I crafted dynamic queries to address specific business scenarios, enabling tailored analysis and decision-making.

5. **View Creation:** To streamline data access and enhance efficiency, I created views which capture complex queries, providing a simplified and optimized interface for data retrieval.

6. **Price Policy Implementation:** I also implemented a new price policy within the database, reflecting innovative pricing strategies to meet evolving market demands.

Throughout the project, I employed critical thinking and problem-solving skills which are essential in identifying opportunities for data analysis, formulating queries to extract relevant information, and interpreting results to make informed business decisions.


## Business Requirements
---
Question 1

Retrieve information about the products with colour values except null, red, silver/black, white and list price between £75 and £750. Rename the column StandardCost to Price. Also, sort the results in descending order by list price.

--Answer

```sql
SELECT 
    ProductID, 
    Name, 
    Color, 
    StandardCost AS Price, 
    ListPrice
FROM 
    Production.Product
WHERE 
    Color NOT IN ('Red', 'Silver/Black', 'White') And Color IS NOT NULL
    AND ListPrice BETWEEN 75 AND 750
ORDER BY 
    ListPrice DESC;

--87 rows returned

```

Question 2
---
Find all the male employees born between 1962 to 1970 and with hire date greater than 2001 and female employees born between 1972 and 1975 and hire date between 2001 and 2002.

--Answer
```sql
SELECT 
    BusinessEntityID, 
    BirthDate, 
    Gender, 
    HireDate
FROM 
    HumanResources.Employee
WHERE 
    (Gender = 'M' 
    AND BirthDate BETWEEN '1962-01-01' AND '1970-12-31' 
    AND HireDate > '2001')
    
    OR 
    
    (Gender = 'F' 
    AND BirthDate BETWEEN '1972-01-01' AND '1975-12-31' 
    AND HireDate BETWEEN '2001-01-01' AND '2002-12-31');

--19 rows returned
```

Question 3
---
Create a list of 10 most expensive products that have a product number beginning with ‘BK’. Include only the product ID, Name and colour.

--Answer
```sql
SELECT TOP (10) 
    ProductID, 
    [Name], 
    Color
FROM 
    Production.Product
WHERE 
    ProductNumber LIKE 'BK%'
ORDER BY 
    ListPrice DESC;
```

Question 4
---
Create a list of all contact persons, where the first 4 characters of the last name are the same as the first four characters of the email address. Also, for all contacts whose first name and the last name begin with the same characters, create a new column called full name combining first name and the last name only. Also provide the length of the new column full name.

--Answer
```sql
SELECT 
    pp.BusinessEntityID, 
    pp.FirstName, 
    pp.LastName, 
    CONCAT(pp.FirstName, ' ', pp.LastName) AS FullName, 
    LEN(CONCAT(pp.FirstName, ' ', pp.LastName)) AS FullNameLength, 
    pe.EmailAddress
FROM 
    Person.Person pp 
INNER JOIN 
    Person.EmailAddress pe ON pp.BusinessEntityID = pe.BusinessEntityID
WHERE 
    SUBSTRING(pp.LastName, 1, 4) = SUBSTRING(pe.EmailAddress, 1, 4) OR 
    (SUBSTRING(pp.FirstName, 1, 1) = SUBSTRING(pp.LastName, 1, 1));

--1,072 rows returned
```

Question 5
---
Return all product subcategories that take an average of 3 days or longer to manufacture.

--Answer
```sql
SELECT 
    pp.ProductSubcategoryID, 
    ps.[Name], 
    DaysToManufacture
FROM 
    Production.Product pp
LEFT JOIN 
    Production.ProductSubcategory ps ON pp.ProductSubcategoryID = ps.ProductSubcategoryID
WHERE 
    DaysToManufacture >= 3;

--97 rows returned
```

Question 6
---
Create a list of product segmentation by defining criteria that places each item in a predefined segment as follows. If price gets less than £200 then low value. If price is between £201 and £750 then mid value. If between £750 and £1250 then mid to high value else higher value. Filter the results only for black, silver and red color products.

--Answer
```sql
SELECT 
    ProductID, 
    [Name], 
    ListPrice, 
    Color,
    CASE 
        WHEN ListPrice < 200 THEN 'Low value'
        WHEN ListPrice BETWEEN 201 AND 750 THEN 'Mid value'
        WHEN ListPrice BETWEEN 751 AND 1250 THEN 'Mid to high value'
        ELSE 'Higher value'
    END AS ProductSegmentation
FROM 
    Production.Product
WHERE 
    Color IN ('black', 'silver', 'red');

```
Question 7
---
How many Distinct Job title is present in the Employee table?

--Answer
```sql
SELECT DISTINCT 
    JobTitle
FROM 
    HumanResources.Employee;

--There are 67 distinct job titles in the Employee table
```

Question 8
---
Use employee table and calculate the ages of each employee at the time of hiring.

--Answer
```sql
SELECT 
    BusinessEntityID, 
    DATEDIFF(year, BirthDate, HireDate) AS AgeAtHiring
FROM 
    HumanResources.Employee;
```

Question 9
---
How many employees will be due a long service award in the next 5 years, if long service is 20 years?

--Answer
```sql
SELECT 
    BusinessEntityID, 
    CURRENT_TIMESTAMP AS Today, 
    DATEDIFF(year, HireDate, CURRENT_TIMESTAMP) AS YearsOfBeingEmployed
FROM 
    HumanResources.Employee
WHERE 
    DATEDIFF(year, HireDate, CURRENT_TIMESTAMP) = 15;

--As of today, 148 employees will be due a long service award in the next 5 years.
```

Question 10
---
How many more years does each employee have to work before reaching sentiment, if sentiment age is 65?

--Answer
```sql
SELECT 
    BusinessEntityID, 
    DATEDIFF(year, BirthDate, CURRENT_TIMESTAMP) AS Age, 
    65 - (DATEDIFF(year, BirthDate, CURRENT_TIMESTAMP)) AS NoOfYearsUntil65
FROM 
    HumanResources.Employee
ORDER BY 
    BusinessEntityID;
```

Question 11
---
Implement new price policy on the product table base on the colour of the item

--Answer
```sql
SELECT 
    ProductID,
    Name,
    Color,
    ListPrice AS OriginalPrice,
    CASE 
        WHEN Color = 'white' THEN ROUND(ListPrice * 1.08, 2)
        WHEN Color = 'yellow' THEN ROUND(ListPrice * 0.925, 2)
        WHEN Color = 'black' THEN ROUND(ListPrice * 1.172, 2)
        WHEN Color IN ('multi', 'silver', 'silver/black', 'blue') THEN ROUND(SQRT(ListPrice) * 2, 2)
        ELSE ListPrice
    END AS NewPrice,
    ROUND(CASE 
              WHEN Color = 'white' THEN ListPrice * 0.375 * 1.08
              WHEN Color = 'yellow' THEN ListPrice * 0.375 * 0.925
              WHEN Color = 'black' THEN ListPrice * 0.375 * 1.172
              WHEN Color IN ('multi', 'silver', 'silver/black', 'blue') THEN (SQRT(ListPrice) * 2) * 0.375
              ELSE ListPrice * 0.375
          END, 2) AS Commission
FROM 
    Production.Product;
```


Question 12
---
Print the information about all the Sales.Person and their sales quota. For every Sales person you should provide their FirstName, LastName, HireDate, SickLeaveHours and Region where they work.

--Answer
```sql
SELECT 
    sp.BusinessEntityID, 
    pp.FirstName, 
    pp.LastName, 
    he.HireDate, 
    he.SickLeaveHours, 
    sp.SalesQuota, 
    cr.[Name] AS Region
FROM 
    Sales.SalesPerson sp 
JOIN 
    HumanResources.Employee he ON sp.BusinessEntityID = he.BusinessEntityID 
JOIN 
    Person.Person pp ON he.BusinessEntityID = pp.BusinessEntityID 
LEFT JOIN 
    Sales.SalesTerritory st ON st.TerritoryID = sp.TerritoryID
LEFT JOIN 
    Person.CountryRegion cr ON st.CountryRegionCode = cr.CountryRegionCode;

--17 rows returned
```

Question 13
---
Using adventure works, write a query to extract the following information.
• Product name
• Product category name
• Product subcategory name
• Sales person
• Revenue
• Month of transaction
• Quarter of transaction
• Region

--Answer
```sql
SELECT 
    p.Name AS ProductName,
    pc.Name AS ProductCategoryName,
    psc.Name AS ProductSubcategoryName,
    CONCAT(per.FirstName, ' ', per.LastName) AS SalesPerson,
    sod.LineTotal AS Revenue,
    MONTH(soh.OrderDate) AS MonthOfTransaction,
    DATEPART(QUARTER, soh.OrderDate) AS QuarterOfTransaction,
    cr.[Name] AS Region
FROM 
    Sales.SalesOrderDetail sod
LEFT JOIN 
    Sales.SalesOrderHeader soh ON soh.SalesOrderID = sod.SalesOrderID
LEFT JOIN 
    Production.Product p ON sod.ProductID = p.ProductID
LEFT JOIN 
    Production.ProductSubcategory psc ON p.ProductSubcategoryID = psc.ProductSubcategoryID
LEFT JOIN 
    Production.ProductCategory pc ON psc.ProductCategoryID = pc.ProductCategoryID
LEFT JOIN 
    Sales.SalesPerson sp ON soh.SalesPersonID = sp.BusinessEntityID
LEFT JOIN 
    Sales.SalesTerritory st ON sp.TerritoryID = st.TerritoryID
LEFT JOIN 
    Person.Person per ON per.BusinessEntityID = sp.BusinessEntityID
LEFT JOIN 
    Person.CountryRegion cr ON st.CountryRegionCode = cr.CountryRegionCode;

--121,317 rows returned consistent with first table, SalesOrderDetail

```

Question 14
---
Display the information about the details of an order i.e. order number, order date, amount of order, which customer gives the order and which salesman works for that customer and how much commission he gets for an order.

--Answer
```sql
SELECT 
    soh.SalesOrderNumber AS OrderNumber,
    soh.OrderDate AS OrderDate,
    ROUND(soh.SubTotal, 2) AS AmountOfOrder,
    CONCAT(c.FirstName, ' ', c.LastName) AS CustomerName,
    soh.CustomerID,
    CONCAT(s.FirstName, ' ', s.LastName) AS SalesPersonName,
    soh.SalesPersonID,
    sp.CommissionPct AS CommissionPercentage,
    ROUND((soh.SubTotal * sp.CommissionPct), 2) AS CommissionAmount 
FROM 
    Sales.SalesOrderHeader soh
LEFT JOIN 
    Sales.SalesPerson sp ON soh.SalesPersonID = sp.BusinessEntityID
LEFT JOIN 
    Sales.Customer sc ON soh.CustomerID = sc.CustomerID 
LEFT JOIN 
    Person.Person c ON sc.PersonID = c.BusinessEntityID
LEFT JOIN 
    Person.Person s ON sp.BusinessEntityID = s.BusinessEntityID;

--31,465 rows returned
```
Question 15
---
For all the products calculate
A) Commission as 14.790% of standard cost,
B) Margin, if standard cost is increased or decreased as follows:
Black: +22%,
Red: -12%
Silver: +15%
Multi: +5%
White: Two times original cost divided by the square root of cost
For other colours, standard cost remains the same

--Answer
A)
```sql
SELECT 
    ProductID,
    Name AS ProductName,
    StandardCost,
    StandardCost * 0.1479 AS Commission
FROM 
    Production.Product;
```
B)
```sql
SELECT 
    ProductID, 
    Color, 
    StandardCost, 
    ListPrice,
    SUM(
        CASE 
            WHEN Color = 'Black' THEN Round((StandardCost + 0.22), 2)
            WHEN Color = 'Red' THEN Round((StandardCost - 0.12), 2)
            WHEN Color = 'Silver' THEN Round((StandardCost + 0.15), 2)
            WHEN Color = 'Multi' THEN Round((StandardCost + 0.05), 2)
            WHEN Color = 'White' THEN Round(((StandardCost * 2) / SQRT(StandardCost)), 2)
            ELSE StandardCost
        END
    ) AS NewStandardCost,
    (ListPrice - 
    SUM(
        CASE 
            WHEN Color = 'Black' THEN Round((StandardCost + 0.22), 2)
            WHEN Color = 'Red' THEN Round((StandardCost - 0.12), 2)
            WHEN Color = 'Silver' THEN Round((StandardCost + 0.15), 2)
            WHEN Color = 'Multi' THEN Round((StandardCost + 0.05), 2)
            WHEN Color = 'White' THEN Round(((StandardCost * 2) / SQRT(StandardCost)), 2)
            ELSE StandardCost
        END
    )) AS Margin
FROM 
    Production.Product
GROUP BY 
    ProductID, Color, StandardCost, ListPrice;
```

Question 16
---
Create a view to find out the top 5 most expensive products for each colour.

--Answer

A)
```sql
CREATE VIEW Top5MostExpensiveProductsPerColor AS
SELECT 
    ProductID,
    [Name] AS ProductName,
    Color,
    ListPrice,
    StandardCost,
    ROW_NUMBER() OVER (PARTITION BY Color ORDER BY ListPrice DESC) AS Rank
FROM 
    Production.Product;

GO

```
B)

Query to find out the top 5 most expensive products by color
```sql
SELECT 
    ProductID,
    ProductName,
    Color,
    ListPrice
FROM 
    Top5MostExpensiveProductsPerColor
WHERE 
    Rank <= 5;

```






