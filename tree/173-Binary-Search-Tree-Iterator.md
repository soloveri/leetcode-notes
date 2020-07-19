---
title: 173. 二叉搜索树迭代器
mathjax: true
data: 2020-07-19 16:08:21
updated:
tags:
- BST
- 中序遍历
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/binary-search-tree-iterator/)

---

### 0x0 题目详情

>实现一个二叉搜索树迭代器。你将使用二叉搜索树的根节点初始化迭代器。
调用 next() 将返回二叉搜索树中的下一个最小的数。

---

**测试用例:**

![173](images/173-bst-tree.png)

>BSTIterator iterator = new BSTIterator(root);
iterator.next();    // 返回 3
iterator.next();    // 返回 7
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 9
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 15
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 20
iterator.hasNext(); // 返回 false

提示：
- next() 和 hasNext() 操作的时间复杂度是 O(1)，并使用 O(h) 内存，其中 h 是树的高度。
- 你可以假设 next() 调用总是有效的，也就是说，当调用 next() 时，BST 中至少存在一个下一个最小的数。


### 0x1 解题思路

BST一般都和中序遍历有关,我已经想到中序遍历了,但是要求是O(h)的空间复杂度难住了我。

看了官方答案,发现它通过均摊,达到了O(h)的空间复杂度。具体解释见代码注释。


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
class BSTIterator {
    LinkedList<TreeNode> stack;

    public BSTIterator(TreeNode root) {
        stack=new LinkedList<>();
        
        while(root!=null){
            stack.offerLast(root);
            root=root.left;
        }
    }
    
    /** @return the next smallest number */
    public int next() {
        // int result;
        TreeNode cur=stack.pollLast();
        int result=cur.val;
        cur=cur.right;
        /**
        我们首先通过非递归的中序遍历完成了题目的操作,非递归的中序遍历就不用我多说了吧。
        在遇到一个节点时,是需要把当前节点的所有左子节点压入到栈中的。在最坏情况下，空间复杂度达到O(N),当然此时树的高度也为N。

        压入所有子节点的操作是通过循环完成了，也就是说这个循环最多执行n次。

        那么我们最多执行n次next操作就能完成树的遍历，平均下来的时间复杂度也就达到了O(n)/n=O(1)的要求
        */
        while(cur!=null){
            stack.offerLast(cur);
            cur=cur.left;
        }
        
        return result;

    }
    
    /** @return whether we have a next smallest number */
    public boolean hasNext() {
        return !stack.isEmpty();
    }
}

/**
 * Your BSTIterator object will be instantiated and called as such:
 * BSTIterator obj = new BSTIterator(root);
 * int param_1 = obj.next();
 * boolean param_2 = obj.hasNext();
 */
```

### 0x3 课后总结

这个通过对时间复杂度的均摊来达到要求秀到我了。所以这里给我们提了个醒:

如果题目对于时间复杂度有要求,我们可以通过两个方面考虑:

- 第一种是实实在在的代码复杂度。
- 第二种通过均摊时间复杂度,看看能够达到要求。