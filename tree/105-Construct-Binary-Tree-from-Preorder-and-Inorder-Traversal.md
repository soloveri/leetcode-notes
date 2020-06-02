---
title: 105. 从前序与中序遍历序列构造二叉树
mathjax: true
data: 2020-06-02 23:46:22
updated:
tags:
- [递归,二叉树]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

### 0x0 题目详情

> 根据一棵树的前序遍历与中序遍历构造二叉树。
注意:
你可以假设树中没有重复的元素。

---

**测试用例:**
>例如，给出
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]

### 0x1 解题思路

这道题呢，做了三遍了，这次也是一遍过。还好。对于一颗没有重复元素的二叉树，前序+中序或者中序+后序可以完全确定一颗二叉树。但是前序+后序却不能完全确定一颗二叉树。

对于前序遍历的区间，区间内的第一个元素肯定是根节点，无论是左子树还是右子树。我们就是通过构造每一个根节点来构建二叉树的。剩下的动作就是构造左子树和右子树。这里确定左右子树的区间是需要长度数据的，而长度数据是可以通过中序遍历得出来的。在中序遍历中当前root节点左边的长度就是左子树的长度，右边就是右子树的长度。这样不断构造每个根节点，直到区间的长度为0，表示二叉树已经构造完毕。

这里通过中序遍历获得长度可以通过hashmap记录中序遍历中的每个元素的索引，加快计算长度。

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
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if(preorder==null || inorder==null || preorder.length==0 || inorder.length==0){
            return null;
        }
        //key为当前值，value为下标
        Map<Integer,Integer> indexMap=new HashMap<>();
        for(int i=0;i<inorder.length;i++){
            indexMap.put(inorder[i],i);
        }
        return recur(preorder,indexMap,0,preorder.length,0,inorder.length);
    }
    //区间采取左闭右开的方式
    private TreeNode recur(int[] preorder,Map<Integer,Integer> indexMap,int preBegin,int preEnd,int inBegin,int inEnd){
        if(preBegin>=preEnd){
            return null;
        }
        TreeNode root=new TreeNode(preorder[preBegin]);

        int pivot=indexMap.get(preorder[preBegin]);

        //计算左子树区间长度
        int leftLength=pivot-inBegin;
        //计算右子树区间长度
        int rightLength=inEnd-pivot-1;
        //先构建左子树
        root.left=recur(preorder,indexMap,preBegin+1,preBegin+1+leftLength,inBegin,pivot);
        //再构建右子树
        root.right=recur(preorder,indexMap,preBegin+1+leftLength,preBegin+1+leftLength+rightLength,pivot+1,inEnd);
        return root;
    }
}

```

### 0x3 课后总结

这道题是固定解法了，需要记住先序+后序不能够完全确定一颗二叉树。先序+中序虽然可以完全确定一颗二叉树，但是树中不能有重复元素，并且从前往后构建先构建左子树，再构建右子树。