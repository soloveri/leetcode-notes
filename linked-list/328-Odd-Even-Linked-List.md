---
title: 328. 奇偶链表
mathjax: true
data: 2020-06-28 16:53:57
updated:
tags:
- [链表]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/odd-even-linked-list/)

### 0x0 题目详情

>给定一个单链表，把所有的奇数节点和偶数节点分别排在一起。请注意，这里的奇数节点和偶数节点指的是节点编号的奇偶性，而不是节点的值的奇偶性。
请尝试使用原地算法完成。你的算法的空间复杂度应为 O(1)，时间复杂度应为 O(nodes)，nodes 为节点总数。

---

**测试用例:**

>示例 1:
输入: 1->2->3->4->5->NULL
输出: 1->3->5->2->4->NULL

>示例 2:
输入: 2->1->3->5->6->4->7->NULL
输出: 2->3->6->7->1->5->4->NULL

>说明:
应当保持奇数节点和偶数节点的相对顺序。
链表的第一个节点视为奇数节点，第二个节点视为偶数节点，以此类推。

### 0x1 解题思路

这道题也是非常简单的哈，就是把奇数位置和偶数位置的节点分离出来，分别挂在奇数节点链表上和偶数节点链表上，然后再把偶数链表挂到奇数链表之后。

### 0x2 代码实现

时间复杂度为O(N)，需要把整个链表遍历完成，空间复杂度为O(1)。

``` java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode oddEvenList(ListNode head) {
        if(head==null || head.next==null){
            return head;
        }
        ListNode leftHead=new ListNode(-1);
        ListNode rightHead=new ListNode(-1);
        ListNode leftPre=leftHead;
        ListNode rightPre=rightHead;
        ListNode next=null;
        int count=1;
        while(head!=null){
            next=head.next;
            head.next=null;

            if(count%2!=0){
                leftPre.next=head;
                leftPre=leftPre.next;

            }else{
                rightPre.next=head;
                rightPre=rightPre.next;

            }
            count++;
            head=next;
        }
        leftPre.next=rightHead.next;
        return leftHead.next;

    }
}
```

### 0x3 课后总结

这题很简单，没啥好说的。
