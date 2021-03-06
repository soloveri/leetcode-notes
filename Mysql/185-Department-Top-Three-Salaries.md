---
title: 185. 部门工资前三高的所有员工
mathjax: true
data: 2020-08-01 16:42:40
updated:
tags:
- topK
categories:
- MySQL
---

[原题链接](https://leetcode-cn.com/problems/department-top-three-salaries/)

---

### 0x0 题目详情

Employee 表包含所有员工信息，每个员工有其对应的工号 Id，姓名 Name，工资 Salary 和部门编号 DepartmentId 。

+----+-------+--------+---------------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+---------------------+
| 1  |&nbsp; &nbsp;Joe&nbsp; &nbsp; | 85000  |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;            |
| 2  |&nbsp;&nbsp;Henry&nbsp;| 80000  |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| 3  |&nbsp;&nbsp;Sam&nbsp;&nbsp;&nbsp;| 60000  |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| 4  |&nbsp;&nbsp; Max&nbsp;&nbsp;&nbsp;| 90000&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| 5  |&nbsp;&nbsp;Janet&nbsp;&nbsp;| 69000  |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| 6  |&nbsp;Randy&nbsp;| 85000&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| 7  |&nbsp;&nbsp;&nbsp;&nbsp;Will&nbsp;&nbsp;&nbsp;| 70000  |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
+----+-------+--------+----------------------+
Department 表包含公司所有部门的信息。

+----+----------+
|&nbsp;Id&nbsp;&nbsp;|&nbsp;&nbsp;Name&nbsp;&nbsp;|
+----+----------+
|&nbsp;1&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;IT&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|&nbsp;2&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;Sales&nbsp;&nbsp;|
+----+----------+
编写一个 SQL 查询，找出每个部门获得前三高工资的所有员工。例如，根据上述给定的表，查询结果应返回：

+------------+----------+-----------------+
| Department | Employee | Salary |
+------------+----------+-----------------+
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;IT&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;Max&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;90000&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;IT&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;Randy&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;85000&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;IT&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;Joe&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;85000&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;IT&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;Will&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;70000&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|&nbsp;&nbsp;&nbsp;&nbsp;Sales&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;Henry    |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;80000&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|&nbsp;&nbsp;&nbsp;&nbsp;Sales&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;Sam&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;60000&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
+------------+----------+-----------------+

>解释：
IT 部门中，Max 获得了最高的工资，Randy 和 Joe 都拿到了第二高的工资，Will 的工资排第三。销售部门（Sales）只有两名员工，Henry 的工资最高，Sam 的工资排第二。

### 0x1 解题思路

对于没有窗口函数的MySQL5.7来说,要想实现topK的功能。基本步骤如下:

我们想要找到的就是符合要求的员工ID。那么我们将Employee与自身进行左连接,连接要求时部门要求一样,但是要求左表e1的Salary小于右表e2的Salary。这么做的目的就是找出在同一部门中,比左表员工Salary高的员工。

然后再对左表中的员工ID进行聚合,分组条件比当前员工薪水高的条目小于三个,这样加上当前员工,每个部门薪水的top3就求出来了。这种方法适用于topK问题。


### 0x2 代码实现

``` sql
# Write your MySQL query statement below

select d.Name as Department,e.Name as Employee,e.Salary as Salary

from Employee as e inner join Department as d 
on e.DepartmentId=d.Id
where e.Id in(
    select e1.Id
    from Employee as e1 left join Employee as e2
    # 要求同一部门,并且左表薪水低于右表
    on e1.DepartmentId=e2.DepartmentId and e1.Salary<e2.Salary
    group by e1.Id
    # 筛选出比当前员工薪水高的人只有两个的员工Id
    having count(distinct e2.Salary)<3
)
order by d.Id asc,Salary desc;

```

### 0x3 课后总结