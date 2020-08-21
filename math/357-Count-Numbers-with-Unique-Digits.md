---
title: 357. 计算各个位数不同的数字个数
mathjax: true
data: 2020-08-21 19:29:00
updated:
tags:
- 动态规划
- 数学
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/count-numbers-with-unique-digits)

---

### 0x0 题目详情
>给定一个非负整数 n，计算各位数字都不同的数字 x 的个数，其中 0 ≤ x < 10n 。

---

**测试用例:**

>示例:
输入: 2
输出: 91 
解释: 答案应为除去 11,22,33,44,55,66,77,88,99 外，在 [0,100) 区间内的所有数字。


### 0x1 解题思路

这道题其实是个数学题。

对于n=1,各位数字都不相同。所以有10个。

对于n=2,第一位数字有9种选择,范围在`1~9`,而第二位数字也有9种选择,`0~9`排除上一位相同的数字。那么如果第一位是`0`,就是`n==1`的情况,把`n==1`的结果加上就好了

对于n=3,第一位数字有9种选择,范围在`1~9`,第二位数字有9种选择,第三位数字有8种选择。如果第一位是`0`,就是`n==2`的情况,把`n==2`的结果加上就好了

规律已经出来咯,简单的排列组合。

### 0x2 代码实现

``` java
class Solution {
    public int countNumbersWithUniqueDigits(int n) {
        if(n<2){
            return n==0?1:10;
        }
        int result=10;
        int count=9;
        int temp=9;
        n--;
        while(n>0){
            temp*=count;
            count--;
            result+=temp;
            n--;
        }
        return result;

    }
}
```

### 0x3 课后总结

数学题就是找规律,最烦这种题了。