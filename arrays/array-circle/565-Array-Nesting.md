---
title: 565. 数组嵌套
mathjax: true
data: 2020-06-11 15:33:57
updated:
tags:
- [数组环]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

### 0x0 题目详情

>索引从0开始长度为N的数组A，包含0到N - 1的所有整数。找到最大的集合S并返回其大小，其中 S[i] = {A[i], A[A[i]], A[A[A[i]]], ... }且遵守以下的规则。
假设选择索引为i的元素A[i]为S的第一个元素，S的下一个元素应该是A[A[i]]，之后是A[A[A[i]]]... 以此类推，不断添加直到S出现重复的元素。

>提示：
N是[1, 20,000]之间的整数。
A中不含有重复的元素。
A中的元素大小在[0, N-1]之间。

---

**测试用例:**

>示例 1:
输入: A = [5,4,0,3,1,6,2]
输出: 4
解释:
A[0] = 5, A[1] = 4, A[2] = 0, A[3] = 3, A[4] = 1, A[5] = 6, A[6] = 2.
其中一种最长的 S[K]:
S[0] = {A[0], A[5], A[6], A[2]} = {5, 6, 2, 0}

### 0x1 解题思路

这道题就是求数组中最大长度的环。求环没什么好说的，就是使用快慢指针。但是我们会通过环中的每个数据作为起始点来求当前环的长度，这对于我们求最大环长度是没有必要的。我们可以在遍历一个环时，更新完长度后将当前元素设为-1表示这个元素我们遍历过了，不用再以这个元素作为起点求环长度了。

### 0x2 代码实现

``` java
class Solution {
    public int arrayNesting(int[] nums) {
        if(nums==null || nums.length==0){
            return 0;
        }
        int result=0;

        for(int i=0;i<nums.length;i++){
            if(nums[i]==-1){
                continue;
            }
            int slow=i;
            int fast=i;
            int cur=0;
            while(nums[slow]!=-1){
                int temp=slow;
                slow=nums[slow];

                cur++;
                //在同一个环中，我们把遍历过的元素都为-1，防止再次遍历
                nums[temp]=-1;
            }
            result=Math.max(result,cur);
        }
        return result;
    }
}

```

### 0x3 课后总结

这里的不能出现相同元素有什么用？如果有相同元素呢？