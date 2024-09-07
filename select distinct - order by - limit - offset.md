## 176. Second Highest Salary
```
Table: Employee

+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
id is the primary key (column with unique values) for this table.
Each row of this table contains information about the salary of an employee.
 

Write a solution to find the second highest distinct salary from the Employee table. If there is no second highest salary, return null (return None in Pandas).

The result format is in the following example.

 

Example 1:

Input: 
Employee table:
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
Output: 
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

## Solution
```sql
SELECT (
    SELECT DISTINCT Salary 
    FROM Employee
    ORDER BY Salary DESC
    LIMIT 1 OFFSET 1
) AS SecondHighestSalary;
```
## Explain
#### SELECT ( ... ) AS SecondHighestSalary:

- Phần ngoài cùng là một câu truy vấn SELECT, trong đó kết quả của truy vấn con sẽ được gán tên là SecondHighestSalary.

- Truy vấn này trả về mức lương cao thứ hai.
#### SELECT DISTINCT Salary FROM Employee:

- Truy vấn con bên trong chọn các giá trị lương (Salary) khác nhau từ bảng Employee.

- Từ khóa DISTINCT đảm bảo rằng các giá trị lương không bị lặp lại. Điều này cần thiết vì có thể có nhiều nhân viên có cùng mức lương.
#### ORDER BY Salary DESC:

- Kết quả được sắp xếp theo thứ tự giảm dần của lương (Salary DESC), có nghĩa là mức lương cao nhất sẽ nằm ở đầu.
#### LIMIT 1 OFFSET 1:

- LIMIT 1 giới hạn kết quả chỉ trả về 1 hàng (một mức lương).
- OFFSET 1 bỏ qua hàng đầu tiên (mức lương cao nhất) và lấy hàng tiếp theo (mức lương cao thứ hai).
