---
title: 276. 栅栏涂色
mathjax: true
data: 2020-08-15 18:52:08
updated:
tags:
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/paint-fence/)

---

### 0x0 题目详情

>有 k 种颜色的涂料和一个包含 n 个栅栏柱的栅栏，每个栅栏柱可以用其中一种颜色进行上色。
你需要给所有栅栏柱上色，并且保证其中相邻的栅栏柱 最多连续两个 颜色相同。然后，返回所有有效涂色的方案数。

>注意:
n 和 k 均为非负的整数。


### 0x1 解题思路

对于一个栏杆,我们可以选择和前一个栏杆的同一个颜色,也可以选择不同的颜色。并且涂什么颜色和前一个栏杆的颜色息息相关。

这是不是有点像[买卖股票问题](Best-Time-to-Buy-and-Sell-Stock-series.md)?对于一支股票我们可以选择买或者卖,但是卖的基础一定得是我们已经买了,而买的基础是我们得持有股票,这同样与前一天的是否持有股票相关。

所以与股票问题类似,我们对栏杆的颜色定义两个状态:`0`表示与前一个栏杆涂不同颜色,`1`表示与前一个栏杆涂相同颜色。那么如果我们选择对栏杆`i`涂不同颜色,总共的颜色方案为应该为前一个栏杆的方案总数乘以`k-1`,因为与前一个栏杆颜色不同,只剩下了`k-1`个颜色可以选。那么如果对栏杆`i`涂与前一根栏杆相同的颜色呢?这种情况下,栏杆`i`允许的方案取决于栏杆`i-1`有多少涂色方案。那么应该选择`i-1`的哪个状态呢?应该是`0`状态。因为`i-1`的`1`状态不取决于`i-1`能涂的颜色方案,它取决于栏杆`i-2`。又因为动态规划都是无后效性的,当前状态只与上一个状态有关。

在语义上解释应该是:`i-1`栏杆能自主选择涂的颜色只有`0`状态表示,`1`状态能涂的颜色数不是`i-1`能决定的。

### 0x2 代码实现

``` java
class Solution {
    public int numWays(int n, int k) {
        if(n<1 || k<1){
            return 0;
        }
        int[][] dp=new int[n][2];
        //dp[i][0]表示与前一个杆涂不同颜色
        //dp[i][1]表示与前一个杆涂相同颜色
        dp[0][0]=k;
        dp[0][1]=0;
        for(int i=1;i<n;i++){
            dp[i][1]=dp[i-1][0];
            dp[i][0]=(k-1)*(dp[i-1][0]+dp[i-1][1]);
        }
        return dp[n-1][0]+dp[n-1][1];


    }
}

```

### 0x3 课后总结

如果对于一个事物,它只有两种状态或者状态是有限的,可以枚举的,类似于股票买还不是不买。那么就能借鉴这种思路,为事物的每一次抉择分配两个或多个状态。