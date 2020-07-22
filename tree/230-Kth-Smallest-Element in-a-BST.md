---
title: 156. 上下翻转二叉树
mathjax: true
data: 2020-07-22 19:17:36
updated:
tags:
- 二叉树
- 找规律
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/)

---

## 0x0 题目详情

>给定一个二叉搜索树，编写一个函数 kthSmallest 来查找其中第 k 个最小的元素。
说明：
你可以假设 k 总是有效的，1 ≤ k ≤ 二叉搜索树元素个数。

---

**测试用例:**

>示例 1:
输入: root = [3,1,4,null,2], k = 1
输出: 1

      3
     / \
    1   4
    \
     2


>示例 2:
输入: root = [5,3,6,2,4,null,null,1], k = 3
输出: 3

          5
         / \
        3   6
       / \
      2   4
     /
    1


>进阶：
如果二叉搜索树经常被修改（插入/删除操作）并且你需要频繁地查找第 k 小的值，你将如何优化 kthSmallest 函数？

## 0x1 解题思路

最简单的暴力解法是维持一个全局变量,当遍历的节点个数达到K时,就返回当前节点。

当然,上面这种方法完全没有利用到BST的性质,不能说错,只是偏离主题了吧。原定的解法是计算左子树的节点总数m:

1. 如果m=k-1，说明当前根节点就是第k小节点
2. 如果m>k-1，说明第k小节点在左子树,那么我递归搜索左子树
3. 如果m\<k-1,说明第k小节点在右子树,我们就递归搜索右子树

直到我们找到结果。利用了BST的特性。

## 0x2 代码实现

``` java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int kthSmallest(TreeNode root, int k) {
        if(root==null){
            return 0;
        }
        return calc(root,k);
    }

    //计算当前子树的根节点
    private int countNodes(TreeNode root){
        if(root==null){
            return 0;
        }
        return 1+countNodes(root.left)+countNodes(root.right);
    }
    private int calc(TreeNode root,int k){
        int leftCount=countNodes(root.left);
        if(leftCount+1==k){
            return root.val;
        }else if(leftCount<k-1){
            return calc(root.right,k-leftCount-1);
        }else{
            return calc(root.left,k);
        }
    }
}

```


## 0x3 课后总结

题目中再次包含了特点明显的数据结构(BST),所以正确解法就是往利用该特点的方法上靠。

