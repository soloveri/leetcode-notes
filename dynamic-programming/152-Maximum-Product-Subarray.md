---
title: 152. 乘积最大子数组
mathjax: true
data: 2020-06-04 00:11:52
updated:
tags:
- [动态规划]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/maximum-product-subarray/)

### 0x0 题目详情

>给你一个整数数组 nums ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

 ---

**测试用例:**
>示例 1:
输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。

### 0x1 解题思路

这道题递归我是写不出来，唉。就是硬总结。

**连续的概念**

连续这个概念可以说是一批题目的代表了，连续最大子数组的和、连续最大子数组的积、最长上升序列等等，都是求一个连续的子数组。子序列、子数组的这类问题的状态设计有一个重点：以nums[i]结尾的连续子数组。

因为数组中包含负数，例如数组`[2,3,-2,4]`，前两个元素组成的最大连续子数组为6，但是一遇到-2，那么最大值会转变为最小值，所以每个位置上有两种状态，最大值状态和最小值状态。dp[i][0]表示以nums[i]结尾的连续子数组的最小积，dp[i][1]表示以nums[i]结尾的连续子数组的最大值

- 如果nums[i]>0,那么dp[i]产生的最小值乘以一个正数nums[i]仍然是最小值，dp[i]产生的最大值乘以一个正数nums[i]仍然是最大值
- 如果nums[i]<0,那么dp[i]产生的最小值乘以一个负数nums[i]就会转变为最大值，dp[i]产生的最大值乘以一个负数nums[i]就会转变为最小值
- 如果nums[i]=0,那么以nums[i]结尾的连续子数组产生的最大值、最小值都为0

并且我们还需要考虑，当nums[i]>0,dp[i-1][1]*nums[i]<0时，那么我们就可以抛弃前面的数组，从nums[i]开始重新计算。

怎么说呢，保持的最小值是为了保留前面的负数积，万一遇到一个负数，负数摇身一变成为最大值了呢。

如果nums[i]>=0
`dp[i][0]=Math.min(nums[i],nums[i]*dp[i-1][0])`
`dp[i][1]=Math.max(nums[i],nums[i]*dp[i-1][1])`

如果nums[i]<0
`dp[i][0]=Math.min(nums[i],nums[i]*dp[i-1][1]`
`dp[i][1]=Math.max(nums[i],nums[i]*dp[i-1][0]`

最后最大值通过遍历dp[i][1]得到最大值。

### 0x2 代码实现

``` java
class Solution {
    public int maxProduct(int[] nums) {
        if(nums==null || nums.length==0){
            return 0;
        }
        if(nums.length<2){
            return nums[0];
        }
        int[][] dp=new int[nums.length][2];
        dp[0][0]=nums[0];
        dp[0][1]=nums[0];
        for(int i=1;i<nums.length;i++){
            if(nums[i]>=0){
                dp[i][0]=Math.min(nums[i],dp[i-1][0]*nums[i]);
                dp[i][1]=Math.max(nums[i],dp[i-1][1]*nums[i]);
            }
            else{
                dp[i][0]=Math.min(nums[i],nums[i]*dp[i-1][1]);
                dp[i][1]=Math.max(nums[i],nums[i]*dp[i-1][0]);
            }
        }
        int result=0;
        for(int i=0;i<dp.length;i++){
            result=Math.max(result,dp[i][1]);
        }
        return result;
    }
}

```

### 0x3 课后总结

对于连续区间、连续子数组等问题，一定要明确一个概念：以nums[i]结尾的状态。以nums[i]结尾，以nums[i]结尾。