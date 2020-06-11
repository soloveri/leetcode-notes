---
title: 229. 求众数 II
mathjax: true
data: 2020-06-07 00:56:27
updated:
tags:
- [摩尔投票法]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/majority-element-ii/)

### 0x0 题目详情

>给定一个大小为 n 的数组，找出其中所有出现超过 ⌊ n/3 ⌋ 次的元素。
说明: 要求算法的时间复杂度为 O(n)，空间复杂度为 O(1)。

---

**测试用例:**
>示例 1:
输入: [3,2,3]
输出: [3]
示例 2:
输入: [1,1,1,3,3,2,2,2]
输出: [1,2]

### 0x1 解题思路

这道题呢，难就难在要求空间复杂度为O(1)，否则直接用hashmap统计一遍就可得出答案。

所以正确的做法是使用摩尔投票法。这个摩尔投票呢，我也解释不清楚，好像是似懂非懂的感觉。对于长度为n的数组，个数超过⌊ n/k ⌋的众数最多有k-1个。因为试想以下，假设有k个众数，每个众数都>⌊ n/k ⌋，那么最后数的个数和就超过n了，显然不成立。

所以对于k=3，众数最多有2个，但是不一定有两个，因为有可能没有产生众数。对于k=3，所以我们需要选取两个候选者A、B，当第三个数不等于A或B，且A和B的个数都不为零时，表示这个人把票投个第三者了，A和B的票数都需要减1。当A或B的票数减为0时，我们就可以更新候选者了。最后剩下了两个数，就有可能成为众数。

怎么说呢？这个摩尔投票感觉就是在相互对抗，对于两个候选者，如果它们的票数都被别的选票对抗完了，表示它们在当前情况下，它们已经不可能成为候选人了，要选新人了。剩下的一定是票数最多的人。  


### 0x2 代码实现

``` java
class Solution {
    public List<Integer> majorityElement(int[] nums) {
        List<Integer> result=new ArrayList<>();
        if(nums==null || nums.length==0){
            return result;
        }
       int can_A=0;
       int A_count=0;
       int can_B=0;
       int B_count=0;
       for(int elem:nums){
           if(elem==can_A){
               A_count++;
               continue;
           }
           if(elem==can_B){
               B_count++;
               continue;
           }
           if(A_count==0){
               can_A=elem;
               A_count++;
               continue;
           }
           if(B_count==0){
               can_B=elem;
               B_count++;
               continue;
           }
           A_count--;
           B_count--;
       }
       A_count=0;
       B_count=0;
       int pivot=nums.length/3;
       for(int elem:nums){
           if(elem==can_A){
               A_count++;
           }//这里一定要是 else if，因为有可能两个候选者是相同的
           else if(elem==can_B){
               B_count++;
           }
       }
       if(A_count>pivot){
           result.add(can_A);
       }
       if(B_count>pivot){
           result.add(can_B);
       }
       return result;
    }
}

```

### 0x3 课后总结

摩尔投票法我还是不是很懂啊，对于只有一个众数的问题，我是明白了，但是我不明白的是求两个众数时，为什么第三张票不同时，两个众数的票数都要减1呢？