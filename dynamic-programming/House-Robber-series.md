---
title: 213. 打家劫舍 II
mathjax: true
data: 2020-08-15 18:50:48
updated:
tags:
- 递归
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/house-robber-ii)

---

### 0x0 题目详情

>你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都围成一圈，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。
给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。

---

**测试用例:**

>示例 1:
输入: [2,3,2]
输出: 3
解释: 你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。

>示例 2:
输入: [1,2,3,1]
输出: 4
解释: 你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。偷窃到的最高金额 = 1 + 3 = 4 。

### 0x1 解题思路

如果做过[第198.打家劫舍](https://leetcode-cn.com/problems/house-robber/),这道题就是在[0,n)范围内偷一遍,在[1,n+1)范围内偷一遍,这样永远也不会出现首尾两间房子一起偷。

没有做过198题没关系,思路很简单。对于一个房间我们可以选择偷或者不偷。我们可以维护一个二维dp数组,dp[i][0],当我们偷到第i间房时,此时不偷第i间房所获得的最大利益。相应的dp[i][1]表示当偷到第i间房时,此时偷第i间房所获得的最大利益。

当然有了一个限制,偷了第i间房,就不能偷第i-1间房;对应的,不偷第i间房,可以选择偷或者不偷第i-1间房。所以:

`dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]`
`dp[i][0]=Math.max(dp[i-1][0]+nums[i]`


### 0x2 代码实现

``` java
class Solution {
    public int rob(int[] nums) {
        if(nums==null || nums.length==0){
            return 0;
        }
        if(nums.length<2){
            return nums[0];
        }
        return Math.max(calc(0,nums.length-1,nums),calc(1,nums.length,nums));

    }
    //左闭右开
    private int calc(int left,int right,int[] nums){
        int[][] dp=new int[nums.length][2];
        //1表示偷
        dp[left][1]=nums[left];
        for(int i=left+1;i<right;i++){
            dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]);
            dp[i][1]=nums[i]+dp[i-1][0];
        }
        return Math.max(dp[right-1][0],dp[right-1][1]);
    }
}

```

### 0x3 课后总结

很常规的动态规划题目。