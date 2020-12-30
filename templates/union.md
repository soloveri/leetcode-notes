---
title: union
mathjax: true
data: 2020-03-23 23:25:27
updated:
tags:
- 并查集
categories:
- algorithm
---
并查集是为了快速合并集合、查询两个点是否属于同一个集合而生的。这里既不采用既不采用链表实现，也不采用hashmap实现。采用链表虽然合并集合很快，但是查询是否属于用一个集合需要遍历链表。采用hashmap虽然查询很快，但是合并又很慢。

并查集需要为用户提供两个方法：

- 判断两个点是否属于同一个集合
- 合并两个点所在的集合

这里采用三个hashmap实现`int`类型的并查集结构。后续可以更改为模板。
首先需要对点进行一个wrap，为啥？没懂。

``` c++ "wrap struct"
struct Node{
    int value;
    Node()=default;
    Node(int v):value(v){}
    bool operator==(const Node& element) {
        return this->value == element.value;
    }
    bool operator!=(const Node& element) {
        return this->value != element.value;
    }
};
struct HashNode {
    std::size_t operator()(const Node& nodes) const
    {
        using std::size_t;
        using std::hash;

        return (hash<int>()(nodes.value));
    }
};

struct EqualNode
{
    bool operator () (const Node& lhs, const Node& rhs) const
    {
        return lhs.value == rhs.value;
    }
};
```

首先每个集合有个代表节点，集合中模拟了图的结构
三个hashmap分别是：

- `unordered_map<int,Node,HashNode,EqualNode> element_map`
    - key：具体的点值
    - value：点对应的wrapper
- `unordered_map<Node,Node,HashNode,EqualNode> father_map`
    - key：`Node`
    - value:`Node`对应的父节点(**不是集合的代表节点哦**)
- `unordered_map<Node,int> size_map`
    - key:一个集合的代表节点
    - value：该集合对应的大小(点的个数)

使用并查集时首先进行初始化,**用户需要一次给出所有点**，不支持后续添加节点。

``` c++
struct UnionFindSets{
    unordered_map<int,Node,HashNode,EqualNode> element_map;
    unordered_map<Node,Node,HashNode,EqualNode> father_map;
    unordered_map<Node,int> size_map;
    //初始化
    UnionFindSets(const list<int>& nums){
        for(auto elem:nums){
            element_top[elem]=*new Node(elem);
            father_map[element_map[elem]]=element_map[elem];
            size_map[element_map[elem]]=1;
        }
    }
    bool IsSameSet(int lhs,int rhs){
        //初始化加入过点才有可能在一个集合中
        if(element_map.find(lhs)==element_top.end()||
        element_map.find(rhs)==element_map.end()){
            return false;
        }
        Node lhs_head=FindHead(element_map[lhs]);
        Node rhs_head=FindHead(element_map[rhs]);
        //代表元素相同就表示两个点在用一个集合里了
        return lhs_head==rhs_head;
    }
    void Union(int lhs,int rhs){
        if(element_map[lhs]==element_map.end()||
        element_map[rhs]==element_map.end()){
            return;
        }
        Node lhs_head=FindHead(lhs);
        Node rhs_head=FIndHead(rhs);
        //不是同一集合才合并啊
        if(lhs_head!=rhs_head){
            Node longer=size_map[lhs_head]>size_map[rhs_head]?lhs_head:rhs_head;
            Node lesser= longer== lhs_head?rhs_head:lhs_head;
            //将短的挂在长的后面
            father_map[lesser]=longer;
            size_map[longer]=size_map[longer]+size_map[lesser];
            //因为lesser不再是代表节点，所以需要将其从size_map中删除
            size_map.erase(lesser);
        }
    }
    Node FindHead(Node current){
        //对一个集合做扁平化处理，防止一个集合元素过多，即将该集合的元素在fahter_map对应的father直接修改为对应的代表元素
        queue<Node> paths;
        Node temp;
        //当节点不能在往上走时，即父节点不发生变化时的节点就是代表节点
        while(current!=father_map[current]){
            paths.push(current);
            current=father_map[current];
        }
        //对集合的节点做扁平化处理
        while(!paths.empty()){
            temp=paths.front();
            paths.pop();
            fahter_map[temp]=current;
        }
        return current;
    }
};
```

原理与实现都非常简单。