# Practice
SQL Code Practice
-- Practicin on AdventureWorks2019

/*
--AdventureWorks

--This data is based on Microsoft's AdventureWorks database.
--*/



--/*
--#1) AdventureWorks Easy Questions

--Q. Show the first name and the email address of customer with CompanyName 'Bike World'
--*/
--SELECT firstname, emailaddress
--FROM Customer
--WHERE companyname = 'Bike World';


--/*
--#2) AdventureWorks Easy Questions

--Q. Show the CompanyName for all customers with an address in City 'Dallas'.
--*/
--SELECT companyname
--FROM Customer c JOIN CustomerAddress ca ON c.customerid = ca.customerid
--JOIN Address a ON ca.addressid = a.addressid
--WHERE city = 'Dallas';


--/*
--#3) AdventureWorks Easy Questions

--Q. How many items with ListPrice more than $1000 have been sold?
--*/
--SELECT COUNT(salesorderid) Total
--FROM SalesOrderDetail s JOIN Product p ON s.productid = p.productid
--WHERE listprice > 1000;


--/*
--#4) AdventureWorks Easy Questions

--Q. Give the CompanyName of those customers with orders over $100000.
--   Include the subtotal plus tax plus freight.
--*/
--SELECT companyname
--FROM Customer c JOIN SalesOrderHeader sh ON c.customerid = sh.customerid
--WHERE subtotal + taxamt + freight > 100000;


--/*
--#5) AdventureWorks Easy Questions

--Q. Find the number of left racing socks ('Racing Socks, L') ordered by CompanyName 'Riding Cycles'
--*/
--SELECT SUM(orderqty) total
--FROM Product p JOIN SalesOrderDetail sd ON p.productid = sd.productid
--JOIN SalesOrderHeader sh ON sd.salesorderid = sh.salesorderid
--JOIN Customer c ON sh.customerid = c.customerid
--WHERE (Name = 'Racing Socks, L') AND (companyname = 'Riding Cycles');


--/*
--#6) AdventureWorks Medium Questions

--A "Single Item Order" is a customer order where only one item is ordered.

--Q. Show the SalesOrderID and the UnitPrice for every Single Item Order.
--*/
--WITH temp1 AS (
--  SELECT salesorderid, SUM(orderqty) items
--  FROM SalesOrderDetail
--  GROUP BY salesorderid
--  HAVING items = 1
--)
--SELECT salesorderid, unitprice
--FROM SalesOrderDetail
--WHERE salesorderid IN (SELECT salesorderid FROM temp1);


--/*
--#7) AdventureWorks Medium Questions

--Where did the racing socks go?

--Q. List the product name and the CompanyName for all Customers who ordered ProductModel 'Racing Socks'.
--*/
--SELECT p.name, companyname
--FROM Customer c JOIN SalesOrderHeader sh ON c.customerid = sh.customerid
--JOIN SalesOrderDetail sd ON sh.salesorderid = sd.salesorderid
--JOIN Product p ON sd.productid = p.productid
--JOIN ProductModel pm ON p.productmodelid = pm.productmodelid
--WHERE pm.name = 'Racing Socks';


--/*
--#8) AdventureWorks Medium Questions

--Q. Show the product description for culture 'fr' for product with ProductID 736.
--*/
--SELECT description
--FROM Product p JOIN ProductModel pm ON p.productmodelid = pm.productmodelid
--JOIN ProductModelProductDescription pmpd ON pm.productmodelid = pmpd.productmodelid
--JOIN ProductDescription pd ON pmpd.productdescriptionid = pd.productdescriptionid
--WHERE (productid = 736) AND (culture = 'fr');


--/*
--#9) AdventureWorks Medium Questions

--Q. Use the SubTotal value in SaleOrderHeader to list orders from the largest to the smallest.
--   For each order show the CompanyName and the SubTotal and the total weight of the order.
--*/
--SELECT companyname, subtotal, SUM(orderqty * weight) weight
--FROM SalesOrderHeader sh JOIN SalesOrderDetail sd ON sh.salesorderid = sd.salesorderid
--JOIN Product p ON sd.productid = p.productid
--JOIN Customer c ON sh.customerid = c.customerid
--GROUP BY sh.salesorderid, companyname, subtotal
--ORDER BY subtotal DESC;


--/*
--#10) AdventureWorks Medium Questions

--Q. How many products in ProductCategory 'Cranksets' have been sold to an address in 'London'?
--*/
--SELECT SUM(orderqty) total
--FROM Address a JOIN SalesOrderHeader sh ON a.addressid = sh.billtoaddressid
--JOIN SalesOrderDetail sd ON sh.salesorderid = sd.salesorderid
--JOIN Product p ON sd.productid = p.productid
--JOIN ProductCategory pc ON p.productcategoryid = pc.productcategoryid
--WHERE (city = 'London') AND (pc.name = 'Cranksets');


--/*
--#11) AdventureWorks Hard Questions

--Q. For every customer with a 'Main Office' in Dallas show AddressLine1 of the 'Main Office' and AddressLine1 of the 'Shipping' address.
--   If there is no shipping address leave it blank.
--   Use one row per customer.
--*/
--SELECT companyname,
--  MAX(CASE WHEN addresstype = 'Main Office' THEN addressline1 ELSE '' END) main_office,
--  MAX(CASE WHEN addresstype = 'Shipping' THEN addressline1 ELSE '' END) shipping
--FROM CustomerAddress ca JOIN Address a ON ca.addressid = a.addressid
--JOIN Customer c ON ca.customerid = c.customerid
--WHERE city = 'Dallas'
--GROUP BY companyname;


--/*
--#12) AdventureWorks Hard Questions

--Q. For each order show the SalesOrderID and SubTotal calculated three ways:
--   A) From the SalesOrderHeader
--   B) Sum of OrderQty*UnitPrice
--   C) Sum of OrderQty*ListPrice
--*/
--WITH tempA AS (
--  SELECT salesorderid, subtotal A_total
--  FROM SalesOrderHeader
--), tempB AS (
--  SELECT salesorderid, SUM(orderqty * unitprice) B_total
--  FROM SalesOrderDetail
--  GROUP BY salesorderid
--), tempC AS (
--  SELECT salesorderid, SUM(orderqty * listprice) C_total
--  FROM SalesOrderDetail sd JOIN Product p ON sd.productid = p.productid
--  GROUP BY salesorderid
--)
--SELECT tempA.salesorderid, A_total, B_total, C_total
--FROM tempA JOIN tempB ON tempA.salesorderid = tempB.salesorderid
--JOIN tempC ON tempB.salesorderid = tempC.salesorderid;


--/*
--#13) AdventureWorks Hard Questions

--Q. Show the best selling item by value.
--*/
--SELECT name, SUM(orderqty * unitprice) total_value
--FROM SalesOrderDetail sd JOIN Product p ON sd.productid = p.productid
--GROUP BY name
--ORDER BY total_value DESC
--LIMIT 1;


--/*
--#14) AdventureWorks Hard Questions

--Q. Show how many orders are in the following ranges (in $):
--   RANGE      Num Orders      Total Value
--   0-99
--   100-999
--   1000-9999
--   10000-
--*/
--WITH temp1 AS (
--  SELECT salesorderid, SUM(orderqty * unitprice) order_total
--  FROM SalesOrderDetail
--  GROUP BY salesorderid
--), temp2 AS (
--  SELECT salesorderid, order_total, CASE
--    WHEN order_total BETWEEN 0 AND 99 THEN '0-99'
--    WHEN order_total BETWEEN 100 AND 999 THEN '100-999'
--    WHEN order_total BETWEEN 1000 AND 9999 THEN '1000-9999'
--    WHEN order_total >= 10000 THEN '10000-'
--    ELSE 'Error'
--    END AS rng
--  FROM temp1
--)
--SELECT rng 'RANGE', COUNT(rng) 'Num Orders', SUM(order_total) 'Total Value'
--FROM temp2
--GROUP BY rng;


--/*
--#15) AdventureWorks Hard Questions

--Q. Identify the three most important cities.
--   Show the break down of top level product category against city.
--*/
--WITH temp1 AS (
--  SELECT city, SUM(unitprice * orderqty) AS total_sales
--  FROM SalesOrderDetail sd JOIN SalesOrderHeader sh ON sd.salesorderid = sh.salesorderid
--  JOIN Address a ON sh.shiptoaddressid = a.addressid
--  GROUP BY city
--  ORDER BY total_sales DESC
--  LIMIT 3
--)
--SELECT city, pc.name, SUM(unitprice * orderqty) total_sales
--FROM SalesOrderDetail sd JOIN SalesOrderHeader sh ON sd.salesorderid = sh.salesorderid
--JOIN Address a ON sh.shiptoaddressid = a.addressid
--JOIN Product p ON sd.productid = p.productid
--JOIN ProductCategory pc ON p.productcategoryid = pc.productcategoryid
--WHERE city IN (SELECT city FROM temp1)
--GROUP BY city, pc.name
--ORDER BY city, total_sales DESC;


--/*
--#1) AdventureWorks Resit Questions

--Q.  List the SalesOrderNumber for the customers 'Good Toys' and 'Bike World'.
--*/
--SELECT companyname, salesorderid
--FROM Customer c LEFT JOIN SalesOrderHeader sh ON c.customerid = sh.customerid
--WHERE companyname LIKE '%Good Toys%' OR companyname LIKE '%Bike World%';


--/*
--#2) AdventureWorks Resit Questions

--Q.  List the ProductName and the quantity of what was ordered by 'Futuristic Bikes'.
--*/
--SELECT companyname, p.name, SUM(sd.orderqty) qty
--FROM Customer c JOIN SalesOrderHeader sh ON c.customerid = sh.customerid
--JOIN SalesOrderDetail sd ON sh.salesorderid = sd.salesorderid
--JOIN Product p ON sd.productid = p.productid
--WHERE companyname LIKE '%Futuristic Bikes%'
--GROUP BY companyname, p.name;


--/*
--#3) AdventureWorks Resit Questions

--Q.  List the name and addresses of companies containing the word 'Bike' (upper or lower case) and companies containing 'cycle' (upper or lower case).
--    Ensure that the 'bike's are listed before the 'cycles's.
--*/
--WITH temp1 AS (
--  (SELECT DISTINCT(companyname), customerid, IF(1, 'bike', '') tag
--  FROM Customer
--  WHERE companyname LIKE '%bike%'
--  ) UNION ALL (
--  SELECT DISTINCT(companyname), customerid, IF(1, 'cycle', '') tag
--  FROM Customer
--  WHERE companyname LIKE '%cycle%')
--)
--SELECT companyname, tag, ca.addressid, addressline1, addressline2, city, stateprovince, postalcode
--FROM temp1 JOIN CustomerAddress ca ON temp1.customerid = ca.customerid
--JOIN Address a ON ca.addressid = a.addressid
--ORDER BY tag;


--/*
--#4) AdventureWorks Resit Questions

--Q.  Show the total order value for each CountryRegion.
--    List by value with the highest first.
--*/
--SELECT countyregion, SUM(subtotal) total
--FROM Address a JOIN SalesOrderHeader sh ON a.addressid = sh.shiptoaddressid
--GROUP BY countyregion;
--From Bro Code

--#1   00:00:00 MySQL intro + installation 
--        00:02:22 Windows installation
--        00:06:05 MAC OS installation
--#2   00:10:29 DATABASES
use vdbms
--#3   00:14:29 TABLES
--#4   00:22:38 INSERT ROWS
--#5   00:28:32 SELECT
--#6   00:33:32 UPDATE & DELETE
--#7   00:37:03 AUTOCOMMIT, COMMIT, ROLLBACK
--#8   00:39:41 CURRENT_DATE() & CURRENT_TIME()
select getdate()
SELECT CONVERT(TIME, GETDATE()) AS CurrentTime;
--#9   00:42:26 UNIQUE
alter table policy_Table 
add constraint unique_PT 
unique (POLICY_Type) 
begin tran update policy_Table set policy_Type = 'General' where policy_id = 20
alter table policy_table
add constraint unique_pt_Policyid
unique (policy_id)
begin tran update policy_Table set policy_id = 20 where policy_id = 27 -- rollback
alter table policy_Holders
add constraint PH_unique_Name
unique (Name)
begin tran update Policy_Holders set name = 'vinay' where policy_id = 263 -- Rollback
alter table policy_Holders
add constraint PH_UNIQUE_ID
unique(policy_Holder_id)
begin tran update Policy_Holders set policy_Holder_id = 2 where policy_Holder_id = 5 -- Rollback
--#10 00:47:09 NOT NULL
alter table policy_Holders
alter column age int not null
begin tran update Policy_Holders set age = null where age = 23 -- rollback
select * from Policy_Holders
alter table policy_Holders 
alter column gender varchar(250) not null
begin tran update Policy_Holders set gender = null where policy_Holder_id = 2 -- Rollback
--#11 00:50:01 CHECK
alter table policy_Holders 
add CONSTRAINT CHECK_PH_Age 
check (AGE > 18)
BEGIN TRAN UPDATE Policy_Holders SET AGE = 17 WHERE NAME = 'Mahesh' -- Rollback
--#12 00:53:58 DEFAULT
--CREATE TABLE products
--(
--product_id              int ,
--Product_Name            varchar(100),
-- Price                   int default 0 ( Taken default runned sucessfully )
--price                   int
--)
--alter table product
--alter price set default 0
--insert into products (product_id, Product_Name) values (1, 'Mouse')
--select * from products
--drop table products
--insert into Policy_Holders (Gender) values (null)

--#13 01:02:16 PRIMARY KEYS 
--primary keys are used to make sure that certain column as unique values also doesn't insert null values
--and creates index for that column 

--#14 01:07:41 AUTO_INCREMENT

--#15 01:11:36 FOREIGN KEYS

--#16 01:19:52 JOINS
select ph.policy_id, ph.Name, ph.age, 
pt.policy_Type, pt.Premium_Amount, 
ag.agent_id, ag.Name,ag.Phone
from Policy_Holders ph
join policy_Table pt on Ph.policy_id = pt.policy_id
join agents ag on ag.policy_id = pt.policy_id
--#17 01:24:55 FUNCTIONS

--1. **Aggregate Functions**: 
--These functions perform operations on a set of values and return a single value. 
--Common aggregate functions include `SUM()`, `AVG()`, `MIN()`, `MAX()`, and `COUNT()`.
--`SUM()`,  To know the total sum of an amount etc.
select sum(premium_Amount) as Total_pre_Amount from policy_Table
--`AVG()`, To know the average in all over column
select avg(coverage_Amount) as Avg_coverage_Amount from policy_Table
--`MIN()`,  To know the minimun amount in a column or least 
select min(salary_Amount) as Min_Salary from salaries
--`MAX()`, To know the maximum  amount in a column or Hegihest
select max(salary_Amount) as Min_Salary from salaries
--`COUNT()`. To count how many rows in a column
select count(policy_id) from policy_Table
--2. **String Functions**: 
--These functions manipulate character or string data. Examples include 
--`LEN()`, `LEFT()`, `RIGHT()`, `UPPER()`, `LOWER()`, `CONCAT()`, and `SUBSTRING()`.
--`LEN()` To know the legnt of a character in a table
select len(policy_Type) from policy_Table
select * from policy_Table
--`LEFT()`
--`RIGHT()`
--`UPPER()`
--`LOWER()`
--`CONCAT()` To add different names or particular columns 

--`SUBSTRING()`.
--3. **Date and Time Functions**: 
--These functions operate on date and time values. 
--Examples include `GETDATE()`, `DATEADD()`, `DATEDIFF()`, `YEAR()`, `MONTH()`, `DAY()`, `HOUR()`, `MINUTE()`, and `SECOND()`.

--4. **Numeric Functions**: 
--These functions operate on numeric data.
--Examples include `ABS()`, `ROUND()`, `CEILING()`, `FLOOR()`, and `RAND()`.

--5. **Conversion Functions**: 
--These functions convert data from one type to another. 
--Examples include `CAST()`, `CONVERT()`, and `PARSE()`.

--6. **Logical Functions**: 
--These functions perform logical operations. 
--Examples include `AND`, `OR`, and `NOT`.

--7. **Conditional Functions**: 
--These functions return different values based on a condition. 
--Examples include `CASE` and `IIF()`.

--8. **Window Functions**: 
--These functions perform calculations across a set of table rows that are related to the current row. 
--Examples include `ROW_NUMBER()`, `RANK()`, `LEAD()`, and `LAG()`.

--#18 01:28:40 
--AND Operators are must includes both syntaxes has to correct other wise runs error.
where policy_Type = 'General' 
and Premium_Amount >= 50000
--OR Operator are not must any if one of the statemnt is right gives output.
select policy_Type, start_Date from policy_Table
where policy_Type = 'Car Insurance' or
Start_Date >= '2023'
-- NOT It only shows the required output
select * from salaries
where not Salary_amount >= 75000
--#19 01:34:37 WILD CARDS
select * from policy_Table where policy_Type like '%ce'
select * from Policy_Holders where gender like 'Ma%'
--#20 01:38:55 ORDER BY
select * from agents 
order by Name asc
--#21 01:41:31 LIMIT
SELECT name, age
FROM Policy_Holders
LIMIT 5


select * from policy_Table
SELECT * FROM Policy_Holders 
select * from agents

--#22 01:44:24 UNIONS
select * from policy_Table
union 
select * from Policy_Holders

--#23 01:48:37 SELF JOINS

--#24 01:58:39 VIEWS
--#25 02:04:41 INDEXES
--#26 02:11:06 SUBQUERIES

use vdbms
select * from Policy_Holders select  * from policy_Table

select policy_id, policy_Type, 
(select avg(Premium_Amount) from policy_Table)
from policy_Table 

select policy_id, policy_Type, Premium_Amount from policy_Table 
where Premium_Amount >
(select avg(Premium_Amount) from policy_Table)

select name, gender from Policy_Holders where age = (select avg(age) from Policy_Holders)
select name, gender from Policy_Holders where age = (select max(age) from Policy_Holders)
select name, gender from Policy_Holders where age in (select age from Policy_Holders where age > 23)

--#27 02:17:52 GROUP BY
select policy_Holder_id, name, gender
from Policy_Holders where policy_id in 
(select  policy_id from Policy_Holders where policy_id >= 21)
group by 

use vdbms
select * from Policy_Holders
select * from policy_Table
--#28 02:23:00 ROLLUP


--#29 02:26:50 ON DELETE
--#30 02:34:20 STORED PROCEDURES
--#31 02:42:22 TRIGGERS

--/*
--#5) AdventureWorks Resit Questions

--Q.  Find the best customer in each region.
--*/
--WITH temp1 AS (
--  SELECT countyregion, companyname, SUM(subtotal) total,
--    RANK() OVER (PARTITION BY countyregion ORDER BY total DESC) rnk
--  FROM Address a JOIN SalesOrderHeader sh ON a.addressid = sh.shiptoaddressid
--  JOIN Customer c ON sh.customerid = c.customerid
--  GROUP BY countyregion, companyname
--)
--SELECT countyregion, companyname, total
--FROM temp1
--WHERE rnk = 1;
