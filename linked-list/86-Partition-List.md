---
title: 86. 分隔链表
mathjax: true
data: 
updated:
tags:
- [链表]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/partition-list/)

### 0x0 题目详情

>给定一个链表和一个特定值 x，对链表进行分隔，使得所有小于 x 的节点都在大于或等于 x 的节点之前。
你应当保留两个分区中每个节点的初始相对位置。

---

**测试用例:**

>示例:
输入: head = 1->4->3->2->5->2, x = 3
输出: 1->2->2->4->3->5

### 0x1 解题思路

简单题简单题，就是把符合条件的节点挑出来，然后再串到原来链表的末尾就可了。

### 0x2 解题思路

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
    public ListNode partition(ListNode head, int x) {
        if(head==null || head.next==null){
            return head;
        }
        ListNode dummy=new ListNode(-1);
        ListNode pre=dummy;

        ListNode ptr=head;
        ListNode next;
        ListNode greater=new ListNode(-1);
        ListNode preGreater=greater;
        while(ptr!=null){
            next=ptr.next;
            if(ptr.val<x){
                ptr.next=pre.next;
                pre.next=ptr;
                pre=pre.next;
            }else{
                ptr.next=preGreater.next;
                preGreater.next=ptr;
                preGreater=preGreater.next;
            }
            ptr=next;
        }
        pre.next=greater.next;
        return dummy.next;
    }
}

```

### 0x3 课后总结

很简单！
