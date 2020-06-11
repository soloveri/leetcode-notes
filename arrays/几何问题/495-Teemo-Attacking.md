---
title: 495. 提莫攻击
mathjax: true
data: 2020-06-11 15:36:15
updated:
tags:
- [几何，智力]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/teemo-attacking/)

### 0x0 题目详情

>在《英雄联盟》的世界中，有一个叫 “提莫” 的英雄，他的攻击可以让敌方英雄艾希（编者注：寒冰射手）进入中毒状态。现在，给出提莫对艾希的攻击时间序列和提莫攻击的中毒持续时间，你需要输出艾希的中毒状态总时长。

你可以认为提莫在给定的时间点进行攻击，并立即使艾希处于中毒状态。

---

**测试用例:**

>示例1:
输入: [1,4], 2
输出: 4
原因: 第 1 秒初，提莫开始对艾希进行攻击并使其立即中毒。中毒状态会维持 2 秒钟，直到第 2 秒末结束。
第 4 秒初，提莫再次攻击艾希，使得艾希获得另外 2 秒中毒时间。
所以最终输出 4 秒。

>示例2:
输入: [1,2], 2
输出: 3
原因: 第 1 秒初，提莫开始对艾希进行攻击并使其立即中毒。中毒状态会维持 2 秒钟，直到第 2 秒末结束。
但是第 2 秒初，提莫再次攻击了已经处于中毒状态的艾希。
由于中毒状态不可叠

### 0x1 解题思路

这道题呢，我认为看作时合并区间的问题，但是比合并简单，这里我们只需要计算合并后的长度。所以如果i位置开始中毒的时间超过了i+1位置的时间点，那么我们人为当前i位置的中毒时常为nums[i+1]-nums[i]，去除了重合的中毒时间，当然最后一个时间点开始的中毒时常一定是规定的中毒时常，因为没有下一个元素能够干扰了。

### 0x2 代码实现

``` java
class Solution {
    public int findPoisonedDuration(int[] timeSeries, int duration) {
        if(timeSeries==null || timeSeries.length==0){
            return 0;
        }
        int result=0;
        for(int i=0;i<timeSeries.length-1;i++){
            result+=timeSeries[i]+duration<=timeSeries[i+1]?duration:timeSeries[i+1]-timeSeries[i];
        }
        result+=duration;
        return result;

    }
}

```

### 0x3 课后总结

这道题，算脑筋急转弯吧，没啥特殊的技巧。