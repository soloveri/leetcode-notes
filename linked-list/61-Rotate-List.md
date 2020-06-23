---
title: 61. 旋转链表
mathjax: true
data: 2020-06-23 19:11:51
updated:
tags:
- [双指针,链表]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/rotate-list/)

---

### 0x0 题目详情

>给定一个链表，旋转链表，将链表每个节点向右移动 k 个位置，其中 k 是非负数。

---

**测试用例:**

>示例 1:
输入: 1->2->3->4->5->NULL, k = 2
输出: 4->5->1->2->3->NULL
解释:
向右旋转 1 步: 5->1->2->3->4->NULL
向右旋转 2 步: 4->5->1->2->3->NULL

### 0x1 解题思路

这道题比数组翻转简单多了，首先计算一下到底需要翻转几步，然后找到翻转点就行了。


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
    public ListNode rotateRight(ListNode head, int k) {
        if(head==null || k==0){
            return head;
        }
        int length=0;
        ListNode ptr=head;
        while(ptr!=null){
            length++;
            ptr=ptr.next;
        }
        //计算到底需要翻转几步
        k=k%length;
        if(k==0){
            return head;
        }
        ListNode oldHead=head;
        ListNode fast=head;
        ListNode slow=head;
        while(k>0){
            fast=fast.next;
            k--;
        }
        //找到翻转点
        while(fast.next!=null){
            fast=fast.next;
            slow=slow.next;
        }
        //得到翻转点newHead
        ListNode newHead=slow.next;
        slow.next=fast.next;
        fast.next=oldHead;
        return newHead;
    }
}

```

### 0x3 课后总结

简单简答，应该归类为简单题奥。