---
title: 23. 合并K个排序链表
mathjax: true
data: 2020-06-21 18:47:59
updated:
tags:
- [链表，合并，堆]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

### 0x0 题目详情

>合并 k 个排序链表，返回合并后的排序链表。请分析和描述算法的复杂度。

---

**测试用例:**

>示例:
输入:
[
  1->4->5,
  1->3->4,
  2->6
]
输出: 1->1->2->3->4->4->5->6

### 0x1 解题思路

**思路1:**
一看这道题的关键字，**有序**、**合并**,是不是有点归并排序的味道？对咯。这道题就可以使用归并排序的思路。先合并两个链表，然后合并四个链表，依次类推，思路很简单，就是写代码的时候要注意细节。

**思路2:**

这道题难就难在是k个链表，如果是两个链表，那非常简单。这道题用if-else就没法写了。所以我们需要额外使用小根堆来辅助我们进行自动排序。，每次从小根堆取出堆顶元素，然后再加入下一个元素，直到堆为空，这样k个链表的合并就完成了。

### 0x2 代码实现

代码引用自[sweetiee](https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/4-chong-fang-fa-xiang-jie-bi-xu-miao-dong-by-sweet/)

``` java "思路1"
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) {
            return null;
        }
        return merge(lists, 0, lists.length - 1);
    }

    private ListNode merge(ListNode[] lists, int lo, int hi) {
        if (lo == hi) {
            return lists[lo];
        }
        int mid = lo + (hi - lo) / 2;
        ListNode l1 = merge(lists, lo, mid);
        ListNode l2 = merge(lists, mid + 1, hi);
        return merge2Lists(l1, l2);
    }
}

```

看到了把，和归并排序非常相似，只不过在分割时分割到不能再分时，会返回一个节点引用。时间复杂度分析：K条链表的总结点数是 N，平均每条链表有 N/K个节点，因此合并两条链表的时间复杂度是 O(N/K)。从 K条链表开始两两合并成 1 条链表，因此每条链表都会被合并 logK 次，因此 KK 条链表会被合并 K * logK次，因此总共的时间复杂度是 K*logK*N/K即O（NlogK）。

---

``` java "思路2"
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if(lists==null || lists.length==0){
            return null;
        }
        PriorityQueue<ListNode> nodes=new PriorityQueue<>(lists.length,new Comparator<ListNode>(){
            public int compare(ListNode lhs,ListNode rhs){
                return lhs.val-rhs.val;
            }
        });

        ListNode dummy=new ListNode(-1);
        ListNode ptr=dummy;
        for(int i=0;i<lists.length;i++){
            if(lists[i]!=null){
                nodes.offer(lists[i]);
            }
        }
        while(!nodes.isEmpty()){
            ListNode current=nodes.poll();
            ptr.next=current;
            ptr=ptr.next;
            if(current.next!=null){
                nodes.offer(current.next);
            }
        }
        return dummy.next;

    }
}
```

小根堆的排序复杂度为`Klog(K)`,平均每个在堆内的元素花费的复杂度为`log(K)`,总共有N个元素，所以排序的复杂度为`Nlogk`。

### 0x3 课后总结

用小根堆没啥好说，主要这道题的有序、合并，这跟归并排序的思路太相似了，以后看到这两个词，就可以往归并上面靠。
