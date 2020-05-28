---
title: 56. 合并区间
mathjax: true
data: 2020-05-29 00:26:19
updated:
tags:
- [数组,贪心]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/merge-intervals/)

### 0x1 题目详情

》给出一个区间的集合，请合并所有重叠的区间。

---

**测试用例:**

>示例 1:
输入: \[[1,3],[2,6],[8,10],[15,18]]
输出: \[[1,6],[8,10],[15,18]]
解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
示例 2:

>输入: \[[1,4],[4,5]]
输出: \[[1,5]]
解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。

### 0x2 解题思路

这题又是贪心了，这题二进宫了，因为做了一次，所以知道怎么搞。这里需要根据区间的左端点排序，如果两个区间能够合并，排序两个区间一定挨在一起。
可以根据结果集中的最后一组数据与当前处理数据进行判断并合并。这里肯定会产生很多问题，为什么需要对区间的左端点排序，怎么想出来的？

首先这道题是区间类的题目，对于几何问题，一般都需要写写画画打下草稿。还需要打打草稿的比如：

- 几何类
- 物理类
- 递归路径树类

至于为什么选择要对区间的左端点排序，说实话，我也不知道，靠直觉，贪心就是靠直觉。

### 0x3 代码实现

时间复杂度为$O(NlogN)$,空间复杂度最坏为$O(N)$，也就是没有区间合并。

``` java
class Solution {
    public int[][] merge(int[][] intervals) {

        if(intervals==null){
            return null;
        }
        Arrays.sort(intervals,new Comparator<int[]>(){
            public int compare(int[] lhs,int[] rhs){
                return lhs[0]-rhs[0];
            }
        });

        ArrayList<int[]> result=new ArrayList<>();
        result.add(intervals[0]);
        for(int i=0;i<intervals.length;i++){

            //获得最后一个元素
            int[] lastElem=result.get(result.size()-1);
            //不需要合并时，直接加入结合
            if(lastElem[1]<intervals[i][0]){

                result.add(intervals[i]);
            }else{
                lastElem[1]=Math.max(lastElem[1],intervals[i][1]);
            }
        }
        return result.toArray(new int[result.size()][]);
    }
}
```

### 0x4 课后总结

贪心这种思想吧，只要能想出来，代码很简单的，关键是贪心策略不好想啊。

使用贪心策略的前提是：局部最优解可能会产生全局最优解。并且贪心策略是**无后效性**的，即我们怎么到达当前状态的与后续的状态选择没有关系。在考虑是否使用贪心时有两个维度：

- 具备**局部最优解可能会产生全局最优解**的特点
- 找不到反例，但是又不能推翻当前的策略，那么就可以尝试使用贪心。

证明贪心是非常困难的，但是推翻当前策略却只需要一个反例。

贪心算法的应用

- 对数据压缩编码的霍夫曼编码（Huffman Coding）
- 求最小生成树的 Prim 算法和 Kruskal 算法
- 求单源最短路径的Dijkstra算法

[参考链接](https://leetcode-cn.com/problems/merge-intervals/solution/tan-xin-suan-fa-java-by-liweiwei1419-3/)