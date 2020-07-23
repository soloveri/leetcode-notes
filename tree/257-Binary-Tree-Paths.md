---
title: 257. 二叉树的所有路径
mathjax: true
data: 2020-07-09 13:44:26
updated:
difficulty: easy
tags:
- [递归,二叉树]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/binary-tree-paths/)

---

## 0x0 题目详情

>给定一个二叉树，返回所有从根节点到叶子节点的路径。
说明: 叶子节点是指没有子节点的节点。

---
**测试用例:**

输入:

      1
     /  \
    2    3
    \
     5


>输出: ["1->2->5", "1->3"]
解释: 所有根节点到叶子节点的路径为: 1->2->5, 1->3

## 0x1 解题思路

这道题本身没有什么难的,就是数字转字符串的小细节需要注意。如果我们在StringBuilder中加入一个数字`-50`,这在StringBuilder中就算3个字符了,如果想要移除`-50`,那么移除的长度就为3。除了这点,这题没啥好说的了。就是由上到下递归收集路径,最后到达叶节点时将收集的路径存放至结果中。然后逐层将路径的每个节点从StringBuilder中移除,避免影响递归结果。

## 0x2 代码实现

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
    List<String> result;
    StringBuilder sb;
    public List<String> binaryTreePaths(TreeNode root) {
        result=new ArrayList<>();
        if(root==null){
            return result;
        }
        sb=new StringBuilder();
        recur(root);
        return result;

    }
    private void recur(TreeNode root){
        if(root.left==null && root.right==null){
            sb.append(root.val);

            result.add(sb.toString());
            int len=String.valueOf(root.val).length();
            sb.delete(sb.length()-len,sb.length());
            return;
        }
        sb.append(root.val).append('-').append('>');
        
        if(root.left!=null){ 
            recur(root.left); 
        }

        if(root.right!=null){
            recur(root.right);
        }

        int len=String.valueOf(root.val).length();
        sb.delete(sb.length()-len-2,sb.length());
    }
}

```

## 课后总结

**数字转字符串的长度问题一定要注意**,在数学中,我们将`-50`定义为一个数字,而在字符串中,就是三个字符了奥!

