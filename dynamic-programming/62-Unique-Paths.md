---
title: 62. 不同路径
mathjax: true
data: 2020-05-27 14:23:10
updated:
tags:
- [递归，动态规划]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/unique-paths/)

### 0x1 题目详情

>一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。
机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。
问总共有多少条不同的路径？

---

**测试用例:**

>示例 1:
输入: m = 3, n = 2
输出: 3
解释:
从左上角开始，总共有 3 条路径可以到达右下角。
a. 向右 -> 向右 -> 向下
b. 向右 -> 向下 -> 向右
c. 向下 -> 向右 -> 向右

### 0x2 解题思路

这道题我一看吧，就是从左往右的单向递归模型。每来到一个点后，有两种方向可以选择，一种是向下，一种是向右，直到到达目标位置后，返回1，表示成功找到一条路径。否则越界后返回0。

递归的代码比较简单，所以改成动态规划也比较简单了。

### 0x3 代码实现

``` java "递归实现"
class Solution {
    //这道题我咋感觉思维定势了,不像排列组合
    //可以画路径数那样求解啊
    public int uniquePaths(int m, int n) {
        if(m<1 || n<1){
            return 0;
        }
        return recur(0,0,m,n);
    }
    //m和n是长度
    int recur(int row,int col,int m,int n){

        因为row只向右移动，所以不可能小于0
        因为col只向下移动，不可能小于0
        if(row>=m || col>=n){
            return 0;
        }
        if(row== m-1 && col== n-1){
            return 1;
        }

        //现在既不越界，也有路可走
        int result=0;
        result+=recur(row+1,col,m,n);
        result+=recur(row,col+1,m,n);
        return result;
    }
}

```

递归版本简单，但是超时，需要优化为动态规划。上面这种递归改dp简直不要太简单。

---

``` java  "动态规划"
class Solution {
    //这道题我咋感觉思维定势了,不像排列组合
    //可以画路径数那样求解啊
    public int uniquePaths(int m, int n) {
        if(m<1 || n<1){
            return 0;
        }
        return recur(0,0,m,n);

    }
    //m和n是长度
    int recur(int row,int col,int m,int n){
        //因为row和col在字面值上最大为m-1，和n-1
        int[][] dp=new int[m][n];
        dp[m-1][n-1]=1;
        //所以填表的顺序为从下到上，从右到左

        for(int i=m-1;i>=0;i--){
            for(int j=n-1;j>=0;j--){
                //这里的三重表达式耗时还是非常明显的
                dp[i][j]+=i+1>m-1?0:dp[i+1][j];
                dp[i][j]+=j+1>n-1?0:dp[i][j+1];
            }
        }
        return dp[0][0];
    }
}
```

在上述代码中的二重循环中，三重表达式看似不耗时，但是只超越了20%左右，但是我把这两句判断去掉，时间立马超越100%了。我吐了。**以后在dp填表的时候还是少用判断的语句**，能不判断就不判断，尽量通过外围条件限制。上述内嵌if都那么耗时了，何况再多次调用函数判断是否越界了。那能超越个鬼啊。

---

``` java "动态规划时间优化"
class Solution {

    public int uniquePaths(int m, int n) {
        if(m<1 || n<1){
            return 0;
        }
        return recur(0,0,m,n);
    }

    //m和n是长度
    int recur(int row,int col,int m,int n){
        //因为row和col在字面值上最大为m-1，和n-1
        int[][] dp=new int[m][n];

        //这里通过提前填表，避免了数据下标越界的判断
        for(int i=0;i<n;i++){
            dp[m-1][i]=1;
        }
        for(int i=0;i<m;i++){
            dp[i][n-1]=1;
        }

        for(int i=m-2;i>=0;i--){
            for(int j=n-2;j>=0;j--){
                //判断语句省略了，效率提升十分明显!
                dp[i][j]+=dp[i+1][j];
                dp[i][j]+=dp[i][j+1];
            }
        }
        return dp[0][0];
    }
}

```

上面的代码仍然不是最优的，空间仍然有优化空间，可将二维省略为一维数组，因为一个位置的状态只依赖其右边的状态与下一行的状态。好吧，刚优化试了一下，仍然没有达到双百...尴尬的一匹。

---

``` java "动态规划空间优化"
class Solution {
    //这道题我咋感觉思维定势了,不像排列组合
    //可以画路径数那样求解啊
    public int uniquePaths(int m, int n) {
        if(m<1 || n<1){
            return 0;
        }
        return recur(0,0,m,n);

    }
    //m和n是长度
    int recur(int row,int col,int m,int n){
        int[] dp=new int[n];  
        for(int i=0;i<n;i++){
            dp[i]=1;
        }

        for(int i=m-2;i>=0;i--){
            for(int j=n-2;j>=0;j--){
                dp[j]+=dp[j+1];
            }
        }
        return dp[0];
    }
}

```

### 0x4 课后总结

我一直在想到底如何区分关于这种从左向右(或从右向左)的单向递归模型与求组合遍历递归模型的区别。到底合适判定用哪种？

但是我现在至少确定了一点，使用组合遍历式的递归模型的一般都要求相关的排列组合。每个数据在每一层递归时都可以考虑。