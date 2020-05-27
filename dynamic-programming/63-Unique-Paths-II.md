---
title: 63. 不同路径 II
mathjax: true
data: 2020-05-27 14:22:39
updated:
tags:
- [递归，动态规划]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/unique-paths-ii/)


### 0x1 题目详情

>一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。
机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）(PS:这里没放图，简而言之就是从一个矩形的左上角走到右下角，求不同的路径)。
现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？
说明：m 和 n 的值均不超过 100。

---

**测试用例:**
>示例 1:
输入:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
输出: 2
解释:
3x3 网格的正中间有一个障碍物。
从左上角到右下角一共有 2 条不同的路径：
a. 向右 -> 向右 -> 向下 -> 向下
b. 向下 -> 向下 -> 向右 -> 向右

### 0x2 解题思路

这道题就是第62题[求不同路径](62-Unique-Paths.md)的翻版，不同点就是路径中可能有障碍，导致无法到达。所以没什么好说的，直接把62题的代码拿过来套用就好了，只不过求每个状态时需要判断当前位置是否有障碍物。

### 0x3 代码实现

``` java "递归实现"
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if(obstacleGrid== null || obstacleGrid.length==0){
            return 0;
        }
        if(obstacleGrid[obstacleGrid.length-1][obstacleGrid[0].length-1]==1){
            return 0;
        }
        return recur(obstacleGrid,0,0);

    }
    //当前的row和col对标于网格中的索引
    //递归的含义是从当前位置走到目标位置成功的路径
    int recur(int[][] obstacleGrid,int row,int col){
        int[][] dp=new int[obstacleGrid.length][obstacleGrid[0].length];
        int m=obstacleGrid.length-1;
        int n=obstacleGrid[0].length-1;
        dp[m][n]=1;
        for(int i=n-1;i>=0;i--){
            if(obstacleGrid[m][i]==1){
                continue;
            }
            dp[m][i]=dp[m][i+1];
        }
        for(int i=m-1;i>=0;i--){
            if(obstacleGrid[i][n]==1){
                continue;
            }
            dp[i][n]=dp[i+1][n];
        }
        for(int i=m-1;i>=0;i--){
            for(int j=n-1;j>=0;j--){
                if(obstacleGrid[i][j]==1){
                    continue;
                }
                dp[i][j]+=dp[i+1][j];
                dp[i][j]+=dp[i][j+1];
            }
        }
        return dp[0][0];
        // if(row>=obstacleGrid.length || col>=obstacleGrid[0].length){
        //     return 0;
        // }
        // if(obstacleGrid[row][col]==1){
        //     return 0;
        // }
        // if(row==obstacleGrid.length-1 && col==obstacleGrid[0].length-1){
        //     return 1;
        // }
        // //现在是既没到达目标位置，也没有障碍物
        // int result=0;
        // result+=recur(obstacleGrid,row+1,col);
        // result+=recur(obstacleGrid,row,col+1);
        // return result;


    }
}

```

---

动态规划版本，时间复杂度为$O(m*n)$。

``` java "动态规划版本"
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if(obstacleGrid== null || obstacleGrid.length==0){
            return 0;
        }
        if(obstacleGrid[obstacleGrid.length-1][obstacleGrid[0].length-1]==1){
            return 0;
        }
        return recur(obstacleGrid,0,0);

    }
    //当前的row和col对标于网格中的索引
    //递归的含义是从当前位置走到目标位置成功的路径
    int recur(int[][] obstacleGrid,int row,int col){
        int[][] dp=new int[obstacleGrid.length][obstacleGrid[0].length];
        int m=obstacleGrid.length-1;
        int n=obstacleGrid[0].length-1;
        dp[m][n]=1;
        //同样提前填充了表，保证了填充一般位置时，下标不会越界
        //极大的提高了效率
        for(int i=n-1;i>=0;i--){
            if(obstacleGrid[m][i]==1){
                continue;
            }
            dp[m][i]=dp[m][i+1];
        }
        for(int i=m-1;i>=0;i--){
            if(obstacleGrid[i][n]==1){
                continue;
            }
            dp[i][n]=dp[i+1][n];
        }
        for(int i=m-1;i>=0;i--){
            for(int j=n-1;j>=0;j--){
                //这里的下标必然不会越界，因为上面已经提前填充过表了
                if(obstacleGrid[i][j]==1){
                    continue;
                }
                dp[i][j]+=dp[i+1][j];
                dp[i][j]+=dp[i][j+1];
            }
        }
        return dp[0][0];
    }
}

```

这里的空间仍然可以优化，可将二维数组优化为一维数组。如下所示：

---

``` java "动态规划优化空间"
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if(obstacleGrid== null || obstacleGrid.length==0){
            return 0;
        }
        if(obstacleGrid[obstacleGrid.length-1][obstacleGrid[0].length-1]==1){
            return 0;
        }
        return recur(obstacleGrid,0,0);

    }
    //当前的row和col对标于网格中的索引
    //递归的含义是从当前位置走到目标位置成功的路径
    int recur(int[][] obstacleGrid,int row,int col){
        int m=obstacleGrid.length-1;
        int n=obstacleGrid[0].length-1;
        int[] dp=new int[n+1];
        dp[n]=1;
        for(int i=n-1;i>=0;i--){
            if(obstacleGrid[m][i]==1){
                continue;
            }
            dp[i]=dp[i+1];
        }

        for(int i=m-1;i>=0;i--){
            for(int j=n;j>=0;j--){
                if(obstacleGrid[i][j]==1){
                    dp[j]=0;
                    continue;
                }
                if(j+1>n){
                    continue;
                }
                dp[j]+=dp[j+1];
            }
        }
        return dp[0];
    }
}

```

### 0x4 课后总结

这道题就是62题的翻版，会62题，这题必会，不会的再去体验一下62题。