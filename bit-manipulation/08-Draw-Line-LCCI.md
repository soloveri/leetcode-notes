---
title: 08.绘制直线
mathjax: true
data: 2020-05-20 19:12:51
updated:
tags:
- [数组,位运算]
categories:
- algorithm
---

### 题目详情

[题目链接](https://leetcode-cn.com/problems/draw-line-lcci/)

绘制直线。有个单色屏幕存储在一个一维数组中，使得32个连续像素可以存放在一个 int 里。屏幕宽度为w，且w可被32整除（即一个 int 不会分布在两行上），屏幕高度可由数组长度及屏幕宽度推算得出。请实现一个函数，绘制从点(x1, y)到点(x2, y)的水平线。

给出数组的长度 length，宽度 w（以比特为单位）、直线开始位置 x1（比特为单位）、直线结束位置 x2（比特为单位）、直线所在行数 y。返回绘制过后的数组。

---

**测试用例:**
输入：length = 1, w = 32, x1 = 30, x2 = 31, y = 0
 输出：[3]
 说明：在第0行的第30位到第31为画一条直线，屏幕表示为[0b000000000000000000000000000000011]

### 解题思路

说实话我感觉这道题目设计的很辣鸡。测试用例的参数根本给的不全，调试的时候根本不好调。

好吧，虽然我知道这道题考的是位运算，但是实现的很麻烦，而且也不能通过所有的测试用例。我刚开始的思路首先求出x1和x2在数组中的下标，然后依次填数组，把x1~x2涉及的数分为三部分依次填开头、中间和结尾。结果费力不讨好，搞半天也没有ac。最后看评论区发现实现思路比我的简单多了。

其中一种做法不分为三部分，直接对x1~x2所涉及的数32bit全部填1，最后再分别处理开头和结尾就行。我靠，我怎么就想不到呢。

### 代码实现

时间复杂度为$O(width)$，因为最多遍历一行。

``` java
class Solution {
    public int[] drawLine(int length, int w, int x1, int x2, int y) {
        int[] result=new int[length];
        int width=w/32;
        if(y>(length/width)){
            return result;
        }
        if(x2>=w){
            return result;
        }
        //定位行数所在的下标
        int index=y*width;

        //firstIndex为第一个数x1所在的下标

        int firstIndex=x1/32+y*w/32;
        //secondIndex为第二个数x2所在的下标
        int secondIndex=x2/32+y*w/32;

        for(int i=firstIndex;i<=secondIndex;i++){
            result[i]=-1;
        }
        //处理首尾元素
        //头元素采用不带符号位右移
        result[firstIndex]=result[firstIndex]&(-1>>>(x1%32));
        //尾元素采用带符号位右移
        result[secondIndex]=result[secondIndex]&(Integer.MIN_VALUE>>(x2%32));
        return result;
    }
}
```

至于最后首尾元素是与出来的，还是异或出来的，自己再纸上画两笔就知道了。

### 课后总结

关于位元素的右移与左移位数我总是搞不清。这里以32bit的数为例：

一个bit在int中最多移动31位无论是左移还是右移。如果左移，那么当前被移动的1为整个int贡献的值为$2^{index}$,index为当前bit所在的下标，范围为[31，0]，从右往左数。

如果原来的index为3，左移i位，新的index就为3+i。同理右移i位，新的index为3-i。(这里的下标都是从左往右数的)。

这个总结实在是没啥营养，建议不看，纯粹给自己记个没用的规律。