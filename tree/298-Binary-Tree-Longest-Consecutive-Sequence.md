---
title: 298. 二叉树最长连续序列
mathjax: true
data: 2020-07-27 18:28:26
updated:
difficulty: 
tags:
- 二叉树
- 递归
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/binary-tree-longest-consecutive-sequence/)

---

### 0x0 题目详情

给你一棵指定的二叉树，请你计算它最长连续序列路径的长度。

该路径，可以是从某个初始结点到树中任意结点，通过「父 - 子」关系连接而产生的任意路径。

这个最长连续的路径，必须从父结点到子结点，反过来是不可以的。


---

**测试用例:**

>示例 1：
输入:

       1
        \
         3
        / \
       2   4
            \
             5

>输出: 3
解析: 当中，最长连续序列是 3-4-5，所以返回结果为 3

>示例 2：
输入:

       2
        \
         3
        / 
       2    
      / 
     1

>输出: 2 
解析: 当中，最长连续序列是 2-3。注意，不是 3-2-1，所以返回 2。


### 0x1 解题思路

这道题从上到下收集答案或者从下到上收集答案都可。不过从下到上有点小麻烦,因为收集的答案有两个,所以在处理当前节点时需要考虑的情况比较多,比如左子树到当前节点是连续的?右子树到当前节点是连续的?还是左子子树到当前字节都是连续的?左右子树收集的答案是否有效?等等。很麻烦。

而从上到下就很简单了,因为上一层只会传一个答案过来,考虑的情况就没那么多。如果上层到当前节点不是连续的,那么就需要更新最大长度,否则在叶子节点结算最大的长度。


### 0x2 实现代码

``` java "up-to-down"
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
    int res;
    public int longestConsecutive(TreeNode root) {
        if(root==null){
            return 0;
        }
        res=Integer.MIN_VALUE;
        recur(root,1);
        return res;

    }
    private void recur(TreeNode root,int result){
        if(root.left==null && root.right==null){
            res=Math.max(res,result);
            return;
        }
        if(root.left!=null){
            if(root.left.val-1==root.val){
                //上层到当前节点是连续的
                recur(root.left,result+1);
            }else{
                //上层到当前节点不是连续的,更新最大值
                res=Math.max(res,result);
                recur(root.left,1);
            }
        }
        //对右子树做同样的处理
        if(root.right!=null){
            if(root.right.val-1==root.val){
                res=Math.max(res,result+1);
                recur(root.right,result+1);
            }else{
                res=Math.max(res,result);
                recur(root.right,1);
            }
        }
        
    }
}

```

---

``` java "down-to-up"
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
        private int base;
        private int cur;
        Info(int base,int cur){
            this.base=base;
            this.cur=cur;
        }
    }
    public int longestConsecutive(TreeNode root) {
        if(root==null){
            return 0;
        }
        result=Integer.MIN_VALUE;
        recur(root);
        return result;
        
    }
    private Info recur(TreeNode root){
        if(root.left==null && root.right==null){
            result=Math.max(result,1);
            return new Info(root.val,1);
        }
        Info left=null;
        Info right=null;
        if(root.left!=null){
            left=recur(root.left);
        }
        if(root.right!=null){
            right=recur(root.right);
        }
        //需要考虑的情况非常多,麻烦
        if(root.left!=null && root.right!=null){
            if(root.val!=left.base-1 && root.val!=right.base-1){
                result=Math.max(result,1);
                return new Info(root.val,1);
            }
            if(root.val==left.base-1&& root.val==right.base-1){
                result=Math.max(result,Math.max(left.cur+1,right.cur+1));
                return new Info(root.val,Math.max(left.cur+1,right.cur+1));
            }else if(root.val == left.base -1){
                result=Math.max(result,left.cur+1);
                return new Info(root.val,left.cur+1);
            }else{
                result=Math.max(result,right.cur+1);
                return new Info(root.val,right.cur+1);
            }
        }
        else if(root.left!=null){
            if(root.val!=left.base-1){
                result=Math.max(result,1);
                return new Info(root.val,1);
            }
            result=Math.max(result,left.cur+1);
            return new Info(root.val,left.cur+1);
        }else{
            if(root.val!=right.base-1){
                result=Math.max(result,1);
                return new Info(root.val,1);
            }
            result=Math.max(result,right.cur+1);
            return new Info(root.val,right.cur+1);

        }
    }
}
```

### 0x3 课后总结

如果从下到上汇集当前节点的结果时,如果需要考虑的情况非常多,可以考虑从上到下的解法,因为从上到下只会给当前层传一个结果。