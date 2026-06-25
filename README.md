# CODEVEDX
create database amazon_sales_analysis;
use amazon_sales_analysis; 
CREATE TABLE customers (
    CustomerID VARCHAR(50) PRIMARY KEY,
    CustomerName VARCHAR(150) NOT NULL
);

CREATE TABLE products (
    ProductID VARCHAR(50) PRIMARY KEY,
    ProductName VARCHAR(255) NOT NULL,
    Category VARCHAR(100),
    Brand VARCHAR(100)
);
CREATE TABLE orders (
    OrderID VARCHAR(50) PRIMARY KEY,
    CustomerID VARCHAR(50) NOT NULL,
    ProductID VARCHAR(50) NOT NULL,
    Quantity INT,
    UnitPrice DECIMAL(10,2),
    Discount DECIMAL(10,2),
    Tax DECIMAL(10,2),
    ShippingCost DECIMAL(10,2),
    TotalAmount DECIMAL(12,2),
    PaymentMethod VARCHAR(50),
    OrderStatus VARCHAR(50),

    FOREIGN KEY (CustomerID)
        REFERENCES customers(CustomerID),

    FOREIGN KEY (ProductID)
        REFERENCES products(ProductID)
);

desc customers;
desc products;
desc orders;
DROP TABLE IF EXISTS orders;
DROP TABLE IF EXISTS products;
DROP TABLE IF EXISTS customers;
LOAD DATA LOCAL INFILE 'C:/Users/onkar/Downloads/Amazon.csv'
INTO TABLE amazon_raw
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
SHOW GLOBAL VARIABLES LIKE 'local_infile';
SELECT @@local_infile;
DESCRIBE amazon_raw;
/*Total revenue generated*/
SELECT COUNT(*) FROM amazon_raw;
SELECT * FROM amazon_raw LIMIT 5;
DESCRIBE amazon_raw;

DROP TABLE IF EXISTS amazon_raw;

CREATE TABLE amazon_raw (
    OrderID VARCHAR(50),
    OrderDate DATE,
    CustomerID VARCHAR(50),
    CustomerName VARCHAR(150),
    ProductID VARCHAR(50),
    ProductName VARCHAR(255),
    Category VARCHAR(100),
    Brand VARCHAR(100),
    Quantity INT,
    UnitPrice DECIMAL(10,2),
    Discount DECIMAL(10,2),
    Tax DECIMAL(10,2),
    ShippingCost DECIMAL(10,2),
    TotalAmount DECIMAL(12,2),
    PaymentMethod VARCHAR(50),
    OrderStatus VARCHAR(50),
    City VARCHAR(100),
    State VARCHAR(50),
    Country VARCHAR(100),
    SellerID VARCHAR(50)
);
SELECT COUNT(*) FROM amazon_raw;

SELECT * FROM amazon_raw LIMIT 5;

/*Total revenue generated*/
SELECT SUM(TotalAmount) AS Total_Revenue
FROM amazon_raw;

/*Most popular products*/
SELECT ProductName,
SUM(CAST(Quantity AS UNSIGNED)) AS Total_Quantity_Sold
FROM amazon_raw
GROUP BY ProductName
ORDER BY Total_Quantity_Sold DESC
LIMIT 10;

/*Products contributing most to revenue*/
SELECT ProductName,
SUM(CAST(TotalAmount AS DECIMAL(10,2))) AS Revenue
FROM amazon_raw
GROUP BY ProductName
ORDER BY Revenue DESC
LIMIT 10;

/*Best-performing categories*/
SELECT Category,
SUM(CAST(TotalAmount AS DECIMAL(10,2))) AS Revenue
FROM amazon_raw
GROUP BY Category
ORDER BY Revenue DESC;

/*Highest revenue-generating brands*/

SELECT Brand,
SUM(CAST(TotalAmount AS DECIMAL(10,2))) AS Revenue
FROM amazon_raw
GROUP BY Brand
ORDER BY Revenue DESC;

/*Order Status Analysis*/

SELECT OrderStatus,
COUNT(*) AS Total_Orders
FROM amazon_raw
GROUP BY OrderStatus;

/*Top-performing cities*/

SELECT City,
SUM(CAST(TotalAmount AS DECIMAL(10,2))) AS Revenue
FROM amazon_raw
GROUP BY City
ORDER BY Revenue DESC
LIMIT 10;

/*Highest-value customers*/

SELECT CustomerName,
SUM(CAST(TotalAmount AS DECIMAL(10,2))) AS Spending
FROM amazon_raw
GROUP BY CustomerName
ORDER BY Spending DESC
LIMIT 10;

/*Relationship between discounts and revenue*/

SELECT Category,
AVG(CAST(Discount AS DECIMAL(10,2))) AS Avg_Discount,
SUM(CAST(TotalAmount AS DECIMAL(10,2))) AS Revenue
FROM amazon_raw
GROUP BY Category;

/*Categories with high logistics costs*/

SELECT Category,
AVG(CAST(ShippingCost AS DECIMAL(10,2))) AS Avg_Shipping_Cost
FROM amazon_raw
GROUP BY Category;
