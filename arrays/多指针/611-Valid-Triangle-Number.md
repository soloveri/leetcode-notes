---
title: 611. 有效三角形的个数
mathjax: true
data: 2020-06-11 12:13:03
updated:
tags:
- [双指针]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/valid-triangle-number/)

### 0x0 题目详情

>给定一个包含非负整数的数组，你的任务是统计其中可以组成三角形三条边的三元组个数。

---

**测试用例:**

>示例 1:
输入: [2,2,3,4]
输出: 3
解释:
有效的组合是: 
2,3,4 (使用第一个 2)
2,3,4 (使用第二个 2)
2,2,3

### 0x1 解题思路

这道题的条件是任意两条边之和大于第三条边，任意两条边之差小于第三条边。我一开始的想法时使用三指针从左向右遍历，但是这里要保存重复元素难住我了。在找到一组结果后，我不知道是移动左指针还是右指针收缩区间，而且也不一定能够满足三角形的定义条件。我看到了一种解法，从后往前使用双指针，首先确定一条最长边，我们的目标就是找到两条较小的边，如果小边之和都能大于最长边，那么一定能够组成三角形。

加入左指针为left，右指针为right，能够组成的三角形数为right-left,因为left指向的是最小边，left之后的元素都比nums\[left]大，那么肯定能够组成三角形。right与left之间的元素个数有right-left+1个，但是因为要组成三角形，right固定的画，left最多能走到right-1，left到right-1之间的个数为right-left。

### 0x2 代码实现

时间复杂度为O(N^2),空间复杂度为O(1)。

``` java
class Solution {
    public int triangleNumber(int[] nums) {
        //一看就是求三数之和的变体
        if(nums==null || nums.length==0){
            return 0;
        }
        int result=0;
        Arrays.sort(nums);
        for(int i=nums.length-1;i>=2;i--){
            //从后往前遍历，每次固定一条最长边
           int left=0;
           int right=i-1;
           while(left<right){
               if(nums[left]+nums[right]>nums[i]){
                   result+=right-left;
                   right--;
               }else{
                   left++;
               }
           }
        }
        return result;
    }
}
```

### 0x3 课后总结

我只能说双指针博大精深，而且这道题有点求小和的味道。通过索引来确定答案的长度，当然这要求数组是有序的。