---
title: 64. 最小路径和
mathjax: true
data: 2020-05-29 00:24:13
updated:
tags:
- [数组,动态规划]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/minimum-path-sum/)

### 0x1 题目详情

>给定一个包含非负整数的 m x n 网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。
说明：每次只能向下或者向右移动一步。

---

>示例:
输入:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
输出: 7
解释: 因为路径 1→3→1→1→1 的总和最小。

### 0x2 解题思路

可与[62.不同路径](62-Unique-Paths.md)、[63.不同路径 II](63-Unique-Paths-II.md)一起打包使用。

显而易见，这是从左向右的单向递归模型，递归版本闭着眼睛都能写出来好了。有一点需要注意，当下标越界时，我这里返回的值是65536，表示不可达。这是我随便指定的，因为如果使用`Integer.MAX_VALUE`，与前面递归的结果一加就会越界。

递归写出来以后，就是改动态规划了。这里的空间我也懒得优化了。从二维可以优化为一维。或者干脆就直接复用题目传进来的数组，原地dp，这一点我是没想到的。

### 0x3 代码实现

``` java
class Solution {
    public int minPathSum(int[][] grid) {
        if(grid== null || grid.length==0){
            return Integer.MAX_VALUE;
        }
        return recur(grid,0,0);
    }

    //返回从当前位置[row,col]到达目的地的最短路径
    int recur(int[][] grid,int row,int col){
        if(row==grid.length-1 && col==grid[0].length-1){
            return grid[row][col];
        }
        if(row>=grid.length || col>=grid[0].length){
            return 65536;
        }
        return Math.min(grid[row][col]+recur(grid,row+1,col),
        grid[row][col]+recur(grid,row,col+1));
    }
}

```

---

时间复杂度为$O(M*N)$

``` java
class Solution {
    public int minPathSum(int[][] grid) {
        if(grid== null || grid.length==0){
            return Integer.MAX_VALUE;
        }
        return recur(grid,0,0);
    }

    //返回从当前位置[row,col]到达目的地的最短路径
    int recur(int[][] grid,int row,int col){
        int m=grid.length-1;
        int n=grid[0].length-1;
        int[][] dp=new int[m+1][n+1];
        dp[m][n]=grid[m][n];
        for(int i=n-1;i>-1;i--){
            dp[m][i]=grid[m][i]+dp[m][i+1];
        }
        for(int i=m-1;i>-1;i--){
            dp[i][n]=grid[i][n]+dp[i+1][n];
        }
        for(int i=m-1;i>-1;i--){
            for(int j=n-1;j>-1;j--){
                dp[i][j]=grid[i][j]+Math.min(dp[i+1][j],dp[i][j+1]);
            }
        }
        return dp[0][0];
    }
}
```

### 0x4 课后总结

非常简单而又经典的单向递归模型，可以与62、63题一起好好对比一下。