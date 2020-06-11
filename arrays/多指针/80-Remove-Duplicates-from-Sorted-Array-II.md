---
title: 80. 删除排序数组中的重复项 II
mathjax: true
data: 2020-06-02 01:52:38
updated:
tags:
- [递归,求路径]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array-ii/)

### 0x0 题目详情

>给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素最多出现两次，返回移除后数组的新长度。
不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

---
**题目详情**

>示例 1:
给定 nums = [1,1,1,2,2,3],
函数应返回新长度 length = 5, 并且原数组的前五个元素被修改为 1, 1, 2, 2, 3 。
你不需要考虑数组中超出新长度后面的元素。

### 0x1 解题思路

这里需要再次说明一下循环不变量的概念，所谓的循环不变量在双指针题目中经常出现。这个变量的含义不会改变，但是数值可以改变，直到整个算法结束。

这道题中我们设计的循环不变量就是重复元素不超过两个的区间的下一个元素。例如`[1,1,2,2,2,2,2]`，这里的循环不变量slow的值为4，符合题意区间的下一个位置。

那么我们如何控制重复元素不超过两个呢？也比较简单`nums[slow]-2!=nums[index]`，就表示重复元素最多为两个。

### 0x2 代码实现

时间复杂度为O(N)，空间复杂度为O(1)

``` java
class Solution {
    public int removeDuplicates(int[] nums) {
        if(nums==null){
            return 0;
        }
        if(nums.length<=2){
            return nums.length;
        }
        //index表示区间的下一个元素，这里的区间指符合条件，重复元素不超过两个元素的区间
        int index=2;
        for(int i=2;i<nums.length;i++){
            if(nums[index-2]!=nums[i]){
                nums[index]=nums[i];
                index++;
            }
        }
        return index;
    }
}

```

### 0x3 课后总结

双指针，对于求特殊区间的这种东西，一般都会用到循环不变量。设计一个好的有意义的循环不变量还是非常可以的。