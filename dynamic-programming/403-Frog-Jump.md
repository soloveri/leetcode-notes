---
title: 403. 青蛙过河
mathjax: true
data: 2020-08-25 20:27:33
updated:
tags:
- 动态规划
- 递归
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/frog-jump)

---

### 0x0 题目详情

>一只青蛙想要过河。 假定河流被等分为 x 个单元格，并且在每一个单元格内都有可能放有一石子（也有可能没有）。 青蛙可以跳上石头，但是不可以跳入水中。
给定石子的位置列表（用单元格序号升序表示）， 请判定青蛙能否成功过河（即能否在最后一步跳至最后一个石子上）。 开始时， 青蛙默认已站在第一个石子上，并可以假定它第一步只能跳跃一个单位（即只能从单元格1跳至单元格2）。
如果青蛙上一步跳跃了 k 个单位，那么它接下来的跳跃距离只能选择为 k - 1、k 或 k + 1个单位。 另请注意，青蛙只能向前方（终点的方向）跳跃。

>请注意：
石子的数量 ≥ 2 且 < 1100；
每一个石子的位置序号都是一个非负整数，且其 < 231；
第一个石子的位置永远是0。

---

**测试用例:**

>示例 1:
\[0,1,3,5,6,8,12,17]
总共有8个石子。
第一个石子处于序号为0的单元格的位置, 第二个石子处于序号为1的单元格的位置,
第三个石子在序号为3的单元格的位置， 以此定义整个数组...
最后一个石子处于序号为17的单元格的位置。

>返回 true。即青蛙可以成功过河，按照如下方案跳跃： 
跳1个单位到第2块石子, 然后跳2个单位到第3块石子, 接着 
跳2个单位到第4块石子, 然后跳3个单位到第6块石子, 
跳4个单位到第7块石子, 最后，跳5个单位到第8个石子（即最后一块石子）。

>示例 2:
\[0,1,2,3,4,8,9,11]
返回 false。青蛙没有办法过河。 
这是因为第5和第6个石子之间的间距太大，没有可选的方案供青蛙跳跃过去。


### 0x1 解题思路

这道题一直有一个隐藏的点,石头`i`最多只能跳`i+1`的距离。

对于一个位置`i`,假设我们找到一个位置`j`,其中从`0`跳到`j`没有问题。那么能不能从`0`跳到`i`就取决于能不能`j`跳到`i`。那么如何取决?

就看从`j`到`i`的距离`distance`。如果`j`能跳`distance`的距离,那么`i`就能跳`distance-1`、`distance`、`distance+1`的距离。

那么怎么找到合适的`j`呢?就需要遍历`0~i-1`之间的数来寻找一个合适的`j`。注意,这里遍历可以从`i-1`开始倒着遍历,因为石头`i`最多只能跳`i+1`的距离,那么`j`和`i`之间的距离最多为`i`。所以当`stones[i]-stones[j]>i`时,就不必再寻找`j`了。

所以我们就可以定义一个二维dp数组完成上述的过程,`dp[i][j]`表示`i`能够跳距离`j`。

最后只要`dp[stones.length-1]`中存在`true`,就表示能有位置跳到最后一个石头。


### 0x2 代码实现

``` java
class Solution {
    public boolean canCross(int[] stones) {
        if(stones==null || stones.length==0){
            return false;
        }
        if(stones.length<2){
            return true;
        }
        boolean[][] dp=new boolean[stones.length][stones.length+1];
        dp[0][1]=true;
        for(int i=1;i<stones.length;i++){
            for(int j=i-1;j>-1;j--){
                int diff=stones[i]-stones[j];
                if(diff>i){
                    break;
                }
                if(dp[j][diff]){
                    dp[i][diff-1]=dp[i][diff]=dp[i][diff+1]=true;
                }
            }
        }
        for(int i=0;i<=stones.length;i++){
            if(dp[stones.length-1][i]){
                return true;
            }
        }
        return false;

    }
}

```

### 0x3 课后总结

说实话,这道题确实不是很难。但是自己想的时候,总差点什么,一看答案,o,原来是这么做的。

这道题感觉还是有点像字符串的dp问题,我们需要对每一个分割点操作。尤其像分割回文串的操作。

位置i能否分割成功,这取决i前面数据,假设位置j(j小于i)已经成功分割,那么i就完全取决于j~i之间的数据状态。
这样就将两个不同的状态联系起来了。

>所以一般的模式是我们在推断i的过程中,当有**多个位置j**能够到达状态i但是**位置i与位置j**之间并不是一定相邻的,需要在**假设**dp\[j]已经成功的前提下,判断**j与i之间**的数据是怎么影响结果dp\[i]的。

所谓的**位置相邻**是指`dp[i]=dp[i-1]`这种,`i`与`i-1`之间已经没有多于的位置了。