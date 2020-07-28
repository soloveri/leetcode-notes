---
title: 428. 序列化和反序列化 N 叉树
mathjax: true
data: 2020-07-28 19:42:59
updated:
difficulty: hard
tags:
- 序列化
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/serialize-and-deserialize-n-ary-tree/)

---

### 0x0 题目详情

序列化是指将一个数据结构转化为位序列的过程，因此可以将其存储在文件中或内存缓冲区中，以便稍后在相同或不同的计算机环境中恢复结构。

设计一个序列化和反序列化 N 叉树的算法。一个 N 叉树是指每个节点都有不超过 N 个孩子节点的有根树。序列化 / 反序列化算法的算法实现没有限制。你只需要保证 N 叉树可以被序列化为一个字符串并且该字符串可以被反序列化成原树结构即可。

### 0x1 解题思路

序列化二叉树比较容易,可以先按照先序遍历,遍历完N个节点,如果不为空,则将节点值加入字符串中,否则使用一个不会重复的符号代替null值。并且每个节点之间使用特殊符号`_`分隔。

注意反序列化就有点技巧了,我们把每个节点加入到一个队列中,每构造一个节点,那么就从队列中弹出一个值。这样不用操心当前反序列化到哪个节点了。

序列化与反序列化N叉树大体思路差不多,不过我们需要记录当前节点的字节点个数,因为我们不能再通过枚举恢复子节点,比如二叉树就只有左子树与右子树,可以手写出来。


### 0x2 实现代码

``` java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Codec {
    
    // Encodes a tree to a single string.
    public String serialize(Node root) {
        if(root==null){
            return "";
        }
        StringBuilder sb=new StringBuilder();
        recur(root,sb);
        return sb.toString();
    }

    private void recur(Node root,StringBuilder sb){
        sb.append(root.val).append("_");
        //序列化当前节点的子节点个数
        sb.append(root.children.size()).append("_");
        for(int i=0;i<root.children.size();i++){
            recur(root.children.get(i),sb);
        }
    }
	
    // Decodes your encoded data to tree.
    public Node deserialize(String data) {
        if(data==null || data.length()==0){
            return null;
        }
        String[] source=data.split("_");
        LinkedList<String> queue=new LinkedList<>();
        //将每个节点值放入队列中
        for(int i=0;i<source.length;i++){
            queue.offerLast(source[i]);
        }
        return rebuild(queue);
    }
    private Node rebuild(LinkedList<String> queue){
        String value=queue.pollFirst();
        //获取当前节点的子节点个数
        int size=Integer.valueOf(queue.pollFirst());
        Node root=new Node(Integer.valueOf(value),new ArrayList<Node>(size));
        for(int i=0;i<size;i++){
            root.children.add(rebuild(queue));
        }
        return root;
    }
}

```
### 0x3 课后总结

序列化没啥好说的,就是按照普通的后序、前序、中序、或者层次都行。

但是反序列化将每个节点的值放入队列中每恢复一个节点就弹出一个节点的方法比较巧妙。