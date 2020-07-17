---
title: 124. 二叉树中的最大路径和
mathjax: true
data: 2020-07-09 13:44:26
difficulty: hard
updated:
tags:
- [递归,二叉树]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

---

### 0x0 题目详情

>给定一个非空二叉树，返回其最大路径和。
本题中，路径被定义为一条从树中任意节点出发，达到任意节点的序列。该路径至少包含一个节点，且不一定经过根节点。

---

**测试用例:**

>示例 1:
输入: [1,-2,3]

       1
      / \
     -2   3

输出: 4

>示例 2:
输入: [-10,9,20,null,null,15,7]

       -10
       / \
      9  20
        /  \
       15   7

输出: 42

### 0x1 解题思路

这道题的难点在于路径和的起点与终点未定。不是从根节点开始也不是在叶子节点结束。所以递归函数仅仅返回一个值是不够的。因为如果只返回一个值不能保证路径的连续性。要么每条路径都会加上当前节点的值,这是错的。要么都不加,但是又可能会错过最大值。所以到底加还是不加?这个问题有点难度奥。

所以我们可以返回一个结构体,里面的两个数组一个是当前的最大值,一个是当前节点产生的最大值。或者直接在类里面设置一个属性,都可以。我采用的是设置类的属性。

产生最大值的情况有两种:一种是直上直下的,例如示例1中的3->1;还有一种是要经过某一个节点打弯的,例如示例2中的15->20->7。所以一个节点能够产生的最大值的情况有以下四种:

在处理每个节点时,当前节点产生的最大值是一定要包含当前节点的。所以有三种情况:

- 左子树最大值+右子树最大值+当前节点值
- 左子树最大值+当前节点值
- 右子树最大值+当前节点值
- 只包含当前节点

ok,获得了当前节点产生的最大值,然后再和全局最大值比较取较大的。这样求最大值时就不会错过节点,因为每个节点都参与了最大值的生成。

### 0x2 代码实现

``` java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    private int result=Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        if(root==null){
            return -1;
        }
        recur(root);
        return result;
    }
    //当回溯到一个节点时,需要计算出当前节点作为根节点的最大路径
    //然后再看需不需要带上左右子节点，然后把单挑路径的值往上传

    private int recur(TreeNode node){
        if(node==null){
            return 0;
        }
        int left=recur(node.left);
        int right=recur(node.right);
        int current=node.val+left+right;
        result=Math.max(Math.max(current,node.val),result);
        // result=Math.max(result,node.val);
        result=Math.max(result,Math.max(node.val+left,node.val+right));
        return Math.max(node.val,Math.max(node.val+left,node.val+right));
    }
}
```

### 0x3 课后总结

