## 183. Customers Who Never Order
```
Table: Customers

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+
id is the primary key (column with unique values) for this table.
Each row of this table indicates the ID and name of a customer.
 

Table: Orders

+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| customerId  | int  |
+-------------+------+
id is the primary key (column with unique values) for this table.
customerId is a foreign key (reference columns) of the ID from the Customers table.
Each row of this table indicates the ID of an order and the ID of the customer who ordered it.
 
Write a solution to find all customers who never order anything.


Example 1:

Input: 
Customers table:
+----+-------+
| id | name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
Orders table:
+----+------------+
| id | customerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
Output: 
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

## Solution
```sql
SELECT name AS Customers
FROM Customers c
LEFT JOIN Orders o
ON c.id = o.id
WHERE c.id NOT IN (SELECT customerId FROM orders)
```
### SQL JOIN
https://www.w3schools.com/sql/sql_join.asp
### SQL IN Operator
https://www.w3schools.com/sql/sql_in.asp

### JOIN nhiều cột
https://leetcode.com/problems/students-and-examinations/description/?envType=study-plan-v2&envId=top-sql-50