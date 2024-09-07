## 197. Rising Temperature
https://leetcode.com/problems/rising-temperature/?envType=study-plan-v2&envId=top-sql-50
```
Table: Weather

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| recordDate    | date    |
| temperature   | int     |
+---------------+---------+
id is the column with unique values for this table.
There are no different rows with the same recordDate.
This table contains information about the temperature on a certain day.
 

Write a solution to find all dates' id with higher temperatures compared to its previous dates (yesterday).


Example 1:

Input: 
Weather table:
+----+------------+-------------+
| id | recordDate | temperature |
+----+------------+-------------+
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |
+----+------------+-------------+
Output: 
+----+
| id |
+----+
| 2  |
| 4  |
+----+
```

## Solution
```sql
SELECT w1.id 
FROM Weather w1, Weather w2
WHERE w1.temperature > w2.temperature 
    AND DATEDIFF(w1.recordDate, w2.recordDate) = 1;
```
### MySQL DATEDIFF() Function
https://www.w3schools.com/sql/func_mysql_datediff.asp
### SQL Self Join
https://www.w3schools.com/sql/sql_join_self.asp