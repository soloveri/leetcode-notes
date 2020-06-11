---
title: 560. 和为K的子数组
mathjax: true
data: 2020-06-11 13:54:13
updated:
tags:
- [前缀和]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

### 0x0 题目详情

>给定一个整数数组和一个整数 k，你需要找到该数组中和为 k 的连续的子数组的个数。

---

**测试用例:**

>示例 1 :
输入:nums = [1,1,1], k = 2
输出: 2 , [1,1] 与 [1,1] 为两种不同的情况。

### 0x1 解题思路

本题求得是和为k的连续子数组的个数，对于一个子数组，假设有两个指针left与right，现在left所在位置的前缀和为A，right所在位置的前缀和为B，如果B—A=k,那么就说明我们找到了一组答案。我们只需要获得前缀和为A的连续子数组的个数，就可以确定和为K的子数组个数。这道题可以采用前缀和数组实现，但是我们并不需要直到每个前缀和的下标，我们只关心每个前缀和出现的次数，所以可以采用哈希表记录每种前缀和出现的个数。

对于每一个right指向的值，我们都计算前缀和B-K出现的次数，并更新新的前缀和。

但是一个需要注意的点就是前缀和的初始化，对于0位置的元素，其前缀和为0，个数为1，作为初始化数据加入map。map的定义如下：

key：前缀和
value：出现的次数

### 0x2 代码实现

``` java
class Solution {
    public int subarraySum(int[] nums, int k) {
        if(nums==null || nums.length==0){
            return 0;
        }
        if(nums.length<2){
            return nums[0]==k?1:0;
        }
        int result=0;
        Map<Integer,Integer> prefixSumCount=new HashMap<>();
        prefixSumCount.put(0,1);
        int sum=0;
        for(int i=0;i<nums.length;i++){
            sum+=nums[i];
            int sub=sum-k;
            //获取前缀和为sum-k的个数，
            //这里为什么要先求出现的次数呢？因为我们求的是i之前前缀和为sun-k的个数，不包括i，一旦先更新，就会提前把包含i的前缀和加入map，，如果k=0，就会污染了原来的数据
            int preCount=prefixSumCount.getOrDefault(sub,0);
            result+=preCount;
            prefixSumCount.put(sum,prefixSumCount.getOrDefault(sum,0)+1);
        }
        return result;
    }
}

```

### 0x3 课后总结

使用前缀、后缀等技巧时，一定要注意第一个元素的前缀(后缀)数据初始化。