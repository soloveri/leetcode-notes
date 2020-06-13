---
title: 42. 接雨水
mathjax: true
data: 2020-06-13 23:41:32
updated:
tags:
- [单调栈,找规律]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/trapping-rain-water/)

### 0x0 题目详情

>给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

---

**测试用例:**

![接雨水](../images/42-rainwatertrap.png)

>示例:
输入: [0,1,0,2,1,0,1,3,2,1,2,1]
输出: 6

### 0x1 解题思路

这道题我看懂得有两种思路，一种是比较常规的单调栈思路，第二种就是找规律。

**思路1:**

单调栈采用的是单调递减栈，每当弹出元素时，就需要对接水面积更新。接水区域的高度为弹出元素左右两侧较小的值，且需要减去当前弹出元素的高度，也就是代码中的base值。宽度为弹出元素左右两侧高度的下标差。这里不需要对栈中的内容进行清算，数组遍历完了，面积就算完了。只要想到了使用单调栈，代码实现还是比较简单的。

**思路2:**

思路2就是找规律了，在高度数组中，最高项左侧的高度是非严格单调递增的，最高项右侧的高度是非严格单调递减的。

所以我们可以发现：对于最高项左侧的面积：如果当前的高度比当前的最高值小，那么更新面积，如果比当前的最高值大，那么就更新最高值。对于右侧我们可以采取同样的思路。

所以计算面积，分为三步：

- 找到最高值
- 计算左侧的面积
- 计算右侧的面积

这种方法纯粹是找规律，不过比第一种方法还是快了很多。

### 0x2 代码实现

``` java "单调栈"
class Solution {
    public int trap(int[] height) {
        if(height==null || height.length==0){
            return 0;
        }
        int result=0;

        LinkedList<Integer> stack=new LinkedList<>();
        for(int i=0;i<height.length;i++){
            while(!stack.isEmpty() && height[stack.peekLast()]<=height[i]){
                int curIndex=stack.pollLast();
                int base=height[curIndex];
                int leftHeight=base;
                int left=curIndex;
                if(!stack.isEmpty()){
                    leftHeight=height[stack.peekLast()];
                    left=stack.peekLast();
                }
                result+=(Math.min(leftHeight,height[i])-base)*(i-left-1);
            }
            stack.offer(i);
        }
        return result;
    }
}

```

---

``` java "找规律"
class Solution {
    public int trap(int[] height) {
        if(height==null || height.length==0){
            return 0;
        }
        int result=0;
        int maxHeight=0;
        for(int i=0;i<height.length;i++){
            if(height[maxHeight]<height[i]){
                maxHeight=i;
            }
        }
        int left=height[0];
        for(int i=1;i<maxHeight;i++){
            if(left<height[i]){
                left=height[i];
            }else{
                result+=left-height[i];
            }
        }
        int right=height[height.length-1];
        for(int i=height.length-2;i>maxHeight;i--){
            if(right<height[i]){
                right=height[i];
            }else{
                result+=right-height[i];
            }
        }
        return result;
    }
}

```

### 0x3 课后总结

对于单调栈的解法，我一直试图在数组最前方加入一个哨兵，在弹出元素时无需判断栈是否为空，但是失败了。对于单调递增栈加入哨兵似乎应该更好使一点。