---
title: 26. 删除排序数组中的重复项
mathjax: true
data: 
updated:
tags:
- [双指针]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

### 0x0 题目详情

给定一个排序数组，你需要在 原地 删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。

---
**测试用例:**

>示例 1:
给定数组 nums = [1,1,2], 
函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 
你不需要考虑数组中超出新长度后面的元素。

>示例 2:
给定 nums = [0,0,1,1,1,2,2,3,3,4],
函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。
你不需要考虑数组中超出新长度后面的元素。

### 0x1 解题思路

这和[第80题移除重复元素II](80-Remove-Duplicates-from-Sorted-Array-II.md)的思路是一致的，需要维持一个循环不变量index，其含义为没有重复的元素序列的下一个位置，所以只需要比较nums[index-1]和nums[i]是否相等(i为遍历的索引)。如果相等，表示我们当前遇到的是重复元素，index不变，i继续遍历下一个元素，否则使用nums[i]覆盖nums[index]即可，然后更新index与i。

注意上述的方法需要在数组有序的前提下。

### 0x2 代码实现

``` java
class Solution {
    public int removeDuplicates(int[] nums) {
        if(nums==null || nums.length==0){
            return 0;
        }
        //left表示没有重复元素数组的下一个位置
        int left=1;
        int right=0;
        for(int i=1;i<nums.length;i++){
            if(nums[left-1]!=nums[i]){
                nums[left]=nums[i];
                left++;
            }
        }
        return left;

    }
}

```

### 0x3 课后总结

循环不变量，有序，这些概念在使用双指针的时候一般都会用到。