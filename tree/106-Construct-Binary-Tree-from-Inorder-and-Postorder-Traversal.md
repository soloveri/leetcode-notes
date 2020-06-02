---
title: 106. 从中序与后序遍历序列构造二叉树
mathjax: true
data: 2020-06-02 23:47:42
updated:
tags:
- [递归,二叉树]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

### 0x0 题目详情

>根据一棵树的中序遍历与后序遍历构造二叉树。
注意:
你可以假设树中没有重复的元素。

---

**测试用例：**
>例如，给出
中序遍历 inorder = [9,3,15,20,7]
后序遍历 postorder = [9,15,7,20,3]

### 0x1 解题思路

这道题跟[105.通过先序和中序构造二叉树](105-Construct-Binary-Tree-from-Preorder-and-Inorder-Traversal.md)只有一点点细微的区别。思路还是一样的。先序+中序构造根节点是通过先序区间中的第一个元素构建根节点，先构建左子树再构建右子树。后序+中序构造根节点是通过后序区间的最后一个元素构建根节点。一个从头开始，一个从尾开始。

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
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        if(inorder==null || inorder.length==0 || postorder==null || postorder.length==0){
            return null;
        }
        Map<Integer,Integer> indexMap=new HashMap<>();
        for (int i=0;i<inorder.length;i++){
            indexMap.put(inorder[i],i);
        }
        return recur(postorder,indexMap,postorder.length-1,-1,inorder.length-1,-1);

    }
    //所有区间采取左开右闭区间
    private TreeNode recur(int[] postorder,Map<Integer,Integer> indexMap,int postBegin,int postEnd,int inBegin,int inEnd){
        if(postBegin<=postEnd){
            return null;
        }
        //先构建右子树，再构建左子树
        TreeNode root=new TreeNode(postorder[postBegin]);
        int pivot=indexMap.get(postorder[postBegin]);
        //计算右子树长度
        int rightLength=inBegin-pivot;
        int leftLength=pivot-inEnd-1;
        root.right=recur(postorder,indexMap,postBegin-1,postBegin-1-rightLength,inBegin,pivot);
        root.left=recur(postorder,indexMap,postBegin-1-rightLength,postBegin-1-rightLength-leftLength,pivot-1,inEnd);
        return root;
    }
}
```

### 0x3 课后总结

通过后序+中序构建二叉树，需要从后往前构建，先构建右子树，再构建左子树。