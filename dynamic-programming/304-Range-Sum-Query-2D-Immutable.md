---
title: 304. 二维区域和检索 - 矩阵不可变
mathjax: true
data: 2020-08-17 19:14:22
updated:
tags:
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/range-sum-query-2d-immutable)

---

### 0x0 题目详情

给定一个二维矩阵，计算其子矩形范围内元素的总和，该子矩阵的左上角为 (row1, col1) ，右下角为 (row2, col2)。

![](images/304.png)

上图子矩阵左上角 (row1, col1) = (2, 1) ，右下角(row2, col2) = (4, 3)，该子矩形内元素的总和为 8。

---

**测试用例:**

>示例:
给定 matrix = [
  [3, 0, 1, 4, 2],
  [5, 6, 3, 2, 1],
  [1, 2, 0, 1, 5],
  [4, 1, 0, 1, 7],
  [1, 0, 3, 0, 5]
]

sumRegion(2, 1, 4, 3) -> 8
sumRegion(1, 1, 2, 2) -> 11
sumRegion(1, 2, 2, 4) -> 12

>说明:
你可以假设矩阵不可变。
会多次调用 sumRegion 方法。
你可以假设 row1 ≤ row2 且 col1 ≤ col2。


### 0x1 解题思路

这道题非常简单,重点是我们需要注意到`sumRegion`会调用多次。所以我们就不能在`sumRegin`里面求区域和。要把矩阵的每个`[i,j]`为右下角的子矩阵和求好。

dp\[i]\[j]含义是以`[i,j]`为右下角的子矩阵和。所以目标区域和就是`dp[i][j]`减去左上角子矩阵面积、上方子矩阵面积、右方子矩阵面积。


### 0x2 代码实现

``` java
class NumMatrix {
    int[][] dp;
    public NumMatrix(int[][] matrix) {
        if(matrix==null || matrix.length==0|| matrix[0].length==0){
            return;
        }
        
        dp=new int[matrix.length+1][matrix[0].length+1];
        int width=matrix[0].length;
        int height=matrix.length;
        for(int i=1;i<=height;i++){
            for(int j=1;j<=width;j++){
                dp[i][j]=dp[i-1][j]-dp[i-1][j-1]+dp[i][j-1]+matrix[i-1][j-1];
            }
        }
        
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        int sum=dp[row2+1][col2+1];
        
        int left=dp[row1][col1];
        int top=dp[row1][col2+1];
        int down=dp[row2+1][col1];
        return sum-top-down+left;
    }
}

/**
 * Your NumMatrix object will be instantiated and called as such:
 * NumMatrix obj = new NumMatrix(matrix);
 * int param_1 = obj.sumRegion(row1,col1,row2,col2);
 */
```

### 0x3 课后总结

需要注意题目中的一些限制与提示。