---
title: 83. 删除排序链表中的重复元素
mathjax: true
data: 2020-06-21 19:11:04
updated:
tags:
- [双指针,链表]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

### 0x0 题目详情

>给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

---

**测试用例:**

>示例 1:
输入: 1->1->2
输出: 1->2

>示例 2:
输入: 1->1->2->3->3
输出: 1->2->3
给定一个链表: 1->2->3->4->5, 和 n = 2.
当删除了倒数第二个节点后，链表变为 1->2->3->5.

### 0x1 解题思路

这道题也是使用双指针fast与slow，如果fast与slow指向的元素不同，那么就更新slow为fast。如果指向的元素相同，说明我们遇到了相同元素了，只需保留重读元素中的第一个。所以slow不动，直到二者指向的元素再次不相同，然后直接删除slow与fast之间的节点并更新slow。

这里有一个值得注意的点：就是如果末尾出现相同元素，上面的操作的是不会执行最后依次删除的，需要手动额外删除一次。比如
1->2->3->3->3，这里slow指向左边第一个3，fast指向null，循环已经结束。

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
    public ListNode deleteDuplicates(ListNode head) {
        if(head==null){
            return null;
        }
        ListNode pre=head;
        ListNode current=head.next;
        while(current!=null){
            if(pre.val!=current.val){
                pre.next=current;
                pre=current;
            }
            current=current.next;
        }
        //手动删除末尾的重读元素
        pre.next=null;
        return head;

    }
}
```

### 0x3 课后总结

看到了把，又是双指针的思路，双指针在数组和链表里都挺好用的啊。