---
title: 119. 杨辉三角 II
mathjax: true
data: 2020-06-21 19:11:04
updated:
tags:
- [双指针,链表]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

### 0x0 题目详情

>给定一个非负索引 k，其中 k ≤ 33，返回杨辉三角的第 k 行。
你可以优化你的算法到 O(k) 空间复杂度吗？

---

**测试用例:**

>输入: 3
输出: [1,3,3,1]

### 0x1 解题思路

这道题我们需要注意：输入的行数是从0开始的，比如输入3，实际包含四个元素，也就是第四行。

可以发现一个规律杨辉三角的一行正好的(1+x)^k的系数。

当k=4时，系数为1、4、6、4、1。那么C(4,2)=C(4,1)*(k-1)/1+1。
一般式就是C(4,i+1)=C(4,i)*(k-i)/(i+1)。这个公式也是找规律找出来的。其中i为项系数的索引，从0开始。

### 0x2 代码实现

``` java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        // List<Integer> result=new ArrayList<>();
        List<Integer> res = new ArrayList<Integer>(rowIndex+1);
        long nk = 1;
        for(int i = 0; i <= rowIndex; i++){
            res.add((int)nk);
            //这里都是提前求出下一个项的系数，
            nk = nk * (rowIndex - i) / (i + 1);
        }
        return res;

    }
}
```

### 0x3 课后总结

数学好就是可以为所欲为！