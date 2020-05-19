---
title: 二分搜索的常见用法总结
tags: 
- binary search
categories: 
- algorithm
- search
---

ps：这篇文章写的有点乱，后续有时间我会修正了的。

---

## 二分搜索的几种常见变体

简要记录一下我学习的过程，这里聊聊利用二分搜索查找**非降序序列**（非升序也可以，只不过麻烦一点）某个值位置的四种情形：

- [查找>=value的下界](#1)
- [查找>value的下界](#2)
- [查找<value的上界](#3)
- [查找<=value的上界](#4)
  
其实只需掌握第一种情况的思路，就可以应付全部的四种情况，这里有两点需要注意。
首先，区间形式为**左闭右开**, 采用左闭右开区间是因为方便更新begin与end的值，并且区间长度为(end-begin),比如区间[0,4),此区间的长度为(4-0)=4,非常方便，**同时当区间begin=end时，区间长度就为0**；其次查找思路是将**将待查找序列分为三个区间**，这里以查找>=value的第一次出现的位置为例简单讲解一下。首先将待查找序列分为三个左闭右开的区间，分别是：

* 左侧 < value的区间
* 右侧 >= value的区间
* 中间待查找的区间

<!--more-->  
这里举个栗子，假设待查找区间target={1，4，4，5，7，7，9，9，9，11}，区间索引为[0,10)。
三个区间分别为\\**[begin0,begin)**、**[begin,end)**、**[end,end0)**
索引值分别为：  
begin0=begin=target.begin(),end0=end=target,end()
由此可以知道，第1、3区间的长度为0，第2区间长度即为序列的原始长度：10
随后第1、第3两个区间不断向中间收缩，当第2区间的长度为0时，停止搜索，返回begin和end都可，因为begin=end时区间长度为0，并且他们就是最终答案。下面以查找<font color=#FB5936>序列>=6下界</font>的索引为例。

### <span id="1">查找>=value的下界</span>

这是基本代码，其他变种都是基于此的

```c++
std::vector::iterator BinarySearch(std::vector<int> target,int value){
    auto begin=target.begin(),end=target.end();
    auto mid=begin+(end-begin)/2;
    while(begin<end){
        if(*mid<value)
            begin=mid+1;
        else
            end=mid;
        mid=begin+(end-begin)/2;
    }
-   return end;
+   return begin;
}
```

这里有三点需要注意：  

1. 中间索引mid的求法  
mid的求法主要有两种：mid=(begin+end)/2和mid=begin+(end-begin)/2。而第一种可能会产生溢出，所以第二种方法最完美
{% note info %}
mid位置总结以及小技巧：

- 区间长度为偶数，则区间刚好分为相等的两半，mid位于右侧区间第一个索引，即偶数相等右侧
- 区间长度为奇数，则右侧区间长度比左侧多一，mid刚好位于区间正中间，将处mid外的区间分为相等，并且同样mid归属于有区间，即奇数多一右侧
- <font color="green">mid=begin+(end-begin)>>1,除2可以使用移位运算符实现，稍微快一点点</font>
{% endnote %}

2. <font color=#FB5936>begin与end的更新 </font>

> **这一点最重要、最重要、最重要！！！是二分查找的关键，一定要理解**！

* 首先将mid值与目标value比较，并更新begin或end值
    + 如果mid值 < value，则将包含此值的左侧区间合并到第一区间，并更新begin=mid+1；
    + 如果mid值 >= value，则将包含此值的右侧区间合并到第三区间，并更新end=mid；  
* 更新mid值，mid=begin+(end-begin)/2

3. 循环终止条件

终止条件在上文说过了，就是当第二区间的长度为0时就停止  

上述区间的搜索过程为：

- 第一次
mid=5，所在值为7>=6,所以扩大第3区间，更新end=mid=5，begin不变，新的mid=2
- 第二次
mid=2，所在值为4<6,所以扩大第1区间，更新begin=mid+1=3，end不变，新的mid=4
- 第三次
mid=4，所在值为7>=6，继续扩大第3区间，更新end=mid=4，begin不变，新的mid=3
- 第四次
mid=3，所在值为5<6,继续扩大第1区间(这里有一点可以说一下，此时的待搜索区间长度为1，如果begin的更新方式为begin=mid而不是begin=mid+1,那么更新后的begin仍为3，会造成死循环，这就是采用begin=mid+1的原因),更新begin=mid+1=4，end不变，新的mid=4
- 第五次
此时区间长度为0，停止搜索，返回索引为4，为序列中>=6的下界索引

### <span id="2">查找>value的下界</span>

这里求查找>value的下界也很简单，上述的代码只需要改一行，那就是mid所在值与value的比较方式

``` c++
if(*mid<=value>)
```

将第1区间的属性变为<=value,相应的第3区间变为>value,这里返回的值就是>value的下界
> 这里注意，end的值永远都是第3区间的第一元素

### <span id="3">查找<value的上界</span>

查找<value的上界就更简单了，利用查找出的>=value下界减1不就是<value的上界了嘛？嘿嘿

### <span id="4">查找<=value的上界</span>

道理同上，查找<=value的上界，就是>value下界减1

### 返回值总结

- 如果求的是上界(<或<=符号)，且返回值为target.begin()-1,则表示上界不存在
- 如果求的是下界(>或>=符号)，且返回值为target.end(),则表示下界不存在

## c++中的相关函数

- lower_bound(),查找>=value的下界
- upper_bound(),查找>value的下界
- [lower_bound(value),upper_bound(value))就是等于value的区间，也就是equal_range(value)的效果
