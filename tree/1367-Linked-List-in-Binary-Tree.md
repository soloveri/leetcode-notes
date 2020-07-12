---
title: 1367. 二叉树中的列表
mathjax: true
data: 2020-07-12 20:58:49
updated:
tags:
- [双递归,二叉搜索树]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/linked-list-in-binary-tree/)

---

### 0x0 题目详情
给你一棵以 root 为根的二叉树和一个 head 为第一个节点的链表。

如果在二叉树中，存在一条一直向下的路径，且每个点的数值恰好一一对应以 head 为首的链表中每个节点的值，那么请你返回 True ，否则返回 False 。

一直向下的路径的意思是：从树中某个节点开始，一直连续向下的路径,如下图所示:

![1367](images/1367.png)

---

**测试用例:**

>示例 1：
输入：head = [4,2,8], root = [1,4,4,null,2,2,null,1,null,6,8,null,null,null,null,1,3]
输出：true
解释：树中蓝色的节点构成了与链表对应的子路径。

>示例 2：
输入：head = [1,4,2,6], root = [1,4,4,null,2,2,null,1,null,6,8,null,null,null,null,1,3]
输出：true

>示例 3：
输入：head = [1,4,2,6,8], root = [1,4,4,null,2,2,null,1,null,6,8,null,null,null,null,1,3]
输出：false
解释：二叉树中不存在一一对应链表的路径。


### 0x1 解题思路

这道题我一开始的思路是从根节点开始比，如果未能完全匹配，那么则表示不能匹配。后来发现这样完全是错的，因为我们不知道从二叉树的哪一个节点开始，我们不能先入为主的认定就是从二叉树的根节点开始匹配。所以在答案区我发现一种双函数的巧妙递归解决了从任意节点开始匹配链表的问题。具体问题在代码注释里会讲。


### 0x2 代码实现

``` java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
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
    public boolean isSubPath(ListNode head, TreeNode root) {
        if(head==null){
            return true;
        }
        if(root == null){
            return false;
        }
        //因为我们不知道从哪一个节点开始匹配，所以我们首先从根节点开始，如果从根节点开始匹配不成功，那么我们会回溯到
        //第一次还未进入递归函数的位置，这里head又是指向链表的头节点的，所以我们根本不用操作匹配到一半失败怎么办
        //这样会递归执行isSubPath从节点的左右子节点开始匹配，不行的话又会从下一节点的左右字节点开始匹配
        return recur(head,root)|| isSubPath(head,root.left)|| isSubPath(head,root.right);

    }

    private boolean recur(ListNode head,TreeNode root){
        if(head==null){
            return true;
        }
        if(root==null){
            return false;
        }
        if(head.val !=root.val){
            return false;
        }
        return recur(head.next,root.left)|| recur(head.next,root.right);
    }
}
```
### 0x3 课后思路

递归不只是递归调用一个函数，还可以递归函数调用递归函数，两个递归函数相互调用，递归能玩的花样还是挺多的。