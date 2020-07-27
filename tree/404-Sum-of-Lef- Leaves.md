---
title: 404. 左叶子之和
mathjax: true
data: 2020-07-27 19:36:01
updated:
difficulty: easy
tags:

categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/sum-of-left-leaves/)

---

### 0x0 题目详情

>计算给定二叉树的所有左叶子之和。

---

**测试用例:**

        3
       / \
      9  20
        /  \
       15   7

在这个二叉树中，有两个左叶子，分别是 9 和 15，所以返回 24
 
### 0x1 解题思路

这道题的关键在于判断叶节点是左边的还是右边的。但是这道题是采用up-to-down还是down-to-up无所谓。因为答案只跟叶节点有关。

判断叶节点是左边还是右边的方法是只在属于左子树的递归中计算结果。

### 0x2 实现代码

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
    private int result;
    public int sumOfLeftLeaves(TreeNode root) {
        if(root==null){
            return 0;
        }
        if(root.left!=null){
            //只在属于左子树的递归中计算结果
            sumOfLeftLeaves(root.left);
            if(root.left.left==null && root.left.right==null){
                result+=root.left.val;
            }
        }
        sumOfLeftLeaves(root.right);
        return result;
    }
}
```

### 0x3 课后总结

简单题就是简单奥。