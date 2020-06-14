---
title: 26. 删除排序数组中的重复项
difficulty: easy
mathjax: true
data: 2020-06-15 00:55:18
updated:
tags:
- [数学]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/plus-one/)

### 0x0 题目详情

>给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。
你可以假设除了整数 0 之外，这个整数不会以零开头。

---
**测试用例:**

>示例 1:
输入: [1,2,3]
输出: [1,2,4]
解释: 输入数组表示数字 123。

>示例 2:
输入: [4,3,2,1]
输出: [4,3,2,2]
解释: 输入数组表示数字 4321。

### 0x1 解题思路

这道题的思路非常简单，跟第2题[链表相加](../../linked-list/2-Add-Two-Numbers.md)思路一致，都是保持一个进位值，在从后往前遍历的过程中不断计算添加近位后的值。如果遍历完成后，近位值为1，这就说明新增了一位数，原数组放不下。需要新生成一个数组。

### 0x2 代码实现

``` java
class Solution {
    public int[] plusOne(int[] digits) {
        if(digits==null || digits.length==0){
            return digits;
        }
        int[] result=null;
        //加1操作只进行一次，所以不必放在循环内执行
        int temp=digits[digits.length-1]+1;
        //grate为维护的进位值
        int grate=temp/10;
        digits[digits.length-1]=temp%10;
        int cur=0;
        for(int i=digits.length-2;i>-1;i--){
            cur=digits[i]+grate;
            grate=cur/10;
            digits[i]=cur%10;
        }
        if(grate==0){
            return digits;
        }
        result=new int[digits.length+1];
        result[0]=grate;
        for(int i=0;i<digits.length;i++){
            result[i+1]=digits[i];
        }
        return result;
    }
}

```

### 0x3 课后总结

凡是遇到这种两个大数相加的情况，都需要考虑加到最后是否还有进位值，是否需要扩充长度来保存新的进位值。

