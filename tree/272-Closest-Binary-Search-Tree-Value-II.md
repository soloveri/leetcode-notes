---
title: 272. 最接近的二叉搜索树值 II
mathjax: true
data: 2020-07-24 19:57:05
updated:
difficulty: hard
tags:
- 递归
- BST
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/closest-binary-search-tree-value-ii/)

---

## 0x0 题目详情

>给定一个不为空的二叉搜索树和一个目标值 target，请在该二叉搜索树中找到最接近目标值 target 的 k 个值。

>注意：
给定的目标值 target 是一个浮点数
你可以默认 k 值永远是有效的，即 k ≤ 总结点数
题目保证该二叉搜索树中只会存在一种 k 个值集合最接近目标值

---

**测试用例:**

>示例：
输入: root = [4,2,5,1,3]，目标值 = 3.714286，且 k = 2
输出: [4,3]

        4
       / \
      2   5
     / \
    1   3

>拓展：
假设该二叉搜索树是平衡的，请问您是否能在小于 O(n)（n 为总结点数）的时间复杂度内解决该问题呢？

## 0x1 解题思路

这道题有三种思路:

- 第一种维护一个大小为k的大根堆,遍历所有节点,如果更接近的节点,那么就加入大根堆。遍历结束,堆中保留的k个节点就是结果。时间复杂度为O(nlogN),不太行。完全没有利用到BST的特点
&nbsp;
- 第二种：因为是BST,所以理所当然的想到了中序遍历。我们从最小节点开始进行中序遍历。每遇到一个节点cur。我们使用`LinkedList`保存结果。如果结果集result数量等于k。那么就说明截至都目前已经找到了k个节点。**如果当前节点cur比结果集中的头部节点head更接近target**。那么我们就弹出头部节点head,在尾部添加节点cur。
  &nbsp;
  这里需要注意,结果集result中的节点如果从头到尾进行遍历,与target的差值并不一定是降序的。每个节点与target的差值一般的情况就是从大到小,再从小到大的。中间的最小值就是与target最接近的点。这很容易理解,因为在最接近的节点min左右各有k/2个节点与target的差值逐渐加大,构成了结果集。
&nbsp;
  最后说说,结果集中的节点与target的差值虽然不是降序的,但还是能成功找到答案。因为如果结果集已达到k的话,表示我们已经找到了k的候选者,我们需要往后面看看,是否有更优的答案。但是我们永远也不会用将中间的最小节点min弹出。能进到result中的,都是比原来候选者更完美的。**所以如果结果集中的头部节点head比当前节点cur更接近target**,就表示我们已经找到了最优解,不用往后找了,直接返回即可。
&nbsp;
  **否则,result没有满的情况下,继续添加候选者。**
&nbsp;
- 第三种就是最优的解法,但是非常复杂。核心思想就是我们首先找到最接近target的节点min,然后从min节点开始往左右两个方向扩张,直到找到了k个节点。这是符合进阶要求的。

水平有限,只实现了第二种方法。复杂度最坏为O(N)。

## 0x2 代码实现

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
    LinkedList<Integer> result;
    // int diff=Integer,MAX_VALUE;
    public List<Integer> closestKValues(TreeNode root, double target, int k) {
        result=new LinkedList<>();
        if(root==null){
            return result;
        }
        recur(root,target,k);
        return result;

    }
    private void recur(TreeNode root,double target,int k){
        if(root==null){
            return;
        }
        recur(root.left,target,k);
        if(result.isEmpty()){
            result.add(root.val);
        }else if(result.size()==k){
            //提前终止递归,进行剪枝操作
            if(Math.abs(result.peekFirst()-target)<=Math.abs(root.val-target)){
                return;
            }else{
                result.offerLast(root.val);
                result.pollFirst();
            }
        }else{
            result.offerLast(root.val);
        }
        recur(root.right,target,k);

    }
}
```


## 0x3 课后总结

这道题比第271题难度高一点,还是那句话:

> 如果题目中给出了特点鲜明的数据结构,那么我们就往往需要利用这种特点来解题。