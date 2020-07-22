---
title: 寻找公共节点
mathjax: true
data: 2020-07-22 18:11:07
updated:
tags:
- 二叉树
- 最近公共节点
categories:
- algorithm
---
## 前言

因为235-BST的最近公共祖先与236-二叉树的最近公共祖先非常相似,这里就放到一起讲解了,都不是很难。

[235. 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

[236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)


---

## 235-二叉搜索树的最近公共祖先

### 0x0 题目详情

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

![235](images/235-binarysearchtree_improved.png)

**测试用例:**

>示例 1:
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
输出: 6 
解释: 节点 2 和节点 8 的最近公共祖先是 6。

>示例 2:
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
输出: 2
解释: 节点 2 和节点 4 的最近公共祖先是 2, 因为根据定义最近公共祖先节点可以为节点本身。


### 0x1 解题思路

这道题可以用下面的寻找普通二叉树最近公共节点的方法来做,但是这样就完全没有利用到BST的性质,也不是题目的本意。

这道题利用BST,有一个非常简单的解法。首先我们需要知道:

- 如果p节点和q节点都小于根节点,那么就需要递归去左子树找公共节点
- 如果p节点和q节点都大于根节点,那么就需要递归右子树找公共节点
- 如果一个节点在左子树,一个节点在右子树,这说明什么?说明当前节点就是最近公共节点啊。直接返回当前节点即可

是不是很简单。

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
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root==null){
            return root;
        }
        
        if(p.val<root.val && q.val<root.val){
            return lowestCommonAncestor(root.left,p,q);
        }
        if(p.val>root.val && q.val>root.val){
            return lowestCommonAncestor(root.right,p,q);
        }
        return root;
        
    }
}
```

### 0x3 课后总结

可惜了,我第一次做的时候就没有利用到BST的特性。

所以说明,如果题目中给了某种带有鲜明特点的数据结果,解题方法肯定要往这个鲜明特点上靠的。

## 236-二叉树的最近公共祖先

### 0x0 题目详情

>给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。
例如，给定如下二叉树:  root = [3,5,1,6,2,0,8,null,null,7,4]

![236](images/236-binarytree.png)

---

**测试用例:**

>示例 1:
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出: 3
解释: 节点 5 和节点 1 的最近公共祖先是节点 3。

>示例 2:
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出: 5
解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。

### 0x1 解题思路

这道题比235题难点,因为需要考虑的情况的情况非常多,下面的代码我也不是很懂,全靠肌肉记忆写出来了,有机会再好好研究吧。

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
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        //如果找到了p、q其中的任何一个节点,那么就返回当前节点
        if(root==null || root==p || root==q){
            return root;
        }
        TreeNode left=lowestCommonAncestor(root.left,p,q);
        TreeNode right=lowestCommonAncestor(root.right,p,q);
        //如果左子树的结果不为空,右子树的结果也不为空,那么就说明p和q我们都找到了,
        //当前节点就是最近公共节点
        //因为最近公共节点的特征就是就是左右各有一个目标节点
        if(left!=null && right!=null){
            return root;
        //如果有一个为空,那么就返回我们找到的其中一个节点,向上传递信息,表示我们找到了其中一个
        }else if(left!=null){
            return left;
        }else{
            return right;
        }
    }
}
```
### 0x3 课后总结

这份代码还是比较牛的,将各种情况都考虑到了一起。使用别的递归代码复杂度比这个高。