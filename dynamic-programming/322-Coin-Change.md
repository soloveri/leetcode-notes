---
title: 322. 零钱兑换
mathjax: true
data: 2020-08-20 20:30:36
updated:
tags:
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/coin-change)

---

### 0x0 题目详情

>给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

---

**测试用例:**

>示例 1:
输入: coins = [1, 2, 5], amount = 11
输出: 3 
解释: 11 = 5 + 5 + 1

>示例 2:
输入: coins = [2], amount = 3
输出: -1
 
>说明:
你可以认为每种硬币的数量是无限的。



### 0x1 解题思路

**思路一:**

这道题暴力非常简单,对于一种面值的硬币,我们有三种选择:

- 不使用该面值
- 使用该面值的硬币一次
- 使用该面值的硬币多次

那么当我们的余额为0时就表示已经无需计算了。暴力递归思路简单,但时间爆炸。按照这样的思路改成dp时间还是爆炸。

**思路二:**

采用贪心加递归的思路。既然要求最少的硬币数,那么我们就从最大的硬币开始找零,这个应该很容易懂。当然还需要剪枝,剪枝思路看实现代码。


### 0x2 代码实现

递归实现的代码比较简单了,这里就不写了。

贪心:递归函数的输入参数`count`的含义是,对于前面已经成功找零的余额选择了`count`个硬币。在递归函数中会更新最终结果`res`,所以无返回值。

``` java
class Solution {
    int res = Integer.MAX_VALUE;
    public int coinChange(int[] coins, int amount){
        if(amount==0){
            return 0;
            }
        Arrays.sort(coins);
        mincoin(coins,amount,coins.length-1,0);
        return res==Integer.MAX_VALUE? -1:res;
    }
    private void mincoin(int[] coins,int amount, int index, int count){
        if(amount==0){
            res = Math.min(res,count);
            return;
        }
        if(index<0){
            return;
        }
        for(int i = amount/coins[index];i>=0 && i+count<res; i--){
            mincoin(coins,amount - (i*coins[index]), index-1, count+i);
        }
    }
}

```

### 0x3 课后总结

不看答案我能做出来?