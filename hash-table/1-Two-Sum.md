---
title: 1. 两数之和
mathjax: true
data: 2020-06-14 00:40:49
updated:
tags:
- [哈希表]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/two-sum/)

### 0x0 题目详情

>给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

---

**测试用例:**

>示例:
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]


### 0x1 解题思路

这道题如果是找哪两个数相加为target，那么对数组排序后，直接双指针首尾遍历一遍，就可得出答案。

关键是这道题要求索引，那么就不能对数组排序了，当然你也可以使用hashmap记住原来的索引。

这里的方法是：仍然需要维护一个hashmap，key为元素，value为元素对应的索引。如果当前数val的对应值target-val不在map里，表示我们还未找到答案。只需要把当前数加入map即可。否则就表示我们找到了一组答案，直接从map里取出索引返回答案即可。

### 0x2 代码实现

``` java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer,Integer> indexMap=new HashMap<>();
        int[] result=new int[2];
        for(int i=0;i<nums.length;i++){
            if(indexMap.containsKey(target-nums[i])){
                result[0]=indexMap.get(target-nums[i]);
                result[1]=i;
                return result;
            }else{
                indexMap.put(nums[i],i);
            }
        }
        return null;
    }
}
```

### 0x3 课后总结

leetcode的第一道题，咋感觉还是要想一会的。