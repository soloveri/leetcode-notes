---
title: 79. 单词搜索
mathjax: true
data: 2020-06-02 01:54:31
updated:
tags:
- [递归,动态规划]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/triangle/)

### 0x0 题目详情

>给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。
相邻的结点 在这里指的是 下标 与 上一层结点下标 相同或者等于 上一层结点下标 + 1 的两个结点。

---

**测试用例：**

>例如，给定三角形：
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。

### 0x1 解题思路

这道题呢，说起来还是比较简单的，递归很容易写出来，那么dp也很容易写出来。dp数组的长度就选三角形最底层list的长度即可。因为从下往上，状态的数量是越来越少的。

### 0x2 代码实现

``` java "递归版本"
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        if(triangle==null || triangle.size()==0){
            return 0;
        }
        if(triangle.size()<2){
            return triangle.get(0).get(0);
        }
        return recur(triangle,0,0);
    }
    private int recur(List<List<Integer>> triangle,int row,int col){
        if(row==triangle.size()){
            return 0;
        }
        if(col>=triangle.get(row).size()){
            return 65536;
        }
        int result=Math.min(recur(triangle,row+1,col),recur(triangle,row+1,col+1))
        +triangle.get(row).get(col);
        return result;
    }
}

```

---

``` java "动态规划版本"
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        if(triangle==null || triangle.size()==0){
            return 0;
        }
        if(triangle.size()<2){
            return triangle.get(0).get(0);
        }
        return recur(triangle,0,0);
    }
    private int recur(List<List<Integer>> triangle,int row,int col){
        List<Integer> last=triangle.get(triangle.size()-1);
        int[] dp=new int[last.size()];
        for(int i=0;i<dp.length;i++){
            dp[i]=last.get(i);
        }
        int N=triangle.size()-2;
        int M=dp.length-2;
        for(int i=N;i>=0;i--){
            for(int j=0;j<=M;j++){
                dp[j]=Math.min(dp[j],dp[j+1])+triangle.get(i).get(j);
            }
            M--;

        }
        return dp[0];
    }
}
```

### 0x3 课后总结

简单的单向递归模型，再改动态规划。