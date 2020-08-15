---
title: 279. 完全平方数
mathjax: true
data: 2020-08-15 19:33:27
updated:
tags:
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/perfect-squares/)

---

### 0x0 题目详情

给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。

### 0x1 解题思路

我一开始思考的是将n每次都分为两个数n1和n2,再递归判断n1和n2拆解后的最小平方个数,但是时间爆炸。

后来采取评论区的一种思路:
我们将n每次减去一个平方数,查看剩余值需要分解为多少个平方数,速度提升很明显。因为少判断了左边的数需要拆解的个数。

dp数组的初始化比较简单,但是仍需注意,应该将dp[i]初始化为i,因为对于要给数i,最多拆解成i个1。

### 0x2 代码实现

原始思路,将n拆解为两个数,注释部分为原始递归代码:

``` java
class Solution {
    public int numSquares(int n) {
        if(n<2){
            return 1;
        }
        return recur(n);

    }
    //返回将number拆成完全平凡数需要的个数
    private int recur(int number){
        int[] dp=new int[number+1];
        dp[0]=1;
        dp[1]=1;
        for(int i=2;i<=number;i++){
            int n=i;
            for(int j=1;n>0;j+=2){
                n-=j;
            }
            if(n==0){
                dp[i]=1;
                continue;
            }
            dp[i]=Integer.MAX_VALUE;
            for(int j=1;j<=i/2;j++){
                dp[i]=Math.min(dp[i],dp[j]+dp[i-j]);
            }
        }
        return dp[number];
        // if(number<2){
        //     return 1;
        // }
        
        // for(int i=1;n>0;i+=2){
        //     n-=i;
        // }
        // if(n==0){
        //     return 1;
        // }
        // int result=Integer.MAX_VALUE;
        // for(int i=1;i<=number/2;i++){
        //     result=Math.min(result,recur(i)+recur(number-i));
        // }
        // return result;
    }
}

```

---

优化代码:

``` java
class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n + 1]; // 默认初始化值都为0
        for (int i = 1; i <= n; i++) {
            dp[i] = i; // 最坏的情况就是每次+1
            for (int j = 1; i - j * j >= 0; j++) { 
                dp[i] = Math.min(dp[i], dp[i - j * j] + 1); // 动态转移方程
            }
        }
        return dp[n];
    }
}
```

### 0x3 课后总结

经典题目,熟悉技巧吧。其实还有一种使用数学公式的方法,因为一般都想不到,这里就不做过多描述。