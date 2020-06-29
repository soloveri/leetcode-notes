---
title: 138. 复制带随机指针的链表
mathjax: true
data: 2020-06-29 10:15:55
updated:
tags:
- [链表]
categories:
- algorithm
---
[原题链接](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

### 0x0 题目详情

>给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。
要求返回这个链表的 深拷贝。 
我们用一个由 n 个节点组成的链表来表示输入/输出中的链表。每个节点用一个 [val, random_index] 表示：
val：一个表示 Node.val 的整数。
random_index：随机指针指向的节点索引（范围从 0 到 n-1）；如果不指向任何节点，则为  null 。

---

**测试用例:**

![复制随机节点](images/copy-random-list.png)

>输入：head = [\[7,null],[13,0],[11,4],[10,2],[1,0]]
输出：[\[7,null],[13,0],[11,4],[10,2],[1,0]]

### 0x1 解题思路

这道题一共三个步骤：

- 原地复制链表
- 设置随机指针
- 分裂链表

写起来也没啥好注意的。

### 0x2 解题思路

``` java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/

class Solution {
    public Node copyRandomList(Node head) {
        if(head==null){
            return head;
        }
        Node ptr=head;
        Node next=null;
        Node copy=null;
        while(ptr!=null){
            copy=new Node(ptr.val);
            next=ptr.next;
            ptr.next=copy;
            copy.next=next;
            ptr=next;
        }
        //设置随机节点
        ptr=head;
        while(ptr!=null){
            copy=ptr.next;
            if(ptr.random!=null){
                copy.random=ptr.random.next;
            }
            ptr=copy.next;

        }
        //分离节点
        //Node dummy=new Node(-1);
        //dummy.next=head;
        //Node preOrigin=dummy;
        Node copyDummy=new Node(-1);
        Node preCopy=copyDummy;

        ptr=head;
        copy=null;
        next=null;
        while(ptr!=null){
            copy=ptr.next;
            next=copy.next;
            ptr.next=next;
            ptr=next;

            copy.next=preCopy.next;
            preCopy.next=copy;
            preCopy=preCopy.next;
        }
        return copyDummy.next;
    }
}

```

### 0x3 课后总结

这道题看着麻烦，写起来超级简单，采用原地复制，然后分裂即可。链表问题确实不难。
