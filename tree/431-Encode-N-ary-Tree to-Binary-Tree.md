---
title: 431. 将 N 叉树编码为二叉树
mathjax: true
data: 2020-07-29 19:16:11
updated:
tags:
- 二叉树
- N叉树
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/encode-n-ary-tree-to-binary-tree/)

---

### 0x0 题目详情

>设计一个算法，可以将 N 叉树编码为二叉树，并能将该二叉树解码为原 N 叉树。一个 N 叉树是指每个节点都有不超过 N 个孩子节点的有根树。类似地，一个二叉树是指每个节点都有不超过 2 个孩子节点的有根树。你的编码 / 解码的算法的实现没有限制，你只需要保证一个 N 叉树可以编码为二叉树且该二叉树可以解码回原始 N 叉树即可。

### 0x1 解题思路

将N叉树转为二叉树有一个比较通用的规则:

二叉树的左子节点存放孩子,右子节点存放第一个孩子原来的兄弟,如果有多个孩子,可以从最右边的孩子开始转换。下面就是实现这个规则的代码。

注意,在写递归函数时,一定一定一定要搞清楚这个递归函数返回的是什么。

- 在encode部分,递归函数`recur`实现的是输入一个节点作为N叉树的根节点,返回以输入节点为根的二叉树
- 在decode部分,递归函数`rebuild`实现的是输入一个根节点,输出的是以输入节点为根的N叉树,注意这里转换的节点仅包括输入节点的左孩子节点,,输入节点的右孩子是不管的,因为只有左孩子才是输入节点真正的孩子。

### 0x2 实现代码

``` java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
};
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

class Codec {
    // Encodes an n-ary tree to a binary tree.
    public TreeNode encode(Node root) {
        if(root==null){
            return null;
        }
        return recur(root);
        
    }
    private TreeNode recur(Node root){
        if(root.children.size()==0){
            return new TreeNode(root.val);
        }

        TreeNode right=null;
        for(int i=root.children.size()-1;i>-1;i--){
            //对孩子节点的转换是从右往左转换的,因为右孩子都会转为其左边兄弟的右节点,自然要逆序递归
            TreeNode current=recur(root.children.get(i));
            current.right=right;
            right=current;
        }
        TreeNode newRoot=new TreeNode(root.val);
        newRoot.left=right;
        return newRoot;
    }
	
    // Decodes your binary tree to an n-ary tree.
    public Node decode(TreeNode root) {
        if(root==null){
            return null;
        }
        return rebuild(root);
        
    }
    //输入一个TreeNode,返回对应的n叉树
    private Node rebuild(TreeNode root){
        // if(root.left==null && root.right==null){
        //     return new Node(root.val,new ArrayList<Node>());
        // }
        //如果没有左节点,那么直接返回当前节点
        if(root.left==null){
            return new Node(root.val,new ArrayList<Node>());
        }
        Node current=new Node(root.val,new ArrayList<Node>());
        if(root.left!=null){
            current.children.add(rebuild(root.left));
        }
        //说明超过了一个孩子
        if(root.left.right!=null){
            TreeNode most_right=root.left.right;
            //这里的循环是在处理root节点的多个孩子问题,因为root的孩子都会作为右孩子链接为root左孩子的右孩子
            while(most_right!=null){
                Node temp=rebuild(most_right);

                current.children.add(temp);
                most_right=most_right.right;
            }
        }
        return current;
    }
}

```

### 0x3 课后总结

N叉树转二叉树的普遍规则就是:根节点的左节点为孩子,右节点为孩子的兄弟。