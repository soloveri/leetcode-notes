---
title: 109. 有序链表转换二叉搜索树
mathjax: true
data: 2020-06-29 09:52:46
updated:
tags:
- [快慢指针,链表]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)

### 0x0 题目详情

>给定一个单链表，其中的元素按升序排序，将其转换为高度平衡的二叉搜索树。
本题中，一个高度平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。

---

**测试用例:**

>示例:
给定的有序链表： [-10, -3, 0, 5, 9],
一个可能的答案是：[0, -3, 9, -10, null, 5], 它可以表示下面这个高度平衡二叉搜索树：
      0
     /  \\
   -3   9
   /    /
 -10  5

### 0x1 解题思路

这道题呢，我一开始想的是用哈希表做，虽然能做出来，但是速度很慢。然后就使用快慢指针找中点的方法，结果直接超过100%。我寻思这不是比我用哈希表还慢呢。

### 0x2 解题思路

粗略分析了一哈，找到每个中点需要花费时间复杂度为O(logN)

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
    public TreeNode sortedListToBST(ListNode head) {
        if(head==null){
            return null;
        }
        return recur(head,null);

    }
    private TreeNode recur(ListNode begin,ListNode end){
        if(begin==end){
            return null;
        }
        ListNode fast=begin;
        ListNode slow=begin;
        while(fast.next!=end && fast.next.next!=end){
            fast=fast.next.next;
            slow=slow.next;
        }
        TreeNode root=new TreeNode(slow.val);
        root.left=recur(begin,slow);
        root.right=recur(slow.next,end);
        return root;

    }
}
```

### 0x3 课后总结

在链表里找中点就是快慢指针，没啥好说的，找个每个中点是O(logN)的复杂度。