
## Solution
```sql
USE classicmodels;

SELECT c.customerNumber, c.customerName, e.firstName AS repFirstName, e.lastName AS repLastName
FROM customers c
LEFT JOIN employees e
ON c.salesRepEmployeeNumber = e.employeeNumber;
```
```
Báo cáo người đại diện tài khoản cho từng khách hàng?
```
.
## Solution
```sql
USE classicmodels;

SELECT customerName, COUNT(*) AS totalOrders, MAX(total) AS maxTotal 
FROM (
	SELECT ord.orderNumber, ord.total, o.customerNumber, c.customerName 
	FROM (
		SELECT orderNumber, SUM(priceEach * quantityOrdered) AS total
		FROM orderdetails
		GROUP BY orderNumber
	) ord
	JOIN orders o ON ord.orderNumber = o.orderNumber
	JOIN customers c ON o.customerNumber = c.customerNumber
) dat
GROUP BY customerNumber;
```
```
Tên khách hàng, số đơn hàng và tổng chi phí của đơn hàng đắt nhất là gì?
```
.
## Solution
```sql
USE classicmodels;

SELECT sum(amount)
FROM payments WHERE customerNumber IN (
	SELECT customernumber
	FROM customers WHERE customerName='Atelier graphique'
 );
```
```
Báo cáo tổng số tiền thanh toán cho Atelier Graphique
```
.
## Solution
```sql
USE classicmodels;
SELECT count(*) FROM orders
WHERE customerNumber IN (
    SELECT customerNumber FROM customers WHERE customerName='Herkku Gifts'
)


USE classicmodels;
SELECT * FROM employees WHERE officeCode IN (
    SELECT officeCode FROM offices WHERE city='Boston'
)
```
```
Herkku Gifts đã đặt bao nhiêu đơn hàng?  Nhân viên ở Boston là ai?
```
.
## Solution
```sql
USE classicmodels;

SELECT 
    p1.productName AS ProductA,
    p2.productName AS ProductB,
    COUNT(*) AS timesTogether
FROM 
    orderdetails od1
JOIN 
    orderdetails od2 ON od1.orderNumber = od2.orderNumber 
                     AND od1.productCode < od2.productCode
JOIN 
    products p1 ON od1.productCode = p1.productCode
JOIN 
    products p2 ON od2.productCode = p2.productCode
GROUP BY 
    p1.productName, p2.productName
HAVING 
    COUNT(*) >= 10
ORDER BY 
    timesTogether DESC;
```
```
Phân tích giỏ hàng: Một nhiệm vụ phân tích bán lẻ phổ biến là phân tích 
từng giỏ hàng hoặc đơn hàng để tìm hiểu những sản phẩm nào thường được 
mua cùng nhau. Báo cáo tên của các sản phẩm xuất hiện trong cùng một 
đơn hàng mười lần trở lên.
```
.
## Solution
```sql
USE classicmodels;

SELECT 
    c.customerName,
    SUM(od.quantityOrdered * od.priceEach) AS customerRevenue,
    ROUND((SUM(od.quantityOrdered * od.priceEach) / 
		(SELECT SUM(quantityOrdered * priceEach) FROM orderdetails)) * 100, 2) 
        AS revenuePercentage
FROM 
    customers c
JOIN 
    orders o ON c.customerNumber = o.customerNumber
JOIN 
    orderdetails od ON o.orderNumber = od.orderNumber
GROUP BY 
    c.customerName
ORDER BY 
    c.customerName;
```
```
Tính toán doanh thu do mỗi khách hàng tạo ra dựa trên đơn hàng của họ. 
Ngoài ra, hiển thị doanh thu của mỗi khách hàng dưới dạng phần trăm 
tổng doanh thu. Sắp xếp theo tên khách hàng.
```
.
## Solution
```sql
USE classicmodels;

SELECT 
    c.customerName,
    SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)) AS profit,
    ROUND((SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)) / 
		(
			SELECT SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)) 
				FROM orderdetails od
            JOIN 
				products p ON od.productCode = p.productCode
        )) * 100, 2) 
        AS profitPercentage
FROM 
    customers c
JOIN 
    orders o ON c.customerNumber = o.customerNumber
JOIN 
    orderdetails od ON o.orderNumber = od.orderNumber
JOIN 
	products p ON p.productCode = od.productCode
GROUP BY 
    c.customerName
ORDER BY 
    profit DESC;
```
```
Tính toán lợi nhuận tạo ra bởi mỗi khách hàng dựa trên đơn hàng của họ. 
Ngoài ra, hiển thị lợi nhuận của mỗi khách hàng dưới dạng phần trăm lợi
nhuận tổng thể. Sắp xếp theo lợi nhuận giảm dần.
```
.
```sql
USE classicmodels;

SELECT p.productName 
FROM 
  orderdetails od
JOIN 
  products p ON p.productCode = od.productCode
WHERE od.priceEach / p.buyPrice >= 2
GROUP BY p.productName; 
```
```
Báo cáo những sản phẩm đã được bán với mức tăng giá 100% trở lên (tức là 
giá của mỗi sản phẩm ít nhất gấp đôi giá mua)
```
.
## Solution
```sql
USE classicmodels;

SELECT 
	od.orderNumber, 
    p.productName, 
    ROUND((od.quantityOrdered * od.priceEach) / t.total * 100, 2) AS orderPercentage 
FROM
	orderdetails od
JOIN
	products p ON p.productCode = od.productCode
JOIN
	(
		SELECT orderNumber, SUM(quantityOrdered * priceEach) as total 
        FROM orderdetails
        GROUP BY orderNumber
        HAVING COUNT(orderNumber) > 2
    ) as t ON od.orderNumber = t.orderNumber
WHERE (od.quantityOrdered * od.priceEach) / t.total >= 0.5
ORDER BY od.orderNumber;
```
```
For orders containing more than two products, report those products
that constitute more than 50% of the value of the order.
```
## Solution
```sql
USE classicmodels;

SELECT 
    p.productCode,
    p.productName,
    SUM(CASE WHEN YEAR(o.orderDate) = 2003 THEN od.quantityOrdered * od.priceEach ELSE 0 END) AS sales2003,
    SUM(CASE WHEN YEAR(o.orderDate) = 2004 THEN od.quantityOrdered * od.priceEach ELSE 0 END) AS sales2004,
    ROUND(
        SUM(CASE WHEN YEAR(o.orderDate) = 2004 THEN od.quantityOrdered * od.priceEach ELSE 0 END) / 
        NULLIF(SUM(CASE WHEN YEAR(o.orderDate) = 2003 THEN od.quantityOrdered * od.priceEach ELSE 0 END), 0), 
        2
    ) AS salesRatio
FROM 
    products p
JOIN 
    orderdetails od ON p.productCode = od.productCode
JOIN 
    orders o ON od.orderNumber = o.orderNumber
WHERE 
    YEAR(o.orderDate) IN (2003, 2004)
GROUP BY 
    p.productCode, p.productName
ORDER BY 
    p.productCode;
```
```
Tính tỷ lệ doanh số của từng sản phẩm trong năm 2003 so với năm 2004.
```