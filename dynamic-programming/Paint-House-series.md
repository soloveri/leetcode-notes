---
title: 粉刷房子系列
mathjax: true
data: 2020-08-14 18:39:47
updated:
tags:
- 动态规划
categories:
- algorithm
---

## 前言

这道题可以分为两个版本,版本I简单,版本II当然是I的升级版了。这里放在一起方便学习。

[粉刷房子-I](https://leetcode-cn.com/problems/paint-house)

[粉刷房子-II](https://leetcode-cn.com/problems/paint-house-ii)

---

## 1.粉刷房子-I

### 0x0 题目详情

>假如有一排房子，共 n 个，每个房子可以被粉刷成红色、蓝色或者绿色这三种颜色中的一种，你需要粉刷所有的房子并且使其相邻的两个房子颜色不能相同。
当然，因为市场上不同颜色油漆的价格不同，所以房子粉刷成不同颜色的花费成本也是不同的。每个房子粉刷成不同颜色的花费是以一个 n x 3 的矩阵来表示的。
例如，costs[0][0] 表示第 0 号房子粉刷成红色的成本花费；costs[1][2] 表示第 1 号房子粉刷成绿色的花费，以此类推。请你计算出粉刷完所有房子最少的花费成本。

>注意：
所有花费均为正整数。

---

**测试用例:**

>示例：
输入: \[[17,2,17],[16,16,5],[14,3,19]]
输出: 10
解释: 将 0 号房子粉刷成蓝色，1 号房子粉刷成绿色，2 号房子粉刷成蓝色。最少花费: 2 + 5 + 3 = 10。


### 0x1 解题思路

这道题的思路简直显而易见,不愧为简单题。对于每一个房子只能刷3种颜色,但是相连的房子不能刷相同的颜色。那么我们就可以设立一个二维的dp数组。`dp\[i]\[j]`表示刷到第`i`个房子第`j`种颜色时,总的花费是多少。

那么递推公式已经很明显了,这道题只有3种颜色,那么当我们刷到第`i`间房子时,假设刷第`j`种颜色。那么最小的花费就是当前房子的花费`cost[i][j]`加上`min(dp[i-1][j_1],dp[i-1][j_2])`,这里`j_1`、和`j_2`表示另外两种颜色的序号。原理就是当前房子`i`的刷的颜色上一个房子`i-1`不能刷,只能刷另外两个颜色。

唯一要注意的是当我们取第`j`种颜色时,另外两种颜色的下标怎么取。我这里采用的是`(j+1)%length`和`(j+2)%length`表示另外两种颜色。

### 0x2 代码实现

``` java
class Solution {
    public int minCost(int[][] costs) {
        if(costs==null || costs.length==0 || costs[0].length==0){
            return 0;
        }
        //dp[i][j]表示刷到第i间房子第j种颜色时，需要花费的总费用
        int[][] dp=new int[costs.length][3];
        for(int i=0;i<3;i++){
            dp[0][i]=costs[0][i];
        }
        int length=costs[0].length;
        for(int i=1;i<costs.length;i++){
            for(int j=0;j<3;j++){
                dp[i][j]=Math.min(dp[i-1][(j+1)%length],dp[i-1][(j+2)%length])+costs[i][j];
            }
        }
        return Math.min(dp[costs.length-1][0],Math.min(dp[costs.length-1][1],dp[costs.length-1][2]));
    }
}
```

## 2. 粉刷房子-II

### 0x0 题目详情

>假如有一排房子，共 n 个，每个房子可以被粉刷成 k 种颜色中的一种，你需要粉刷所有的房子并且使其相邻的两个房子颜色不能相同。
当然，因为市场上不同颜色油漆的价格不同，所以房子粉刷成不同颜色的花费成本也是不同的。每个房子粉刷成不同颜色的花费是以一个 n x k 的矩阵来表示的。
例如，costs[0][0] 表示第 0 号房子粉刷成 0 号颜色的成本花费；costs[1][2] 表示第 1 号房子粉刷成 2 号颜色的成本花费，以此类推。请你计算出粉刷完所有房子最少的花费成本。

>注意：
所有花费均为正整数。

>进阶：
您能否在 O(nk) 的时间复杂度下解决此问题？

---

**测试用例:**

>示例：
输入: \[[1,5,3],[2,9,4]]
输出: 5
解释: 将 0 号房子粉刷成 0 号颜色，1 号房子粉刷成 2 号颜色。最少花费: 1 + 4 = 5; 
或者将 0 号房子粉刷成 2 号颜色，1 号房子粉刷成 0 号颜色。最少花费: 3 + 2 = 5. 

### 0x1 解题思路

这道题比第一题难就难在进阶部分,在O(nk)时间复杂度下完成才是这道题的正确方式。

关键在于当我们粉刷第`i`个房子第`j`种颜色时,如何得知第`i-1`个房子除第`j`个颜色外,其他颜色中的最小花费。遍历除第`j`种颜色外的所有花费获取最小值是第一题的方式,我们不予考虑。

这里采用的方法时:我们维护第`i-1`个房子所有颜色中的最小花费值`min`与第二小花费值`ndMin`。这里要设置第二小的原因时如果第`i-1`个房子最小花费值的颜色和我们当前要求的颜色重合,那么就违规了。这时只能采用第`i-1`个房子第二小花费的颜色表示`i`前面所有房子的花费。

剩下的就是填充数组,跟第一题的方式差不多。

### 0x2 代码实现

``` java
class Solution {
    public int minCostII(int[][] costs) {
        if(costs==null || costs.length==0 || costs[0].length==0){
            return 0;
        }
        int result=Integer.MAX_VALUE;
        //注意测试用例有可能是只有一种颜色
        if(costs.length<2){
            for(int i=0;i<costs[0].length;i++){
                result=Math.min(result,costs[0][i]);
            }
            return result;
        }
        //k必须大于1
        int[][] dp=new int[costs.length][costs[0].length];
        int length=costs[0].length;
        int min=0;
        int ndMin=1;
        for(int i=0;i<length;i++){
            dp[0][i]=costs[0][i];
            if(dp[0][i]<costs[0][min]){
                ndMin=min;
                min=i;
            //这里要设置i！=min的原因时防止当前的dp[0][i]表示的是最小花费,而不是
            //第二小的花费
            }else if(dp[0][i]<costs[0][ndMin]&& i!=min){
                ndMin=i;
            }
        }

        for(int i=1;i<costs.length;i++){
            int currentMin=0;
            int currentNdMin=1;
            for(int j=0;j<length;j++){
                if(j!=min){
                    dp[i][j]=costs[i][j]+dp[i-1][min];
                }else{
                    dp[i][j]=costs[i][j]+dp[i-1][ndMin];
                }//保存最小值

                if(dp[i][j]<dp[i][currentMin]){
                    currentNdMin=currentMin;
                    currentMin=j;
                //同样为了防止当前dp[i][j]是最小花费,因为i是从0开始的
                //有可能第一个值就是最小值
                }else if(dp[i][j]<dp[i][currentNdMin] && j!=currentMin){
                    currentNdMin=j;
                }
            }
            min=currentMin;
            ndMin=currentNdMin;     
        }
        int reuslt=Integer.MAX_VALUE;
        for(int i=0;i<length;i++){
            reuslt=Math.min(reuslt,dp[costs.length-1][i]);
        }
        return reuslt;
    }
}
```

## 3. 课后总结

第二题的优化方式是难点。
