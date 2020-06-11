---
title: 287. 寻找重复数
mathjax: true
data: 2020-06-11 11:50:15
updated:
tags:
- [数组环]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/find-the-duplicate-number/)

### 0x0 题目详情

>给定一个包含 n + 1 个整数的数组 nums，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。假设只有一个重复的整数，找出这个重复的数。

---

**测试用例:**

>示例 1:
输入: [1,3,4,2,2]
输出: 2

>示例 2:
输入: [3,1,3,4,2]
输出: 3

>说明：
不能更改原数组（假设数组是只读的）。
只能使用额外的 O(1) 的空间。
时间复杂度小于 O(n2) 。
数组中只有一个重复的数字，但它可能不止重复出现一次。

### 0x1 解题思路

这道题想是想不出来的，看看答案才能勉强维持生活的样子。我看懂的结果一是二分法，但是时间超了。二是采用链表找环入口点的做法，我不知道为啥，这道题的输入总是可以构成一个链表，你说奇不奇怪，输入中的每个数字都在1~n之中，总共有n+1个数。

啊，这道题太巧了，总能构成一个环，那么题目就转化为求环的入口点，采用快慢指针，但是我不知道原理，我只知道咋用。

### 0x2 代码实现

这道题的时间复杂度吧，不好计算，遍历了几遍我算不出来，但是肯定没有n遍，那就先算成O(N)吧，空间复杂度为O(1)

``` java
class Solution {
    public int findDuplicate(int[] nums) {
        //快慢指针从第一个元素开始
        int fast = 0, slow = 0;
        while(true) {
            //第一次相遇
            fast = nums[nums[fast]];
            slow = nums[slow];
            if(slow == fast) {
                fast = 0;
                //第二次相遇
                while(nums[slow] != nums[fast]) {
                    fast = nums[fast];
                    slow = nums[slow];
                }
                return nums[slow];
            }
        }
    }
}

```

### 0x3 课后总结

数组中的环问题也可以使用快慢指针。每次更新指针为数组中对应的元素，但是要注意是否会产生越界问题。