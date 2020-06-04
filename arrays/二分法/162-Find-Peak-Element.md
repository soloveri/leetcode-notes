---
title: 162. 寻找峰值
data: 2020-06-05 00:15:59
updated:
tags:
- [二分法]
categories:
- algorithm
---

### 0x0 题目详情

>峰值元素是指其值大于左右相邻值的元素。
给定一个输入数组 nums，其中 nums[i] ≠ nums[i+1]，找到峰值元素并返回其索引。
数组可能包含多个峰值，在这种情况下，返回任何一个峰值所在位置即可。
你可以假设 nums[-1] = nums[n] = -∞。
说明:
你的解法应该是 O(logN) 时间复杂度的。

---

**测试用例：**
>示例 1:
输入: nums = [1,2,3,1]
输出: 2
解释: 3 是峰值元素，你的函数应该返回其索引 2。
示例 2:
输入: nums = [1,2,1,3,5,6,4]
输出: 1 或 5
解释: 你的函数可以返回索引 1，其峰值元素为 2；或者返回索引 5， 其峰值元素为 6。

### 0x1 解题思路

这道题O(n)方法是简单的，当然也不是这道题的本意。
这道题的关键条件时假设 nums[-1] = nums[n] = -∞。并且相邻元素是不相同的。只要数组中出现了一对相邻数组且二者存在大小关系。那么峰值是一直存在的。
如果nums[i]< nums[i+1],那么如果有nums[i+2]< nums[i+1]，nums[i+1]就是峰值，如果nums[i+2]> nums[i+1]，那么峰值肯定在有半侧，因为最右侧有个负无穷兜底。即右侧是单调递增的，那么最后一个一个元素就峰值。左侧同理。所以就实现了二分法。

二分法：收缩左区间还是右区间的规则：
为了方便获取mid+1的值，这里区间采用左闭右闭的方法

- 放弃左区间：nums[mid]< nums[mid+1],left=mid+1
- 放弃右区间：nums[mid]> nums[mid+1]，right=mid

### 0x2 代码实现

``` java
class Solution {
    public int findPeakElement(int[] nums) {
        if(nums==null || nums.length==0){
            return 0;
        }
        int mid=0;
        int left=0;
        int right=nums.length-1;
        while(left<right){
            mid=left+(right-left)/2;
            if(nums[mid]<nums[mid+1]){
                left=mid+1;
            }else{
                right=mid;
            }
        }
        return left;
    }
}

```

### 0x3 课后总结

这题是二分法我是真没想出来能这么用。那两个条件我看不出任何的隐含意思。。怎么说呢，因为左右两端都为服务穷，所以可以认为对于右半区间，上坡必有坡顶。对于左半区间，下坡也必有坡顶。