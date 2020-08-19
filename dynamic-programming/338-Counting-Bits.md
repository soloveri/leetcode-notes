---
title: 338. 比特位计数
mathjax: true
data: 2020-08-19 19:11:47
updated:
tags:
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/counting-bits)

---

### 0x0 题目详情

>给定一个非负整数 num。对于 0 ≤ i ≤ num 范围中的每个数字 i ，计算其二进制数中的 1 的数目并将它们作为数组返回。

---

**测试用例:**

>示例 1:
输入: 2
输出: [0,1,1]

>示例 2:
输入: 5
输出: [0,1,1,2,1,2]

>进阶:
给出时间复杂度为O(n*sizeof(integer))的解答非常容易。但你可以在线性时间O(n)内用一趟扫描做到吗？
要求算法的空间复杂度为O(n)。
你能进一步完善解法吗？要求在C++或任何其他语言中不使用任何内置函数（如 C++ 中的 __builtin_popcount）来执行此操作。


### 0x1 解题思路

对于数`i`,`i&(i-1)`能够消除`i`从右边开始的第一个1,那么数字`i`中1的个数就是`dp[i&(i-1)]+1`

### 0x2 代码实现

``` java
class Solution {
    public int[] countBits(int num) {
        int[] result=new int[num+1];
        if(num<0){
            return result;
        }
        result[0]=0;
        for(int i=1;i<=num;i++){
            result[i]=result[i&(i-1)]+1;
        }
        return result;

    }
}

```

### 0x3 课后总结

对于求一个数中`1`的个数啊,求一些数字中的`bit`粒度的信息啊,一般都需要用到位运算。

