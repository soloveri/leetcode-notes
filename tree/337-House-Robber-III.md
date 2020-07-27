---
title: 337. 打家劫舍 III
mathjax: true
data: 2020-07-27 18:18:23
updated:
difficulty: 
tags:
- 二叉树
- 递归
- 
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/house-robber-iii/)

---

### 0x0 题目详情

>在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。
计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

---

**测试用例:**

>示例 1:
输入: [3,2,3,null,3,null,1]
输出: 7 
解释: 小偷一晚能够盗取的最高金额 = 3 + 3 + 1 = 7.

        3
       / \
      2   3
       \   \ 
        3   1

>示例 2:
输入: [3,4,5,1,3,null,1]
输出: 9
解释: 小偷一晚能够盗取的最高金额 = 4 + 5 = 9.

         3
        / \
       4   5
      / \   \ 
     1   3   1


### 0x1 解题思路

很明显,这又是一道先递归到子节点,然后由下到上回溯收集信息的二叉树的题目。

对于一个节点,我们有偷与不偷两种选择,所以在处理当前节点时,我们就需要返回偷当前节点与不偷当前节点的收益值。并且不能连续偷两个节点。

所以对于叶节点,不偷就是收益为0,偷就是当前节点值。而对于普通节点,如果偷当前节点了,那么就需要下一层不偷的收益。如果不偷当前节点,那么下一层我们可以选择偷与不偷。

还是比较简单的奥。

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
    private class Info{
        int rob;
        int noRob;
        Info(){};
        Info(int rob,int noRob){
            this.rob=rob;
            this.noRob=noRob;
        }
    };
    public int rob(TreeNode root) {
        if(root==null){
            return 0;
        }
        Info result=recur(root);
        return Math.max(result.rob,result.noRob);

    }
    private Info recur(TreeNode root){
        if(root.left==null && root.right==null){
            return new Info(root.val,0);
        }
        Info left=root.left!=null?recur(root.left):new Info(0,0);
        Info right=root.right!=null?recur(root.right):new Info(0,0);
        Info current=new Info(0,0);
        //偷当前节点,只能与下一层不偷的收益做计算
        current.rob=root.val+left.noRob+right.noRob;
        // current.noRob=Math.max(left.rob+right.rob,current.noRob);
        // current.noRob=Math.max(left.noRob+right.noRob,current.noRob);
        // current.noRob=Math.max(left.rob+right.noRob,current.noRob);
        // current.noRob=Math.max(left.noRob+right.rob,current.noRob);
        //不偷当前节点,选择下一层偷和不偷较高收益和当前节点利益计算
        current.noRob+=Math.max(left.rob,left.noRob);
        current.noRob+=Math.max(right.rob,right.noRob);
        //返回当前层的信息
        return current;
    }
}

```

### 0x3 课后总结