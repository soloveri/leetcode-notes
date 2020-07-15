---
title: 113. 路径总和 II
difficulty: medium
mathjax: true
data: 2020-07-15 19:23:27
updated:
tags:
- [递归,二叉树]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/path-sum-ii/)

### 0x0 题目详情
> 给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于给定目标和的路径。
说明: 叶子节点是指没有子节点的节点。

---

**测试用例:**

>示例:
给定如下二叉树，以及目标和 sum = 22，

              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1

> 返回:
[
   [5,4,11,2],
   [5,8,4,5]
]

### 0x1 解题思路

这道题就是[第122题 路径总和](https://leetcode-cn.com/problems/path-sum/)的升级版，只要第122题会做，这道题稍微改改就出来了。

这道题采用的是从上到下收集节点的方式。我们在逐节点向下遍历的过程中，不断使用sum减去node的val,如果当前的node为跟节点并且sum与val相减后为0，那么我们则把从上到下一条路径的节点和加入结果集中，然后返回。

还是比较简单的奥。

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
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        List<List<Integer>> result=new ArrayList<>();
        if(root==null){
            return result;
        }
        recur(root,sum,new ArrayList<Integer>(),result);
        return result;
    }
    private void recur(TreeNode node,int sum,List<Integer> current,List<List<Integer>> result){

        //我们当前处理的是根节点
        if(node.left==null && node.right==null){
            if(sum== node.val){
                current.add(node.val);
                //将当前路径加入结果集中
                result.add(new ArrayList<Integer>(current));
                current.remove(current.size()-1);
            }
            return;
        }
        if(node.left!=null && node.right!=null){
            //在进入下一层递归前，我们需要把当前节点的值加入路径中
            //然后再递归结束时回到本层后，再把刚才加入的点删除
            current.add(node.val);
            recur(node.left,sum-node.val,current,result);
            recur(node.right,sum-node.val,current,result);
            current.remove(current.size()-1);
            return;
        }
        if(node.left!=null){
            current.add(node.val);
            recur(node.left,sum-node.val,current,result);
            current.remove(current.size()-1);
        }
        else{
            current.add(node.val);
            recur(node.right,sum-node.val,current,result);
            current.remove(current.size()-1);
        }
        return;
    }
}
```

### 0x3 课后总结

还是比较简单的树的题目，由上至下收集答案。树的递归收集答案就两种情况，一种从上到下，一种从下到上，灵活应变把。 :)