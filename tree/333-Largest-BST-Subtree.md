---
title: 333. 最大BST的子树
mathjax: true
data: 2020-07-27 18:58:21
updated:
difficulty: medium
tags:
- 二叉树
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/largest-bst-subtree/)

---

### 0x0 题目详情

给定一个二叉树，找到其中最大的二叉搜索树（BST）子树，其中最大指的是子树节点数最多的。

注意:
子树必须包含其所有后代。

---

**测试用例:**

>输入: [10,5,15,1,8,null,7]

         10 
         / \ 
        5  15 
       / \   \ 
      1   8   7

>输出: 3
解释: 高亮部分为最大的 BST 子树。
返回值 3 在这个样例中为子树大小。
进阶:
你能想出用 O(n) 的时间复杂度解决这个问题吗？

### 0x1 解题思路

这道题很明显是要采用从down-to-up的方法求解。因为一颗BST要求左子树是BST并且左子树是BST并且当前节点也符合BST的规则。

所以思路就很简单:

- 首先查看左右子树是否为BST,如果不是直接返回
- 查看当前节点是否符合BST的规则,即是否大于左子树的最大值,小于右子树的最小值,如若不是则返回
- 收集当前最大BST的节点树并返回当前树的最大值与最小值


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
    int result;
    private class Info{
        int max;
        int min;
        int count;
        boolean isBST;
        Info(int max,int min,boolean isBST,int count){
            this.max=max;
            this.min=min;
            this.isBST=isBST;
            this.count=count;
        }
        Info(){}
    };
    public int largestBSTSubtree(TreeNode root) {
        if(root==null){
            return 0;
        }
        recur(root);
        return result;
    }

    private Info recur(TreeNode root){
        if(root==null){
            return new Info(Integer.MIN_VALUE,Integer.MAX_VALUE,true,0);
        }
        Info left=recur(root.left);
        Info right=recur(root.right);
        //先判断左子树和右子树是不是BST,如果不是,本层也不用判断了
        if(!left.isBST || !right.isBST){
            return new Info(Integer.MIN_VALUE,Integer.MAX_VALUE,false,0);
        }
        //左右子树都是BST,判断本层是不是BST,并且需要更新result
        if(root.val<=left.max || root.val>=right.min){
            return new Info(Integer.MIN_VALUE,Integer.MAX_VALUE,false,0);
        }
        //本层已经是BST了,更新最大BST的节点数
        result=Math.max(result,left.count+right.count+1);
        Info info=new Info();
        info.count=left.count+right.count+1;
        info.isBST=true;
        //收集当前层的最大值与最小值
        info.min=Math.min(root.val,Math.min(left.min,right.min));
        info.max=Math.max(root.val,Math.max(left.max,right.max));
        // if(root.left== null && root.right==null){
        //     info.max=root.val;
        //     info.min=root.val;
        // }
        // //这里如果不使用else,那么如果左右节点都为空,还是会执行最后一个else
        // else if(root.left!=null && root.right!=null){
        //     info.min=left.min;
        //     info.max=right.max;
        // }else if(root.left!=null){
        //     info.min=left.min;
        //     info.max=root.val;
        // }else{
        //     info.min=root.val;
        //     info.max=right.max;
        // }
        return info;
    }
}

```

### 0x3 课后总结

查看树是不是BST一看就是down-to-up的解法辣。

左子树是BST、右子树是BST、当前树也是BST,这棵树才叫BST。