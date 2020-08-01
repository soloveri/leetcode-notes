---
title: 53. 最大子序和
mathjax: true
data: 2020-08-01 16:25:22
updated:
tags:
- 贪心
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/maximum-subarray/)

---

### 0x0 题目详情

>给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

### 0x1 解题思路

呜,不知道怎么想的,贪心就过了(或许也可以算作动态规划吧),就是对于数组中的每一个元素i,我们都维护一个变量`pre`表示截至到元素i为止(包括元素i),能够产生的连续的最大子数组和,并且不断更新最大值`result`。当遍历结束时就可以得到结果。

### 0x2 代码实现

``` java
class Solution {
    public int maxSubArray(int[] nums) {
        int result=Integer.MIN_VALUE;
        if(nums==null || nums.length<2){
            return result=nums.length!=0?nums[0]:0;
        }
        //表示i之前能够产生的最大连续子数组和
        int pre=0;
        for(int i=0;i<nums.length;i++){
            //判断i之前的子数组和加上当前元素i的结果是否比元素i本身大,如果没有,说明我们根本没有必要使用前面的子数组和
            pre=Math.max(pre+nums[i],nums[i]);
            //每次都更新最大值
            result=Math.max(result,pre);
        }
        return result;
    }
}
```

### 0x3 课后总结

脑袋一闪,就做出来了。也许这就是灵感吧,haha.