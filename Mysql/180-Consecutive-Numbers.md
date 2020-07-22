---
title: 180. 连续出现的数字
mathjax: true
data: 2020-07-22 19:31:41
difficulty: medium
updated:
tags:
- 局部变量
categories:
- MySQL
---

[原题链接](https://leetcode-cn.com/problems/consecutive-numbers/)

---

## 0x0 题目详情
编写一个 SQL 查询，查找所有至少连续出现三次的数字。

+----+-----+
| Id | Num |
+----+-----+
| 1  |  &nbsp;&nbsp;&nbsp;1&nbsp;&nbsp;&nbsp;  |
| 2  |  &nbsp;&nbsp;&nbsp;1&nbsp;&nbsp;&nbsp;  |
| 3  |  &nbsp;&nbsp;&nbsp;1&nbsp;&nbsp;&nbsp;  |
| 4  |  &nbsp;&nbsp;&nbsp;2&nbsp;&nbsp;&nbsp;  |
| 5  |  &nbsp;&nbsp;&nbsp;1&nbsp;&nbsp;&nbsp;  |
| 6  |  &nbsp;&nbsp;&nbsp;2&nbsp;&nbsp;&nbsp;  |
| 7  |  &nbsp;&nbsp;&nbsp;2&nbsp;&nbsp;&nbsp;  |
+----+-----+
例如，给定上面的 Logs 表， 1 是唯一连续出现至少三次的数字。

+-------------------------+
| ConsecutiveNums |
+-------------------------+
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
+-------------------------+


## 0x1 解题思路

这道题也可以采用局部变量的方式求解。具体思路就是我们判断当前值和上一个值是否相等,如果相等,则对应的count计数加1。否则从1开始计数。

这道题我们要先完成对局部变量的赋值,将上述操作放在一个子查询里,在计算完所有的count后,首先使用`where`筛选出出现次数大于等于3的值,然后再使用`distinct`对`Num`进行去重。

注意,这里有一个新的知识点:

>**使用>代替>=,因为>=会使索引失效!!!**

## 0x2 代码实现

``` sql
select distinct Num as ConsecutiveNums
from(
    select Num,
    case
        when @preValue=Num then @count:=@count+1
        when (@preValue:=Num) is not null then @count:=1
    end as `count` 
    from `Logs`,(select @count:=null,@preValue:=null) as init
) as countTable
#先执行where,再distinct
where `count`>2;

```
## 0x3 课后总结

养好用>而不用>=的习惯，因为>=会让索引失效。