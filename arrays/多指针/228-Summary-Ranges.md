---
title: 228. 汇总区间
mathjax: true
data: 2020-06-07 01:32:13
updated:
tags:
- [三指针]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/summary-ranges/)

### 0x0 题目详情

>给定一个无重复元素的有序整数数组，返回数组区间范围的汇总。

---

**测试用例:**
>示例 1:
输入: [0,1,2,4,5,7]
输出: ["0->2","4->5","7"]
解释: 0,1,2 可组成一个连续的区间; 4,5 可组成一个连续的区间。
示例 2:
输入: [0,2,3,4,6,8,9]
输出: ["0","2->4","6","8->9"]
解释: 2,3,4 可组成一个连续的区间; 8,9 可组成一个连续的区间。

### 0x1 解题思路

这道题呢，不难。主要是想记录一下三指针的用法。三个指针分别为left、right、cur。cur用于遍历数组。right作为循环不变量，表示连续区间的右边界。

三指针的移动条件：

如果nums[cur]<=nums[right+1],表示当前元素与区间是连续的，扩大右边界。否则查看连续区间的长度是否为1，从而决定答案的形式。

### 0x2 代码实现

``` java
class Solution {
    public List<String> summaryRanges(int[] nums) {
        List<String> result=new ArrayList<>();
        if(nums==null || nums.length==0){
            return result;
        }
        if(nums.length==1){
            result.add(String.valueOf(nums[0]));
            return result;
        }
        int left=0;
        int right=0;
        int cur=0;
        while(cur<nums.length){
            //转成long是必须的，因为当cur=right且nums[cur]=2147483647时，nums[cur]+1=-1,这样cur永远也不会更新，导致死循环
            while(cur<nums.length && ((long)nums[cur]<=(long)nums[right]+1)){
                right=cur;
                cur++;
            }
            if(left==right){
                result.add(String.valueOf(nums[left]));
            }else{
                result.add(String.valueOf(nums[left])+"->"+String.valueOf(nums[right]));
            }
            left=right=cur;
        }
        return result;
    }
}
```

### 0x3 课后总结

对于边界整数加减法还是要注意一下的把。