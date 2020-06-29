---
title: 92. 反转链表 II
mathjax: true
data: 2020-06-29 09:47:43
updated:
tags:
- [链表]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

### 0x0 题目详情

>反转从位置 m 到 n 的链表。请使用一趟扫描完成反转。
说明:
1 ≤ m ≤ n ≤ 链表长度。

---

**测试用例:**

>示例:
输入: 1->2->3->4->5->NULL, m = 2, n = 4
输出: 1->4->3->2->5->NULL

### 0x1 解题思路

这道题也是非常简单，定位到位置在m的前一个节点和位置n的后一个节点，然后把中间的部分反转即可。

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
    public ListNode reverseBetween(ListNode head, int m, int n) {
        if(head==null || head.next==null){
            return head;
        }
        ListNode dummy=new ListNode(-1);
        ListNode pre=null;
        ListNode after=null;
        int count=0;
        dummy.next=head;
        ListNode ptr=dummy;
        while(ptr!=null){

            count++;
            if(count==m){
                pre=ptr;
            }else if(count==n+1){
                after=ptr.next;
                ptr.next=null;
                break;
            }
            ptr=ptr.next;
        }
        ListNode oldHead=pre.next;
        pre.next=reverse(oldHead);
        oldHead.next=after;
        return dummy.next;
    }
    ListNode reverse(ListNode head){
        ListNode pre=null;
        ListNode next;
        while(head!=null){
            next=head.next;
            head.next=pre;
            pre=head;
            head=next;
        }
        return pre;
    }
}
```

### 0x3 课后总结

简单的反转链表，没啥值得注意的。
