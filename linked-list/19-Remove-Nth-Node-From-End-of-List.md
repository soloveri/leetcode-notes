---
title: 19. 删除链表的倒数第N个节点
mathjax: true
data: 2020-06-21 19:09:19
updated:
tags:
- [双指针,链表]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

### 0x0 题目详情

>给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

---

**测试用例:**

>示例：
给定一个链表: 1->2->3->4->5, 和 n = 2.
当删除了倒数第二个节点后，链表变为 1->2->3->5.
说明：
给定的 n 保证是有效的。

### 0x1 解题思路

这道题没什么难的，就是一个双指针的解法，首先第一个指针需要先走n步，然后第二个指针从头开始走，直到第一个指针的没有下一个元素。主要这里要删除倒数第N个节点，那么第二根指针需要指向倒数N+1个节点，因为是单向链表，没法获取倒数第N节点的前向节点。

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
    public ListNode removeNthFromEnd(ListNode head, int n) {
        if(head==null){
            return null;
        }
        //这里设置了头节点，方便指针移动
        ListNode dummy=new ListNode(-1);
        dummy.next=head;
        ListNode current=dummy;
        ListNode pre=dummy;
        int step=n;
        while(step>0){
            current=current.next;
            step--;
        }
        while(current.next!=null){
            pre=pre.next;
            current=current.next;
        }
        pre.next=pre.next.next;
        return dummy.next;

    }
}
```

### 0x3 课后总结

链表的题目里双指针的使用还是比较频繁的，可以记为一个常用的解题点。