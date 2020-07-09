---
title: 95. 不同的二叉搜索树 II
mathjax: true
data: 2020-07-09 13:28:26
updated:
tags:
- [递归,二叉搜索树]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)


### 0x0 题目详情

给定一个整数 n，生成所有由 1 ... n 为节点所组成的 二叉搜索树 。

---

**测试用例:**


>示例：
输入：3
输出：
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]
解释：
以上的输出对应以下 5 种不同结构的二叉搜索树：

### 0x1 解题思路

这道题呢，肯定要用递归做了，但是怎么个递归法？

为了生成所有的搜索二叉树，我们需要让每一个点都有成为根节点的机会，对于一个数i，其左子树的节点由`1\~i`范围的数字构成，右子树由`i+1\~n`范围的数字构成。并且左子树和右子树并没有对应关系。也就是说对于节点i来说，其任意一颗左子树和任意一颗右子树都能构成一颗二叉搜索树，因为节点中的值在最开始已经规定好了。

根据以上思路，写出递归就很简单了。

### 0x2 代码实现

``` java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<TreeNode> generateTrees(int n) {
        
        if(n<1){
            return new ArrayList<TreeNode>();
        }
        return recur(1,n);

    }
    private List<TreeNode> recur(int start,int end){
        //存放的是每颗二叉搜索树的根节点
        List<TreeNode> result=new ArrayList<>();
        if(start>end){
            result.add(null);
            return result;
        }
        //每一个数都可以作为一颗单独的子树
        if(start==end){
            result.add(new TreeNode(start));
            return result;
        }

        //start~end中的每个数都尝试作为根节点
        for(int i=start;i<=end;i++){
            List<TreeNode> left=recur(start,i-1);
            List<TreeNode> right=recur(i+1,end);
            //任意组合左子树与右子树
            for(TreeNode leftNode:left){
                for(TreeNode rightNode:right){
                    TreeNode root=new TreeNode(i);
                    root.left=leftNode;
                    root.right=rightNode;
                    result.add(root);
                }
            }
        }
        return result;
    }
}

```

### 0x3 课后总结

构建二叉搜索树，首先需要把根节点确定好，然后如果左子树或者右子树有多种组成结构时，左右子树之间是没有关系的，因为左子树都是小于根节点的，右子树都是大于根节点的，这样已经确定生成的树必然是二叉搜索树，所以左子树与右子树可以随意结合。