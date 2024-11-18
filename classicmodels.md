
## Solution
```
Báo cáo người đại diện tài khoản cho từng khách hàng?
```
```sql
USE classicmodels;

SELECT c.customerNumber, c.customerName, e.firstName AS repFirstName, e.lastName AS repLastName
FROM customers c
LEFT JOIN employees e
ON c.salesRepEmployeeNumber = e.employeeNumber;
```

.
## Solution
```
Tên khách hàng, số đơn hàng và tổng chi phí của đơn hàng đắt nhất là gì?
```
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

.
## Solution
```
Báo cáo tổng số tiền thanh toán cho Atelier Graphique
```
```sql
USE classicmodels;

SELECT sum(amount)
FROM payments WHERE customerNumber IN (
	SELECT customernumber
	FROM customers WHERE customerName='Atelier graphique'
 );
```

.
## Solution
```
Herkku Gifts đã đặt bao nhiêu đơn hàng?  Nhân viên ở Boston là ai?
```
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

.
## Solution
```
Phân tích giỏ hàng: Một nhiệm vụ phân tích bán lẻ phổ biến là phân tích 
từng giỏ hàng hoặc đơn hàng để tìm hiểu những sản phẩm nào thường được 
mua cùng nhau. Báo cáo tên của các sản phẩm xuất hiện trong cùng một 
đơn hàng mười lần trở lên.
```
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

.
## Solution
```
Tính toán doanh thu do mỗi khách hàng tạo ra dựa trên đơn hàng của họ. 
Ngoài ra, hiển thị doanh thu của mỗi khách hàng dưới dạng phần trăm 
tổng doanh thu. Sắp xếp theo tên khách hàng.
```
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

.
## Solution
```
Tính toán lợi nhuận tạo ra bởi mỗi khách hàng dựa trên đơn hàng của họ. 
Ngoài ra, hiển thị lợi nhuận của mỗi khách hàng dưới dạng phần trăm lợi
nhuận tổng thể. Sắp xếp theo lợi nhuận giảm dần.
```
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

.
## Solution
```
Báo cáo những sản phẩm đã được bán với mức tăng giá 100% trở lên (tức là 
giá của mỗi sản phẩm ít nhất gấp đôi giá mua)
```
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

.
## Solution
```
For orders containing more than two products, report those products
that constitute more than 50% of the value of the order.
```
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

## Solution
```
Tính tỷ lệ doanh số của từng sản phẩm trong năm 2003 so với năm 2004.
```
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
.
## Solution
```
Tìm các sản phẩm được bán vào năm 2003 nhưng không phải năm 2004.
```
```sql
USE classicmodels;

SELECT DISTINCT p.productCode, p.productName FROM orders o
JOIN 
	orderdetails od ON o.orderNumber = od.orderNumber
JOIN 
	products p ON p.productCode = od.productCode
WHERE YEAR(o.orderDate) = 2003 AND p.productCode NOT IN (
	SELECT DISTINCT od.productCode FROM orders o
	JOIN 
		orderdetails od ON o.orderNumber = od.orderNumber
	WHERE YEAR(o.orderDate) = 2004
);	
```
.
## Solution
```
Liệt kê tên khách hàng và số đơn hàng tương ứng khi đơn hàng cụ thể của 
khách hàng đó có giá trị lớn hơn 25.000 đô la?
```
```sql
USE classicmodels;

SELECT 
    c.customerName, 
    o.orderNumber, 
    ROUND(SUM(od.quantityOrdered * od.priceEach), 2) AS orderValue
FROM customers c
JOIN 
    orders o ON c.customerNumber = o.customerNumber
JOIN
     orderdetails od ON o.orderNumber = od.orderNumber
GROUP BY c.customerName, o.orderNumber
HAVING orderValue > 25000;
```
.
## Solution
```
Sự khác biệt về ngày giữa ngày đặt hàng gần đây nhất và cũ nhất trong tệp Đơn hàng là gì?
```
```sql
USE classicmodels;

SELECT  DATEDIFF(MAX(orderDate), MIN(orderDate))
FROM orders;
```
.
## Solution
```
Tính tổng giá trị đã đặt hàng, tổng số tiền đã thanh toán và 
chênh lệch của chúng đối với mỗi khách hàng đối với các đơn hàng đã đặt
trong năm 2004 và các khoản thanh toán đã nhận trong năm 2004.
```
```sql
USE classicmodels;

SELECT 
    c.customerNumber,
    c.customerName,
    COALESCE(SUM(o.totalOrdered), 0) AS totalOrdered,
    COALESCE(SUM(p.totalPaid), 0) AS totalPaid,
    COALESCE(SUM(o.totalOrdered), 0) - COALESCE(SUM(p.totalPaid), 0) AS difference
FROM customers c
LEFT JOIN (
    SELECT 
        o.customerNumber,
        SUM(od.quantityOrdered * od.priceEach) AS totalOrdered
    FROM orders o
    JOIN orderdetails od ON o.orderNumber = od.orderNumber
    WHERE YEAR(o.orderDate) = 2004
    GROUP BY o.customerNumber
) o ON c.customerNumber = o.customerNumber
LEFT JOIN (
    SELECT 
        p.customerNumber,
        SUM(p.amount) AS totalPaid
    FROM payments p
    WHERE YEAR(p.paymentDate) = 2004
    GROUP BY p.customerNumber
) p ON c.customerNumber = p.customerNumber
GROUP BY c.customerNumber, c.customerName
ORDER BY c.customerName;
```
.
## Solution
```
Tính lợi nhuận tạo ra từ mỗi dòng sản phẩm, sắp xếp theo lợi nhuận giảm dần.
```
```sql
USE classicmodels;

SELECT 
    p.productLine, 
    SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)) AS profit 
FROM products p
JOIN orderdetails od ON p.productCode = od.productCode
JOIN orders o ON od.orderNumber = o.orderNumber
GROUP BY p.productLine
ORDER BY profit DESC;
```
.
## Solution
```
Tính toán lợi nhuận tạo ra bởi mỗi đại diện bán hàng dựa trên các đơn hàng 
từ khách hàng mà họ phục vụ. Sắp xếp theo lợi nhuận tạo ra giảm dần.
```
```sql
USE classicmodels;

SELECT 
    e.employeeNumber,
    e.firstName,
    e.lastName,
    SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)) AS profit
FROM employees e
JOIN customers c ON e.employeeNumber = c.salesRepEmployeeNumber
JOIN orders o ON c.customerNumber = o.customerNumber
JOIN orderdetails od ON o.orderNumber = od.orderNumber
JOIN products p ON od.productCode = p.productCode
GROUP BY e.employeeNumber
ORDER BY profit DESC;
```