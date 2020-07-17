---
title: 129. 求根到叶子节点数字之和
mathjax: true
data: 2020-07-17 19:14:28
difficulty: medium
updated:
tags:
- [递归,二叉树]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/)

---

### 0x0 题目详情

>给定一个二叉树，它的每个结点都存放一个 0-9 的数字，每条从根到叶子节点的路径都代表一个数字。
例如，从根到叶子节点路径 1->2->3 代表数字 123。
计算从根到叶子节点生成的所有数字之和。
说明: 叶子节点是指没有子节点的节点。

---

**测试用例:**
>示例 1:
输入: [4,9,0,5,1]

           4
         /  \
         9   0
       / \
      5   1

>输出: 1026
解释:
从根到叶子节点路径 4->9->5 代表数字 495.
从根到叶子节点路径 4->9->1 代表数字 491.
从根到叶子节点路径 4->0 代表数字 40.
因此，数字总和 = 495 + 491 + 40 = 1026.

### 0x1 解题思路

这道题我一开始使用是维持一个list,当当前处理的节点为叶子节点时,就对list中的节点进行结算。虽然做是能做,但是效率不是很客观。遂观察评论区,看看有什么奇淫技巧。嘿,真叫我找见了.

其实根本不用维持list,对于基本类型,递归时直接复制的值,当递归完成回到本层时,原来的和根本没有发生改变。不像引用,进行递归时可能直接改变对象的内容。对于这道题直接计算从上到当前节点的和,深度每加一层,和就乘10。因为每下一层，就相当于把和往左移动一位。不错不错。


### 0x2 代码实现

原始代码:

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
    private int result=0;
    public int sumNumbers(TreeNode root) {
        if(root==null){
            return 0;
        }
        // List<List<Integer>> result=new ArrayList<>();
        recur(root,new LinkedList<Integer>());
        return result;
    }

    private void recur(TreeNode node,LinkedList<Integer> current){
        if(node.left==null && node.right==null){
            //表示到达子节点,可以结算了
            
            current.addLast(node.val);
            result+=calc(current);
            current.pollLast();
            // result+=current;
            return;
        }
        if(node.left!=null){
            current.addLast(node.val);
            recur(node.left,current);
            current.pollLast();
        }
        if(node.right!=null){
            current.addLast(node.val);
            recur(node.right,current);
            current.pollLast();
        }
    }

    private int calc(LinkedList<Integer> current){
        LinkedList<Integer> temp=new LinkedList<>(current);
        int result=0;
        while(!temp.isEmpty()){
            result+=temp.peekFirst()*(int)Math.pow(10,temp.size()-1);
            temp.pollFirst();
        }
        return result;
    }
}

```

---

改进代码:

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
    private int result=0;
    public int sumNumbers(TreeNode root) {
        if(root==null){
            return 0;
        }
        // List<List<Integer>> result=new ArrayList<>();
        recur(0,root);
        return result;
    }

    private void recur(int current,TreeNode node){
        if(node.left==null && node.right==null){
            //表示到达子节点,可以结算了
            result+=current*10+node.val;
            // current.addLast(node.val);
            // result+=calc(current);
            // current.pollLast();
            // result+=current;
            return;
        }
        if(node.left!=null){
            // current.addLast(node.val);
            
            recur(current*10+node.val,node.left);
            // current.pollLast();
        }
        if(node.right!=null){
            // current.addLast(node.val);
            recur(current*10+node.val,node.right);
            // current.pollLast();
        }
    }

    private int calc(LinkedList<Integer> current){
        LinkedList<Integer> temp=new LinkedList<>(current);
        int result=0;
        while(!temp.isEmpty()){
            result+=temp.peekFirst()*(int)Math.pow(10,temp.size()-1);
            temp.pollFirst();
        }
        return result;
    }
}

```

### 0x3 课后总结

tips:如果跟递归相关的数据是**基本类型**时，**直接递归**就好了,不用考虑递归完成后会不会对本层的原始数据有什么影响,因为是值传递,简单复制。