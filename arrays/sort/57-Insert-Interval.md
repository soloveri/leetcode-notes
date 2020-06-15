---
title: 57. 插入区间
mathjax: true
data: 2020-06-15 22:52:42
updated:
tags:
- [双指针]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/insert-interval/)

### 0x0 题目详情

>给出一个无重叠的 ，按照区间起始端点排序的区间列表。
在列表中插入一个新的区间，你需要确保列表中的区间仍然有序且不重叠（如果有必要的话，可以合并区间）。

---

**测试用例:**

>示例 1:
输入: intervals = [[1,3],[6,9]], newInterval = [2,5]
输出: [[1,5],[6,9]]

>示例 2:
输入: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
输出: [[1,2],[3,10],[12,16]]
解释: 这是因为新的区间 [4,8] 与 [3,5],[6,7],[8,10] 重叠。


### 0x1 解题思路

这道题跟第56题[合并区间](../../greedy/56-Merge-Intervals.md)的思路一致，因为给出的区间数组已经有序了，我们要做的只是找到插入新区间的正确位置。而有序、找位置这些动作足以提示我们使用二分法找位置。

所以我们可以首先是由二分法找到合适插入位置，然后对插入位置后的区间进行合并操作。插入位置之前的区间我们不用管，因为不可能有合并操作。

### 0x2 代码实现

``` java
class Solution {
    public int[][] insert(int[][] intervals, int[] newInterval) {
        if(intervals==null && newInterval==null){
            return null;
        }
        if(intervals==null){
            return new int[][]{newInterval};
        }
        if(newInterval==null){
            return intervals;
        }


        //先找到插入点
        int left=0;
        int right=intervals.length;
        int mid=0;
        while(left<right){
            mid=left+(right-left)/2;
            if(intervals[mid][0]>=newInterval[0]){
                right=mid;
            }else{
                left=mid+1;
            }
        }
        ArrayList<int[]> list=new ArrayList<>();

        for(int i=0;i<right;i++){
            list.add(intervals[i]);

        }
        //如果插入位置为0，表示新区间要放到第一个位置，后面所有的区间都可能产生合并操作
        if(right==0){
            list.add(newInterval);
        //否则先将新区间插入结果中，再查看本次操作是否可能产生合并操作
        }else{
            int[] lastInterval=list.get(list.size()-1);
            if(lastInterval[1]<newInterval[0]){
                list.add(newInterval);
            }else{
                lastInterval[1]=Math.max(lastInterval[1],newInterval[1]);
            }
        }
        //剩下就是合并右半部分的区间
        for(int i=right;i<intervals.length;i++){
            int[] lastInterval=list.get(list.size()-1);
            if(lastInterval[1]<intervals[i][0]){
                list.add(intervals[i]);
            }else{
                lastInterval[1]=Math.max(lastInterval[1],intervals[i][1]);
            }
        }
        return list.toArray(new int[0][0]);
    }
}

```

### 0x3 课后总结

合并区间，对所有区间进行排序是必要的。排序后区间如果能够合并，那么就一定相互紧邻!