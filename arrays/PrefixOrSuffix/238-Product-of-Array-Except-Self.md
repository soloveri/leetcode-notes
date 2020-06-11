---
title: 238. 除自身以外数组的乘积
mathjax: true
data: 2020-06-02 01:52:38
updated:
tags:
- [前缀和]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/product-of-array-except-self/)

### 0x0 题目详情

>给你一个长度为 n 的整数数组 nums，其中 n > 1，返回输出数组 output ，其中 output[i] 等于 nums 中除 nums[i] 之外其余各元素的乘积。

---

**测试用例:**

示例:

>输入: [1,2,3,4]
输出: [24,12,8,6]
提示：题目数据保证数组之中任意元素的全部前缀元素和后缀（甚至是整个数组）的乘积都在 32 位整数范围内。
说明: 请不要使用除法，且在 O(n) 时间复杂度内完成此题。

### 0x1 解题思路

这道题一看就需要用到前缀积。只不过是两次不同方向的前缀，并且不允许使用额外空间是有点麻烦。

而且前缀和数组的定义我们一定要记住，对于索引i，在前缀和数组中preSum[i]表示i之前的元素的乘积。我们首先需要初始化前缀积的第一个元素为1。然后从左向右计算一遍前缀积。

在从右向左计算的情况下，前缀积数组中最后一个元素不要改变，需要引入一个额外变量来维持后缀积，并且每计算出一个元素的结果，就需要更新后缀积。

### 0x2 代码实现

遍历两边数组,时间复杂度为O(N),空间复杂度为O(1)

``` java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int[] result=new int[nums.length];
        if(nums==null || nums.length==0){
            return result;
        }
        result[0]=1;
        for(int i=1;i<nums.length;i++){
            result[i]=result[i-1]*nums[i-1];
        }
        //temp就是用来维持后缀积的变量
        int temp=nums[nums.length-1];
        for(int i=nums.length-2;i>=0;i--){
            result[i]=result[i]*temp;
            temp*=nums[i];
        }
        return result;
    }
}
```

### 0x3 课后总结

前缀和、前缀积这些概念没什么好说的，我们需要注意的是一个元素的值到底是什么？到底要初始化为多少？

对于前缀和，需要初始化为0，对于前缀积，需要初始化为1。