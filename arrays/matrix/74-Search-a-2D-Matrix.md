---
title: 74. 搜索二维矩阵
mathjax: true
data: 2020-05-21 14:22:11
updated:
tags:
- [数组,二分法]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/search-a-2d-matrix/)
编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：

每行中的整数从左到右按升序排列。
每行的第一个整数大于前一行的最后一个整数。
示例 1:


输入:
matrix = [
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
target = 3
输出: true

二分法，将一维的下标转为二维的，index/width=横坐标，index%width=纵坐标