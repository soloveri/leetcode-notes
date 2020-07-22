---
title: 177. 第N高的薪水
mathjax: true
data: 2020-07-19 16:07:12
updated:
tags:
categories:
- Mysql
---

[原题链接](https://leetcode-cn.com/problems/nth-highest-salary/)

---

### 0x0 题目详情

>编写一个 SQL 查询，获取 Employee 表中第 n 高的薪水（Salary）。
+----+--------+
| Id | Salary |
+----+--------+
| 1     | 100 |
| 2     | 200 |
| 3     | 300 |
+----+--------+

### 0x1 解题思路

这道题不是很难，只是mysql的语法不太熟悉。首先回顾以下`limit`的用法。`limit`可以接受两个参数：limit`offset` ,`limit`。

- offset表示偏移量，**从0开始计算**。并且只能是常量，不能写运算表达式
- limit表示从offset开始查询的条数

limit有一个缺陷就是数据量很大时，越往后后页的效率越慢，`limit 1,10` 和`limit 10000,10`的区别还是比较明显的。例如下面的[例子](https://segmentfault.com/a/1190000008859706)：

>SQL代码1：平均用时6.6秒 SELECT * FROM `cdb_posts` ORDER BY pid LIMIT 1000000 , 30

>SQL代码2：平均用时0.6秒 SELECT * FROM `cdb_posts` WHERE pid >= (SELECT pid FROM  
`cdb_posts` ORDER BY pid LIMIT 1000000 , 1) LIMIT 30

所以一种优化策略就是当要查询的数据很靠后时，我们可以先定义查询一个目标位置的子查询，然后再从查询到的位置开始获取limit条数据。

### 0x2 代码实现

``` sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  set N=N-1;
  if N<0 then
  return null;
  else  
  RETURN (
      # Write your MySQL query statement below.
      select IFNULL((
        select distinct(Salary) from Employee
        order by Salary desc
        limit N,1
      ),null) as getNthHighestSalary
       
  );
  //别忘了end if
  end if;
END
```

mysql里的if还要end if一下,不错不错。



