---
title: 148. 排序链表
mathjax: true
data: 2020-06-28 17:17:13
updated:
tags:
- [链表]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/sort-list/)

### 0x0 题目详情

在 O(nlogn) 时间复杂度和常数级空间复杂度下，对链表进行排序。

---

**测试用例:**

>示例 1:
输入: 4->2->1->3
输出: 1->2->3->4

### 0x1 解题思路

很简单，就是归并排序就完事了。

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
    public ListNode sortList(ListNode head) {
        if(head==null || head.next==null){
            return head;
        }
        return recur(head);


    }
    private ListNode recur(ListNode head){
        if(head.next==null){
            return head;
        }
        ListNode mid=null;
        ListNode fast=head;
        ListNode slow=head;
        while(fast.next!=null && fast.next.next!=null){
            fast=fast.next.next;
            slow=slow.next;
        }
        mid=slow.next;
        slow.next=null;
        ListNode leftHead=recur(head);
        ListNode rightHead=recur(mid);
        return merge(leftHead,rightHead);
    }

    private ListNode merge(ListNode lhs,ListNode rhs){
        ListNode dummy=new ListNode(-1);
        ListNode ptr=dummy;
        ListNode next=null;
        while(lhs!=null && rhs!=null){
            if(lhs.val<=rhs.val){
                next=lhs.next;
                lhs.next=ptr.next;
                ptr.next=lhs;
                ptr=ptr.next;
                lhs=next;
            }else{
                next=rhs.next;
                rhs.next=ptr.next;
                ptr.next=rhs;
                ptr=ptr.next;
                rhs=next;
            }
        }
        while(lhs!=null){
            next=lhs.next;
            lhs.next=ptr.next;
            ptr.next=lhs;
            ptr=ptr.next;
            lhs=next;
        }
        while(rhs!=null){
            next=rhs.next;
            rhs.next=ptr.next;
            ptr.next=rhs;
            ptr=ptr.next;
            rhs=next;
        }
        return dummy.next;
    }
}
```

### 0x3 课后总结

链表的归并排序!