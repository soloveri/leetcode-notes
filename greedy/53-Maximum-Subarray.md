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

呜,不知道怎么想的,贪心就过了(或许也可以算作动态规划吧),就是对于数组中的每一个元素i,我们都维护一个变量`pre`表示在`i`之前(包括`i`在内)的最大连续子数组和。

核心思路就是对于元素`nums[i]`,我们需要判断前面产生的和`pre`有没有对`nums[i]`产生增益。所谓的产生增益就是`nums[i]+pre>=nums[i]`,在这种情况下,我`nums[i]`才选择使用前面产生的和`pre`。否则如果`nums[i]+pre<nums[i]`,那么在这种情况下,凭什么需要使用前面的和?`nums[i]`都能自立门户了,直接将`pre`更新为`nums[i]`就好。

并且不断更新最大值`result`。当遍历结束时就可以得到结果。

那么我们为什么不能直接使用`result=Math.max(result,result+nums[i]`求最大连续子数组和呢?

因为这样写,就放弃了`nums[i]`单独成为最大连续子数组第一个元素的机会。

那么`result=max(result,result+nums[i],nums[i])`对吗?

不对,对于子数组`[5,-1,2]`。我们在遍历到`-1`时,`result==5`,不会使用`-1`,但是在遇到`2`时,又会选择`result=result+2==7`,越过了中间元素`-1`。

所以我们必须使用两个变量。 


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