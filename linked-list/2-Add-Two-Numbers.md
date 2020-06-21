---
title: 2. 两数相加
mathjax: true
data: 2020-06-14 00:43:35
updated:
tags:
- [链表]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/add-two-numbers/)

### 0x0 题目详情

>给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。
如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。
您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

---

**测试用例:**

>示例：
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807

### 0x1 解题思路

这道题也是非常简单的，维持一个进位值，唯一值得注意的就是当循环结束后，如果此时的进位值为1，说明需要额外构造一个节点存储进位值，其他的没什么好说的。

### 0x2 代码实现

``` java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {

        int grate=0;
        ListNode result=new ListNode(-1);
        ListNode curNode=result;;
        while(l1!=null && l2!=null){
            int current=l1.val+l2.val+grate;
            grate=current/10;
            curNode.next=new ListNode(current%10);
            curNode=curNode.next;
            l1=l1.next;
            l2=l2.next;
        }
        while(l1!=null){
            int current=l1.val+grate;
            grate=current/10;
            curNode.next=new ListNode(current%10);
            curNode=curNode.next;
            l1=l1.next;
        }
        while(l2!=null){
            int current=l2.val+grate;
            grate=current/10;
            curNode.next=new ListNode(current%10);
            curNode=curNode.next;
            l2=l2.next;
        }
        //额外判断一下进位值，看看是否需要额外构造节点
        if(grate!=0){
            curNode.next=new ListNode(grate);
            curNode=curNode.next;
        }
        return result.next;

    }
}

```

### 0x3 课后总结

这题没啥好说的，链表的常规题。没有什么特别的技巧。