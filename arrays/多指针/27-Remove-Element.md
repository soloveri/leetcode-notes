---
title: 27. 移除元素
mathjax: true
data: 2020-06-14 00:42:09
updated: 2020-06-21 20:25:22
tags:
- [双指针]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/remove-element/)

### 0x0 题目详情

>给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。
不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。
元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

---

**测试用例:**

>示例 1:
给定 nums = [3,2,2,3], val = 3,
函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。
你不需要考虑数组中超出新长度后面的元素。

>示例 2:
给定 nums = [0,1,2,2,3,0,4,2], val = 2,
函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。
注意这五个元素可为任意顺序。
你不需要考虑数组中超出新长度后面的元素。

### 0x1 解题思路

同[第26题一样](26-Remove-Duplicates-from-Sorted-Array.md),首先需要对数组进行排序,然后维持一个循环不变量index，含义为不包含元素val的最长序列的下一个位置。如果当前遇到的元素为val，那么直接遍历下一个元素，否则替换nums[index]

### 0x2 代码实现

``` java
class Solution {
    public int removeElement(int[] nums, int val) {
        if(nums==null || nums.length==0){
            return 0;
        }
        Arrays.sort(nums);
        int left=0;
        for(int i=0;i<nums.length;i++){
            if(nums[i]!=val){
                nums[left]=nums[i];
                left++;
            }
        }
        return left;

    }
}
```

### 0x3 课后总结

循环不变量，有序，这些概念一般在是使用双指针的时候都会用到。