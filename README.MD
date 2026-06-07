# Project 3: SQL Data Analysis - DCLAB Orders

Analyzed the `dclab.csv` orders dataset using SQL Server to extract business insights.  
Covers all SQL fundamentals required: SELECT, WHERE, ORDER BY, GROUP BY, COUNT, SUM, AVG.

## Dataset
**File:** `dclab.csv`  
**Table:** `dbo.dclab`  
**Columns:** `OrderID, Date, CustomerID, Product, Quantity, Unit_Price, Shipping_Address, Payment_Method, Order_Status, Tracking_Number, Items`

## Project Requirements Met
| Requirement | SQL Clause |
| --- | --- |
| Extract data | SELECT |
| Filter rows | WHERE |
| Sort results | ORDER BY |
| Group + summarize | GROUP BY |
| Basic aggregations | COUNT, SUM, AVG |

## Key Insights + Queries

### 1. Filter Shipped Credit Card Orders
```sql
SELECT OrderID, Date, Product, Quantity, Unit_Price
FROM dclab
WHERE Order_Status = 'Shipped' AND Payment_Method = 'Credit Card'

### 2. TOP 10 MOST EXPENSIVE ORDER
SELECT OrderID, Product, Quantity, Unit_Price, 
       Quantity * Unit_Price AS TotalPrice
FROM dclab
ORDER BY TotalPrice DESC
OFFSET 0 ROWS FETCH NEXT 10 ROWS ONLY

### 3. Orders Count per Product 
SELECT Product, COUNT(*) AS OrderCount
FROM dclab
GROUP BY Product
ORDER BY OrderCount DESC

### 4. Average Unit Price per Product
SELECT Product, AVG(Unit_Price) AS AvgPrice
FROM dclab
GROUP BY Product
ORDER BY AvgPrice DESC

### 5. Total Quantity Sold by Payment Method
SELECT Payment_Method, SUM(Quantity) AS TotalQuantity
FROM dclab
GROUP BY Payment_Method

### 6. Revenue by Product - Completed Orders Only
SELECT Product, 
       COUNT(*) AS Orders,
       SUM(Quantity * Unit_Price) AS TotalRevenue,
       AVG(Unit_Price) AS AvgUnitPrice
FROM dclab
WHERE Order_Status IN ('Shipped', 'Delivered')
GROUP BY Product
ORDER BY TotalRevenue DESC

### 7. Best Selling Month
SELECT 
    FORMAT(Date, 'yyyy-MM') AS OrderMonth,
    COUNT(*) AS TotalOrders,
    SUM(Quantity * Unit_Price) AS MonthlyRevenue
FROM dclab
WHERE Order_Status IN ('Shipped', 'Delivered')
GROUP BY FORMAT(Date, 'yyyy-MM')
ORDER BY MonthlyRevenue DESC

### 8. Cancel Rate by Payment Method
SELECT 
    Payment_Method,
    COUNT(*) AS TotalOrders,
    SUM(CASE WHEN Order_Status = 'Cancelled' THEN 1 ELSE 0 END) AS CancelledOrders,
    ROUND(100.0 * SUM(CASE WHEN Order_Status = 'Cancelled' THEN 1 ELSE 0 END) / COUNT(*), 2) AS CancelRatePercent
FROM dclab
GROUP BY Payment_Method
ORDER BY CancelRatePercent DESC
