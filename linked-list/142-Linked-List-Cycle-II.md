---
title: 142. 环形链表 II
mathjax: true
data: 2020-06-28 17:10:25
updated:
tags:
- [链表]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

### 0x0 题目详情

>给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。
为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。
说明：不允许修改给定的链表。

---

**测试用例:**

>示例 1：
输入：head = [3,2,0,-4], pos = 1
输出：tail connects to node index 1
解释：链表中有一个环，其尾部连接到第二个节点。

>示例 2：
输入：head = [1], pos = -1
输出：no cycle
解释：链表中没有环。

### 0x1 解题思路

这题也是经典的快慢指针，具体步骤如下：

1. 快指针走两步，慢指针走一步，直到快慢指针相遇
2. 第一次相遇后，将快指针指向链表头节点，快指针走一步，慢指针走一步，直到二者再次相遇
3. 二者相遇时的节点就是入环节点。


### 0x2 代码实现

``` java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if(head==null || head.next==null || head.next.next==null){
            return null;
        }
        ListNode fast=head.next.next;
        ListNode slow=head.next;
        //第一次相遇
        while(fast!=slow){
            if(fast.next==null || fast.next.next==null){
                return null;
            }
            fast=fast.next.next;
            slow=slow.next;
        }
        // if(fast==null){
        //     return null;
        // }
        fast=head;
        //第二次相遇
        while(fast!=slow){
            fast=fast.next;
            slow=slow.next;
        }
        return slow;
    }
}
```

### 0x3 课后总结

快慢指针求入环节点的原理是啥呢，我暂时也不清楚，以后再研究吧。
