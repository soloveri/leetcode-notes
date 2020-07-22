---
title: 255. 验证前序遍历序列二叉搜索树
mathjax: true
data: 2020-07-22 18:11:07
updated:
tags:
- 二叉树
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/verify-preorder-sequence-in-binary-search-tree/)

---

## 0x0 题目详情

>给定一个整数数组，你需要验证它是否是一个二叉搜索树正确的先序遍历序列。
你可以假定该序列中的数都是不相同的。
参考以下这颗二叉搜索树：

         5
        / \
       2   6
      / \
     1   3
---

**测试用例:**

>示例 1：
输入: [5,2,6,1,3]
输出: false

>示例 2：
输入: [5,2,1,3,6]
输出: true

>进阶挑战：
您能否使用恒定的空间复杂度来完成此题？

## 0x1 解题思路

验证搜做二叉树:

1. 如果是中序遍历,验证搜索二叉树,那么只需要验证遍历结果是否为单调升序就行了。

2. 而对于先序遍历,如果想要验证BST的话,常规做法是需要维持一个单调递减栈用来存储根节点的,不然没办法准确地获取每颗子树地根节点。极限做法是不开辟额外占空间,将原始的遍历结果作为栈,这样就是闲了常数空间的做法。

3. 那么对于后序遍历,相应的,就是维持一个单调递增栈用来保存每颗子树的根节点。

以本题为例,维持了单调递减栈后,如果当前元素比栈顶元素大,说明当前节点为右子树节点,我们就需要找到当前子树的根节点。显而易见,在弹出的过程中,最后一个弹出的元素就是当前子树的**根节点**。

在获得了根节点,我们就观察是否有元素小于根节点,如果有,说明当前的遍历结果不是BST的正确遍历。因为能够与根节点相比的元素都是其对应的子树的右节点。

思考了以下,上述规则为什么能正确BST,是因为先序的BST就能通过这种方法判断出来,所以这种方法与BST的先序遍历是完全对等的。


## 0x2 代码实现

``` java
class Solution {
    public boolean verifyPreorder(int[] preorder) {
        if(preorder==null || preorder.length<1){
            return true;
        }
        int root=Integer.MIN_VALUE;//根节点，初始化为最小值,因为左子树必然是小于根节点的,还未找到第一个需要判断的右节点
        int esp=1;//栈帧,将原数组改造为一个栈
        for(int i=1;i<preorder.length;i++){
            if(preorder[i]<=root){
                return false;
            }
            //入栈操作
            if(preorder[i]<preorder[esp-1]){
                preorder[esp]=preorder[i];
                esp++;
            }
            else{
                //维持单调递减栈，做出栈操作
                while(preorder[i]>preorder[esp-1]){
                    esp--;
                    root=preorder[esp];
                    //栈为空
                    if(esp==0){
                        break;
                    }
                }
                //新节点入栈
                preorder[esp]=preorder[i];
                esp++;
            }
        }
        return true;
    }
}

```

## 0x3 课后总结

这种将原始遍历结果改造为栈的方法实在是 妙啊！

所以这是不是能说,树的遍历要么递归,要么就要使用辅助栈呢?