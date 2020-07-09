---
title: 96. 不同的二叉搜索树
mathjax: true
data: 2020-07-09 13:44:26
updated:
tags:
- [递归,二叉搜索树]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/unique-binary-search-trees/)


### 0x0 题目详情

给定一个整数 n，求以 1 ... n 为节点组成的二叉搜索树有多少种？

### 0x1 解题思路

这道题与[第95题:构建所有的二叉搜索树](95-Unique-Binary-Search-Trees-II.md)的区别在于这道题是求数量，不需要把二叉树构建出来，按道理来说是比95题简单一点。思路还是一样，首先可以先把递归的方法写出来，然后再改成动态规划的。递归的精髓也是：左子树与右子树之间没有联系，可以任意组合。

### 0x2 代码实现

``` java "递归版本"
class Solution {
    public int numTrees(int n) {
        if(n<2){
            return 1;
        }

        return recur(n);


    }


    private int recur(int n){
        
        if(n<2){
            return 1;
        }
        int result=0;
        for(int i=1;i<=n;i++){
            //因为可以任意组合，所以这里是乘法
            result+=recur(n-i)*recur(i-1);
        }
        return result;
    }
}
```

---

递归写出来，动态规划也是比较好改出来的。

``` java "动态规划"
class Solution {
    public int numTrees(int n) {
        if(n<2){
            return 1;
        }

        return recur(n);


    }

    private int recur(int n){
        int[] dp=new int[n+1];
        dp[0]=1;
        dp[1]=1;

        for(int j=2;j<=n;j++){
            for(int i=1;i<=j;i++){
                dp[j]+=dp[i-1]*dp[j-i];
            }
        }


        return dp[n];
    }
}

```
### 0x3 课后总结

给定一个范围构建所有的二叉搜索树，要点还是每个数都要有成为根节点的机会，并且左范围构建的左子树与右范围构建的右子树没有关系，可以将二者任意组合。