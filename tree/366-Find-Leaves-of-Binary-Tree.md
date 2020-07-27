---
title: 366. 寻找二叉树的叶子节点
mathjax: true
data: 2020-07-27 18:04:52
updated:
difficulty: 
tags:
- 递归
- 二叉树
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/find-leaves-of-binary-tree/)

---

### 0x0 题目详情
>给你一棵二叉树，请按以下要求的顺序收集它的全部节点：
依次从左到右，每次收集并删除所有的叶子节点
重复如上过程直到整棵树为空
 
---

**测试用例:**
>示例:
输入: [1,2,3,4,5]
 
             1
            / \
           2   3
          / \     
         4   5    

>输出: \[[4,5,3],[2],[1]] 
解释:

1.删除叶子节点 [4,5,3] ，得到如下树结构：

      1
     / 
    2          
 

2.现在删去叶子节点 [2] ，得到如下树结构：

    1          
 

3.现在删去叶子节点 [1] ，得到空树：

    []         

### 0x1 解题思路

这道题的难点在于当我们遇到叶子节点时,我们需要知道它应该放到哪个集合中。所以当递归到叶子节点往上回溯时,我们附带向上层传递本层节点的层数。当前节点的层数取左右节点传回的较大的值。

然后因为结果集的存放是有规律的:第一层放到第一个集合,第二层放到第二个集合,...,以此类推,就将层数信息和对应的集合联系了起来(这里的层数由下往上,最底层为第一层)。

编写递归代码就比较简单了。

### 0x2 实现代码

遍历了n个节点,时间复杂度为O(N)。

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
    List<List<Integer>> result;
    public List<List<Integer>> findLeaves(TreeNode root) {
        result=new ArrayList<>();
        if(root==null){
            return result;
        }
        recur(root);
        return result;
        
    }
    private int recur(TreeNode root){
        if(root==null){
            return 0;
        }
        //返回左子树的高度
        int left=recur(root.left);
        //返回右子树的高度
        int right=recur(root.right);
        int current=Math.max(left,right)+1;
        //如果是新集合,那么就new一个
        if(current>result.size()){
            result.add(new ArrayList<Integer>());
            result.get(result.size()-1).add(root.val);
        }else{
            //否则通过层数找到对应的集合加入 当前节点
            result.get(current-1).add(root.val);
        }
        return current;
    }
}
```

### 0x3 课后总结

老生常谈！！！树的题目:

- 要么从根节点开始递归由上至下收集答案,直至遇到根节点
- 要么先递归到子节点,然后由下到上收集答案