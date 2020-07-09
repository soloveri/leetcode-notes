---
title: 98. 验证二叉搜索树
mathjax: true
data: 2020-07-09 13:53:29
updated:
tags:
- [递归,二叉搜索树]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/validate-binary-search-tree/)


### 0x0 题目详情

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

节点的左子树只包含小于当前节点的数。
节点的右子树只包含大于当前节点的数。
所有左子树和右子树自身必须也是二叉搜索树。

### 0x1 解题思路

二叉搜索树就是中序遍历！！！
二叉搜索树就是中序遍历！！！
二叉搜索树就是中序遍历！！！
重要的话说三遍，遍历二叉搜索树就是树的中序遍历，所以验证二叉搜索树也可以采用中序遍历的方法来判断，如果中序遍历不是严格升序的，那么该树就不是二叉搜索树。当然了，我们还需要维护一个前向节点，才能验证是否严格升序。

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
    Integer pre=null;
    public boolean isValidBST(TreeNode root) {
        if(root==null){
            return true;
        }
        return recur(root);
    }

    private boolean recur(TreeNode root){
        if(root==null){
            return true;
        }
        if(!recur(root.left)){
            return false;
        }
        if(pre!=null && root.val<=pre){
            return false;
        }
        pre=root.val;
        if(!recur(root.right)){
            return false;
        }
        return true;
    }
}

```

### 0x3 课后总结

二叉搜索树跟中序遍历联系紧密！！！
二叉搜索树跟中序遍历联系紧密！！！
二叉搜索树跟中序遍历联系紧密！！！

这句话一定要牢牢记住哦。