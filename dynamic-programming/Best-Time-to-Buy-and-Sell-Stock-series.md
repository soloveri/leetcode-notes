---
title: 27. 移除元素
mathjax: true
data: 2020-06-17 22:31:00
difficulty: hard
updated:
tags: 动态规划
categories: algorithm
---

本篇是股票问题的合集，股票问题总计6道，文章预计很长。

[121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)
[122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)
[123. 买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)
[188. 买卖股票的最佳时机 IV](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)
[309. 最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)
[714. 买卖股票的最佳时机含手续费](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)


首先来看看这六道题的共同点是什么：

- 只能先买后卖
- 有次数限制

![股票状态图](images/Best-Time-to-Buy-and-Sell-Stock-series.drawio.svg)

由上图可知，股票买入与卖出之间有四种状态状态，所以在一天之中，我们有两种操作，即买入与卖出。

并且还有次数限制，所以这六道题的状态都可以用一个三维数组表示。

`dp[i][k][opreation]`,第一维表示当前所在的天数，第二维表示当前的交易次数，第三位opreation表示是卖出还是买入。**并且我们需要遍历所有的状态才能够得出最终答案**。

所以状态方程可以总结如下：

> dp[i][k][0]=Math.max(dp[i-1][k][0],dp[i-1][k][1]+prices[i])

第i天不持有股票的最大收益来自前一天不持有股票的收益或者来自今天把所持有的股票卖了的收益。

>dp[i][k][1]=Math.max(dp[i-1][k][1],dp[i-1][k-1][0]-prices[i])

第i天持有股票的最大收益来自前一天持有股票的收益或者来自今天买入股票后的收益。

k的值范围为0~k,总共k+1个数。因为当前的交易次数可以为0次、1次...k次。
我们把买入卖出两天作为一次交易记录，所以我们仅仅在求`dp[i][k][1]`的收益时，在买入时将k减1，在卖股票时交易次数不用改变，因为本次交易的次数在买入时已经计算过了。

最后返回dp[prices.length][k][0]，因为交易次数达到k，且不持有股票的利润才会是最大的。

### 121. 买卖股票的最佳时机

#### 0x0 题目详情

>给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
如果你最多只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润。
注意：你不能在买入股票前卖出股票。

---

**测试用例:**

>示例 1:
输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。

#### 0x1 解题思路

这道题套用上述的dp公式，因为只允许1次交易，所以求dp数组的代码如下：

``` java

    dp[0][1]=-prices[0];

    for(int i=1;i<prices.length;i++){
        for(int j=1;j<=k;j++){
            dp[i][j][0]=Math.max(dp[i-1][j][0],dp[i-1][j][1]+prices[i]);
            dp[i][j][1]=Math.max(dp[i-1][1],dp[i-1][j-1][0]-prices[i]);
        }
    }
```

仔细观察，上述代码的内层循环只会执行一次，也就是当j=1时，那么交易次数为0时不持有的股票时的利润是多少呢？是0，即dp[0][0][1]=0，因为次数为0，不可能有买入操作，利润必须为0，即使当前状态为持有股票状态。所以我们就可以把内层循环去掉。并且因为dp数组需要初始化第0天的数据(也就是第一天)，第一天不持有股票的最大利润就是0，因为什么也不做。第一天持有的股票的最大利润是`-prices[i]`。所以最终代码如下。

#### 0x2 代码实现

下面代码可以把dp数组优化掉，所注释掉的代码就是未优化前的原始代码。

``` java
 public int maxProfit(int[] prices) {
        if(prices==null || prices.length==0){
            return 0;
        }
        int[][] dp=new int[prices.length][2];
        dp[0][1]=-prices[0];
        // for(int i=1;i<prices.length;i++){
        //     dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
        //     dp[i][1]=Math.max(dp[i-1][1],dp[i-1][0]-prices[i]);
        // }
        int preSold=0;
        int preBuy=-prices[0];
        for(int i=1;i<prices.length;i++){
            preSold=Math.max(preSold,preBuy+prices[i]);
            preBuy=Math.max(preBuy,-prices[i]);
        }
        // return dp[prices.length-1][0];
        return preSold;
    }
```

### 122. 买卖股票的最佳时机 II

#### 0x0 题目详情

>给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。
注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

---

**测试用例:**

>示例 1:
输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 

#### 0x1 解题思路

这道题和第121题唯一的差别就是这道题可以交易无限次。那么代码仍然按照最原始dp公式写，如下：

``` java
    dp[0][1]=-prices[0];
    for(int i=1;i<prices.length;i++){
        for(int j=1;j<=k;j++){
            dp[i][j][0]=Math.max(dp[i-1][j][0],dp[i-1][j][1]+prices[i]);
            dp[i][j][1]=Math.max(dp[i-1][1],dp[i-1][j-1][0]-prices[i]);
        }
    }
```

那么上面代码中的k到底是多少，答案是正无穷。那么我们干脆就去掉k这一维的信息，因为对状态转换没有作用了，就跟没有一个样。

#### 0x2 代码实现

``` java
public int maxProfit(int[] prices) {
    if(prices==null || prices.length==0){
        return 0;
    }
    int[][] dp=new int[prices.length][2];

    dp[0][1]=-prices[0];
    for(int i=1;i<prices.length;i++){
        dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
        dp[i][1]=Math.max(dp[i-1][1],dp[i-1][0]-prices[i]);
    }
    return dp[prices.length-1][0];
}
```

可以看到和121题差别细微。仅仅是去除了一维的无用的信息。

### 123. 买卖股票的最佳时机 III

#### 0x0 题目详情

>给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。
设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。
注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

---

**测试用例:**

>示例 1:
输入: [3,3,5,0,0,3,1,4]
输出: 6
解释: 在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。
随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。

#### 0x1 解题思路

这道题与122题的差别就是有交易次数限制了，只能限制两次交易，我们仍然用最原始的代码来实现：

``` java
    for(int i=1;i<prices.length;i++){
        for(int j=1;j<=k;j++){
            dp[i][j][0]=Math.max(dp[i-1][j][0],dp[i-1][j][1]+prices[i]);
            dp[i][j][1]=Math.max(dp[i-1][1],dp[i-1][j-1][0]-prices[i]);
        }
    }
```

上述代码中循环处理没有什么问题，因为遍历了所有的状态。但是dp数组的初始化是一个值得注意的问题。在第0天，不持有的股票的最大利润为0。持有股票的最大利润为`-prices[i]`，无论当前交易次数为多少。

所以需要使用一个循环来处理所有交易次数的情况。

#### 0x2 代码实现

``` java
public int maxProfit(int[] prices) {
        if(prices==null || prices.length==0){
            return 0;
        }
        int[][][] dp=new int[prices.length][3][2];
        //因为就两次交易限制，可以直接手写，当次数限制多了，就得使用循环了，交易次数为0时没有交易，所以dp[0][0][1]=0,即最大利润为0，因为当交易次数为
        //0时不可能有买入的操作
        dp[0][1][1]=-prices[0];
        dp[0][2][1]=-prices[0];
        for(int i=1;i<prices.length;i++){
            for(int j=1;j<3;j++){
                dp[i][j][0]=Math.max(dp[i-1][j][0],dp[i-1][j][1]+prices[i]);
                dp[i][j][1]=Math.max(dp[i-1][j][1],dp[i-1][j-1][0]-prices[i]);
            }
        }
        return dp[prices.length-1][2][0];

    }
```

### 188. 买卖股票的最佳时机 IV

#### 0x0 题目详情

>给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。
设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。
注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

---

**测试用例:**

>示例 1:
输入: [2,4,1], k = 2
输出: 2
解释: 在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2 。

#### 0x1 解题思路

这道题与上一题没有本质区别，唯一的变化就是交易次数由定值2变为变量k。但是当k非常大时，dp数组非常大，会超时。所以思考一下这个k的意义。k代表交易次数。两天代表一次。所以如果k>prices.length/2时，就表示没有交易次数限制了，因为最大交易次数覆盖的天数已经超过prices的长度了，就跟米有限制一样。在这种情况下，代码就跟122题一样了。

#### 0x2 代码实现

``` java
public int maxProfit(int k, int[] prices) {
        if(prices==null || prices.length==0){
            return 0;
        }
        if(k<=prices.length/2){
            int[][][] dp=new int[prices.length][k+1][2];
            //对第0天的数据进行初始化，需要对持有股票的每一个交易次数状态初始化
            for(int i=1;i<=k;i++){
                dp[0][i][1]=-prices[0];
            }
            for(int i=1;i<prices.length;i++){
                for(int j=1;j<=k;j++){
                    dp[i][j][0]=Math.max(dp[i-1][j][0],dp[i-1][j][1]+prices[i]);
                    dp[i][j][1]=Math.max(dp[i-1][j][1],dp[i-1][j-1][0]-prices[i]);
                }
            }
            return dp[prices.length-1][k][0];
        }
        //没有次数限制的状态
        else{
            int[][] dp=new int[prices.length][2];
            dp[0][1]=-prices[0];
            for(int i=1;i<prices.length;i++){
                dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
                dp[i][1]=Math.max(dp[i-1][1],dp[i-1][0]-prices[i]);
            }
            return dp[prices.length-1][0];
        }
    }
```

但是当k非常大时，这种解决方法仍有可能超时所以不完美。

### 309. 最佳买卖股票时机含冷冻期

#### 0x0 题目详情

>给定一个整数数组，其中第 i 个元素代表了第 i 天的股票价格 。​
设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:
你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

---

**测试用例:**
>示例:
输入: [1,2,3,0,2]
输出: 3
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]

#### 0x1 解题思路

这道题跟122题也非常类似，但是加入了冷冻期这么一个概念。

dp[i][1]可由两种情况得到：【i-1天本来就持有股票】和【i-1天没有股票，是今天进行了买入操作】。后者dp[i-1][0] - prices[i]由于是今天(i天)进行了买入操作，那么i-1天没有股票的原因就一定【不能是】i-1天进行了卖出操作（因为冷冻期）。即i-1天没有股票的原因一定是因为i-2天就没有股票。因此直接用dp[i-2][0]表示，就可以避免dp[i-1][0]中可能包含的上述违禁操作。

#### 0x2 代码实现

``` java
public int maxProfit(int[] prices) {
         if(prices==null || prices.length==0){
            return 0;
        }
        int[][] dp=new int[prices.length][2];

        dp[0][1]=-prices[0];
        for(int i=1;i<prices.length;i++){
            dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
            //因为越界后最大利润都为0，所以直接为-prices[i]
            if(i-2<0){
                dp[i][1]=Math.max(dp[i-1][1],-prices[i]);
                continue;
            }
            dp[i][1]=Math.max(dp[i-1][1],dp[i-2][0]-prices[i]);
        }
        return dp[prices.length-1][0];

    }
```

### 714. 买卖股票的最佳时机含手续费

#### 0x0 题目详情

>给定一个整数数组 prices，其中第 i 个元素代表了第 i 天的股票价格 ；非负整数 fee 代表了交易股票的手续费用。
你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。
返回获得利润的最大值。
注意：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。

---

**测试用例:**

>示例 1:
输入: prices = [1, 3, 2, 8, 4, 9], fee = 2
输出: 8
解释: 能够达到的最大利润:  
在此处买入 prices[0] = 1
在此处卖出 prices[3] = 8
在此处买入 prices[4] = 4
在此处卖出 prices[5] = 9
总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.

#### 0x1 解题思路

这道题与122题没有什么区别，就是卖股票的时候利润需要减去手续费而已。

#### 0x2 代码实现

``` java
public int maxProfit(int[] prices, int fee) {
         if(prices==null || prices.length==0){
            return 0;
        }
        int[][] dp=new int[prices.length][2];

        dp[0][1]=-prices[0];
        for(int i=1;i<prices.length;i++){
            //卖股票时减去了手续费
            dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]-fee);
            dp[i][1]=Math.max(dp[i-1][1],dp[i-1][0]-prices[i]);
        }
        return dp[prices.length-1][0];
    }
```

### 课后总结

可以看到这六道题所用的dp公式都是一样的，差别基本上都是在允许的交易次数这一点。这一组题基本上没什么问题了。一个三维数组解决所有的状态。

其中有一点，我们可以注意，当原先设立的变量对状态转换没什么用时，可以直接将该变量去掉。