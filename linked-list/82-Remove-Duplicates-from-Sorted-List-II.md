---
title: 82. 删除排序链表中的重复元素 II
mathjax: true
data: 2020-06-23 19:13:36
updated:
tags:
- [三指针,链表]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

---

### 0x0 题目详情

>给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。

---

**测试用例:**

>示例 1:
输入: 1->2->3->3->4->4->5
输出: 1->2->5

>示例 2:
输入: 1->1->1->2->3
输出: 2->3

### 0x1 解题思路

这道题跟[第83题](83-Remove-Duplicates-from-Sorted-List.md)的区别就是当前元素一旦有重复的，我们就需要删掉所有重复，比保留一个要难一点。但是说难也没有多难。就是细节繁琐。

这里我维护了三指针，第一个指针`pre`指向可能要删除的节点的前向节点，因为是单链表，要删除一个元素，就要获得它的前向节点。第二个指针`cur`指向重复元素序列的第一个位置，第三个指针`ptr`用来遍历链表。

这里还有一个小细节需要注意：如果在末尾有大量重复元素，上面这种方法是删不掉的，因为不管最后的元素是否重复，ptr为null时都会结束循环。

所以我们在循环外需要额外判断一次，`cur`的下一个节点是否为null，因为如果末尾出现重复，cur的下一个节点一定不为null。

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
        if(head==null || head.next==null){
            return head;
        }
        ListNode dummy=new ListNode(-1);

        dummy.next=head;

        ListNode pre=dummy;
        ListNode cur=head;
        ListNode ptr=head.next;
        //采用count计数，如果count>1，表示有重复元素
        int count=1;
        while(ptr!=null){
            if(cur.val!=ptr.val){
                if(count==1){
                    pre=cur;
                    cur=ptr;
                }else{
                    pre.next=ptr;
                    cur=ptr;
                    count=1;
                }
            }else{
                count++;

            }
            ptr=ptr.next;
        }
        //额外判断末尾是否出现重复元素
        if(cur.next!=null){
            pre.next=null;
        }
        return dummy.next;
    }
}

```

### 0x3 课后总结

链表题的细节就是烦人啊。