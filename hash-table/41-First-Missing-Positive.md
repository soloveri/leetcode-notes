---
title: 41. 缺失的第一个正数
mathjax: true
data: 2020-06-13 23:41:32
updated:
tags:
- [自定义哈希表]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/first-missing-positive/)

### 0x0 题目详情

>给你一个未排序的整数数组，请你找出其中没有出现的最小的正整数。

---

**测试用例:**

>示例 1:
输入: [1,2,0]
输出: 3
示例 2:

>输入: [3,4,-1,1]
输出: 2
示例 3:

>输入: [7,8,9,11,12]
输出: 1

提示：
你的算法的时间复杂度应为O(n)，并且只能使用常数级别的额外空间。


### 0x1 解题思路

这道题跟第442题[找出数组中的重复元素非常像](442-Find-All-Duplicates-in-an-Array.md)。特别是442题中的抽屉原理的解法，一个萝卜一个坑，在这里也适用。但是我觉得好神奇啊，自定义哈希规则：i位置上的数应该为i+1。这个规则好神奇，我不知道为什么。。。,但是就是能把数放到正确的位置上。

这里有个前提，我们只负责将1~nums.length范围内的数。

### 0x2 代码实现

``` java
class Solution {
    public int firstMissingPositive(int[] nums) {
        if(nums==null){
            return -1;
        }
        if(nums.length==0){
            return 1;
        }
        for(int i=0;i<nums.length;i++){
            //将数放到正确的位置上
            //注意这里只能是nums[nums[i]-1]!=nums[i]，而不能是nums[i]-1!=i
            //因为如果数组为[1,1]，这时就会产生死循环
            while(nums[i]>0 && nums[i]<nums.length && nums[nums[i]-1]!=nums[i]){
                swap(nums,nums[i]-1,i);
            }
        }

        for(int i=0;i<nums.length;i++){
            if(nums[i]-1!=i){
                return i+1;
            }
        }
        return nums.length+1;

    }
    private void swap(int[] nums,int lhs,int rhs){
        int temp=nums[lhs];
        nums[lhs]=nums[rhs];
        nums[rhs]=temp;
    }
}

```

### 0x3 课后总结

这个自定义的哈希规则到底是为什么啊，虽然不知道如何解释，但就是找不出反例，总能将数放到正确的位置上。