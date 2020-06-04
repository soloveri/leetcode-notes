---
title: 209. 长度最小的子数组
mathjax: true
data: 2020-06-02 01:54:31
updated:
tags:
- [滑动窗口，双指针]
categories:
- algorithm
---


### 0x0 题目详情

>给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的连续子数组，并返回其长度。如果不存在符合条件的连续子数组，返回 0。

---

**测试用例：**
>示例: 
输入: s = 7, nums = [2,3,1,2,4,3]
输出: 2
解释: 子数组 [4,3] 是该条件下的长度最小的连续子数组。
进阶:
如果你已经完成了O(n) 时间复杂度的解法, 请尝试 O(n log n) 时间复杂度的解法。


### 0x1 解题思路

这道题打上了滑动窗口的标签，但是跟我学到的滑动窗口不是一个东西啊。还有这滑动窗口和双指针有啥区别？

我仔细想了一哈，滑动窗口的左右两个指针只会往前走，不会倒退，一个指针一个元素只会遍历一次。但是双指针也是这个样子的吧。

使用滑动窗口主要有两个规则需要我们制定：

- 什么时候扩大窗口
- 什么时候收缩窗口

当当前的窗口元素和小于target时，不断扩大窗口，当窗口元素和大于等于target时，就可以收缩左窗口了，直到窗口内元素和再次小于target为止时。在每一次收缩窗口时，需要计算一下最大值。

### 0x2 代码实现

``` java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        if(nums==null || nums.length==0){
            return 0;
        }
        int result=Integer.MAX_VALUE;
        int curResult=0;
        int i=0;
        for(int j=0;j<nums.length;j++){
            curResult+=nums[j];
            //收缩窗口
            while(curResult>=s){
                curResult-=nums[i];
                //每收缩一次，就需要更新一下最大值
                result=Math.min(result,j-i+1);
                i++;
            }
        }
        return result==Integer.MAX_VALUE?0:result;
    }
}
```

### 0x3 课后总结

我寻思这种滑动窗口和双指针有啥区别啊。滑动倒是我认为有一个特点，一个指针只会遍历数组元素一次，并且通常也只需要遍历数组一次就可求得答案。