---
title: graph
mathjax: true
data: 2020-03-21 16:03:57
updated:
tags:
- graph
categories:
- algorithm
---

图的基本算法主要有四个：BFS、DFS、最短路径、最小生成树算法、拓扑排序算法。算法比较多，并且图的表达形式也非常多，所以我们可以维护一种常用的图结构来作为构成图的模板，将不熟悉的图结构转化为我们常用的图结构。

## 图结构模板

``` c++ "图的总体结构"
struct Graph{
    unordered_map<int,Node,HashNode,EqualNode> nodes;
    unordered_set<Edge,HashEdge,EqualEdge> edges;
};
```

图主要有点集`nodes`和边集`edges`构成，这里选用unordered结构的原因是因为hashmap比普通的map查询更快。`Node`是基本点的wrapper，这么做是为了方便算法编写。

``` c++ "node的基本结构"
struct Node{
    int value;//基本点的序号，即点1、点2...
    int in;//该点的入度
    int out;//该店的出度
    unordered_set<Node*,HashNodeptr,EqualNodeptr> nexts;//该set表示由该点出发可以到达的第一个邻居节点
    unordered_set<Edge*,HashEdgePtr,EqualEdgePtr> edges;//该set表示属于该点的边,即从该点出发的边
    Node (int v):value(v){
        in=0;
        out=0;
    }
}
```

``` c++ "Edge的基本结构"
struct Edge{
    int weight;//该边的权重
    Node* from;//该边的出发节点
    Node* to；//改边的目的节点
    Edge(int w,Node* f,Node* t):weight(w),from(f),to(t){}
}
```

因为结构体相互嵌套的原因，所以必须使用指针。

使用上面的图结构，只需要写一个转换接口就可以使用。假设采用这样一种图接口：一个二维数组：每一行表示一个节点：`[Node from,Node to,int weight]`,下面是一个栗子。

``` c++ "转化函数"
Graph* Transform(vector<vecotr<int>>& nums){
    Graph* graph=new Graph;
    for(auto elem:nums){
        int from=elem[0];
        int to=elem[1];
        int weight=elem[2];
        //没有构造过该节点，将该节点加入nodes
        //比如[1,2,0]和[2,3,0]，在处理第一个边时节点2已经构造好了，只需要更新节点相关信息即可
        if(graph->nodes.find(from)==graph->nodes.end()){
            graph->nodes.insert(pair<int,Node>(from,*new Node(from)));
        }
        if(graph->nodes.find(to)==graph->nodes.end()){
            graph->nodes.insert(pair<int,Node>(to,*new Node(to)));
        }
        //这里不能使用at,因为返回值copy，除非对应的value是指针，无所谓copy与否，使用find返回迭代器直接修改
        auto from_iter=graph->nodes.find(from);
        auto to_iter=graph->nodes.find(to);
        //更新入度出度
        from_iter->second.out++;
        to_iter->second.in++;
        //更新邻居节点
        from_iter->second.nexts.insert(&to_iter.second);
        Edge* edge=new Edge(weight,&from_iter.second,&to_iter.second);
        from_iter->second.edges.insert(edge);
        graph->edges.insert(*edge);
        //一个节点处理完毕
    }
    return graph;
}

```

使用时只需要稍微修改上述模板即可使用该结构。

## 图结构java模板

``` java "图结构"
public class Graph {
    HashMap<Integer, Node> nodes;
    HashSet<Edge> edges;
    Graph(){
        this.nodes=new HashMap<>();
        this.edges=new HashSet<>();
    }
    }


    class Node{
    public int value;
    public int in;
    public int out;
    public ArrayList<Node> nexts;
    public ArrayList<Edge> edges;

    public Node(int value) {
        // TODO Auto-generated constructor stub
        this.value=value;
        this.in=0;
        this.out=0;
        this.nexts=new ArrayList<>();
        this.edges=new ArrayList<>();
    }

    }

    class Edge{
    int weight;
    Node from;
    Node to;
    public Edge(int weight, Node from, Node to) {
        super();
        this.weight = weight;
        this.from = from;
        this.to = to;
    }
}
```

``` java "转换函数"
Graph transfoGraphTemp(int[][] data) {
    Graph graph=new Graph();
    for(int i=0;i<data.length;++i) {
        Integer from=data[i][1];
        Integer to=data[i][0];
        //查询是否加入过这两个点

        if(!graph.nodes.containsKey(from)) {
            graph.nodes.put(from, new Node(from));
        }
        if(!graph.nodes.containsKey(to)) {
            graph.nodes.put(to, new Node(to));
        }
        //更新点的入度和出度
        Node fromNode=graph.nodes.get(from);
        Node toNode=graph.nodes.get(to);
        Edge newEdge=new Edge(0, fromNode, toNode);

        //更新所属边和邻居节点

        fromNode.nexts.add(toNode);
        fromNode.out++;
        toNode.in++;

        fromNode.edges.add(newEdge);
        graph.edges.add(newEdge);
    }
    return graph;
}
```

## 图遍历

图遍历两种常用的方法是`DFS`和`BFS`,含义就不细说了，主要讲讲实现代码。

### BFS

顾名思义，按照广度遍历，这里需要使用一个队列结构，以及一个hashset结构(防止重复将邻居节点放入队列中)

``` c++

void BFS(Node* head){
    if(head =nullptr){
        return;
    }
    queue<Node*> que;
    unordered_set<Node*,HashNodePtr,EqualNodePtr> sets;
    //入队列和入set操作是配套的
    que.push(head);
    sets.insert(head);
    while(!que.empty()){
        Node* current=que.front();
        que.pop();
        //do operation
        cout<<"value: "<<current->value<<endl;
        //处理该节点的所有邻居节点
        for(auto elem:current->nexts){
            //如果该邻居节点没有注册过，那么进行注册并放入队列
            if(sets.find(elem)==sets.end()){
                que.push(elem);
                sets.insert(elem);
            }
        }
    }
    return;
}

```

``` c++
void DFS(Node* head){
    if(head==nullptr){
        return;
    }
    stack<Node*> stacks;
    unordered_set<Node*,HashNodePtr,EqualNodeptr> sets;
    stacks.push(head);
    sets.insert(head);
    cout<<"value: "<<head->value<<endl;
    while(!stacks.empty()){
        Node* current=stacks.top();
        stacks.pop();
        //处理current的邻居节点
        for(auto elem:current->nexts){
            //如果next中的一个节点没有注册过，那么就注册，并入栈
            //在入栈前必须先将current先入栈，原因如下：
            //如果current的next节点elem没能遍历完所有节点，
            //那么退栈时退到current会发现其的next节点没有全部注册
            //说明还没遍历完，需要使用下一个next继续遍历；
            //那么如果next全部注册，就直接退栈
            //所以需要在第一次入栈的时候打印；如果不是第一次，一个节点可能入栈出栈多次
            //比如current节点，需要亲自处理所有next节点，即current入栈，next1入栈，next1出栈，current出栈，
            //然后current的next2入栈，current入栈，到底哪次打印？
            if(sets.find(elem)==sets.end()){
                stacks.push(current);
                stacks.push(elem);
                sets.insert(elem);
                cout<<"value: "<<elem->value<<endl;
                break;
            }
        }
    }
    return;
}


```

## 拓扑排序

这个有啥用？有一个典型应用场景：编译项目时，子项目的依赖问题，需要决定先后顺序，这时候就能用到拓扑排序了。解决该问题时需要用到节点的入度与出度。

``` c++
list<Node*> SortTopo(cosnt Graph* graph){
    list<Node*> results;
    queue<Node*> zero_in_nodes;
    unordered_map<Node*,int HashNodePtr,EqualNodeptr> in_maps;
    //先找到所有入度为0的节点
    for(auto elem:graph->nodes){
        if(elem.second.in == 0){
            zero_in_nodes.push(&elem.second);
        }
    }
    while(!zero_in_nodes.empty()){
        Node* current=zero_in_nodes.front();
        zero_in_nodes.pop();
        results.emplace_back(current);
        //消除该节点对所有邻居节点的影响
        for(auto every_node:current->nexts){
            //这样写就破坏图结构了，不好，应使用hashmap维护节点的入读
            /*
            every_nodes->in--;
            if(every_nodes->in == 0){
                zero_in_nodes.push(every_nodes);
            }
            */
            in_map[every_node]=--every_nodes->in;
            if(in_map[every_node]== 0){
                zero_in_nodes.push(every_node);
            }

        }
    }
}
```

> 写代码时应考虑是否应该更改参数的相关数据，所以不希望更改时应加const，那么如何表示常量指针呢？
答案：const写在`*`**前面**就是**常量指针**，写在后面就是指针常量，比如`const Graph* graph`,`graph`是一个常量指针。


java版本：

``` java "拓扑排序java版"
static List<Node> TopoSort(GraphTemp graph,int numCourse) {
    HashMap<Node, Integer> inMap=new HashMap<>();
    Queue<Node> zeroInQueue=new LinkedList<>();

    for(Node elem:graph.nodes.values()) {
        inMap.put(elem, elem.in);
        if(elem.in==0) {
            zeroInQueue.add(elem);
        }
    }
    if(zeroInQueue.isEmpty()) {
        return false;
    }
    List<Node> results=new ArrayList<>();

    while(!zeroInQueue.isEmpty()) {
        Node cur=zeroInQueue.poll();
        results.add(cur);

        for(Node elem:cur.nexts) {
            inMap.put(elem, inMap.get(elem)-1);
            if(inMap.get(elem)==0) {
                zeroInQueue.add(elem);
            }
        }
    }
    return resullts;
}
```

## 最小生成树(无向图算法)

最小生成树有两种算法：

- p算法：在不构造环的前提下，依次选取权重最小的边，当所有边都选完了，最小生成树也构造好了。
- k算法

### p算法

就一个重点：**不构造环的前提下**，那么如何判断会不会构造环？这里提供一种判断方法：如果两个点在一个集合中时，就会构成环。所以问题转化成了如何判断两个点是否属于同一个集合。一般采用**并查集**的方法快速判断，这里先实现一种简单的结构。

``` c++
struct Mysets{
    //需要维护一个hashmap，key为node，value为对应的集合
    //这里如果直接存放set，不好判断两个点是否在同一个集合啊
    unordered_map<Node*,set<Node*,HashNodePtr,EqualNodeptr>*,HashNodePtr,EqualNodePtr> sets_map;
    //进行初始化
    Mysets(const unordered_map<int,Node,HashNode,EqualNode>& nodes){
        for(auto elem:nodes){
            sets_map.insert(pair<Node*,set<Node*,HashNodePtr,EqualNodePtr>>(&elem.second,new set{&elem}));
        }
    }
    //需要为用户提供两个方法
    //判断两个点是否属于同一个集合
    bool isSameSet(const Node* lhs,const Node* rhs){
        //判断两个node对应的set内存地址是否相同
        return sets_map[lhs]==sets_map[rhs];
    }
    //合并两个点至同一集合
    void Union(Node* lhs,Node* rhs){
        auto left_iter=sets_map.find(lhs);
        auto right_iter=sets_map.find(rhs);
        auto address=right_iter->second;
        for(auto elem:*right_iter->second){
            left_iter->second->insert(elem);
            //需要更新每个节点对应的集合
            Mysets[elem]=right_iter->second;
        }
        //而不是在最后只更新rhs对应的集合
        //sets_map[right_iter.first]=left_iter->second;
        delete address;
    }

};

list<Edge*> Kruskal(Graph* graph){
    list<Edge*> results;
    //首先需要对所有边构造一个小根堆
    auto cmp=[](const Node* lhs,const Node* rhs)->bool{return lhs->weight>rhs->weight};
    priority_queue<Node*,vector<Node*>,decltype(cmp)> pri_queue(cmp);
    //初始化节点与集合
    Mysets myset(graph->nodes);
    for(auto elem:graph->edges){
        pri_queue.push(&elem);
    }
    //由小到大处理每条边
    while(!pri_queue.empty()){
        Edge* current_edge=pri_queue.top();
        pri_queue.pop();
        Node* from=current_edge->from;
        Node* to=current_edge->to;
        //如果新选取的边首尾两点不在同一集合，那么就选它
        if(myset.isSameSet(from,to)){
            results.emplace_back(current_edge);
            myset.Union(from,to);
        }
    }
    return std::move(results);
}
```

### p算法

p算法就是首先随便选取一个点，然后选取该点权值最小的边，查看该边的to节点是否已经处理过，如果没有处理，将该点的所有边加入小根堆，继续选取最小的边

> 注意：map中的每个元素是一个pair，而不是一个pair迭代器，这一点不要弄混了

``` c++
list<Edge*> Prim(const Graph* graph){
    list<Edge*> results;
    unordered_set<Node*,HashNodePtr,EqualNodePtr> selected_nodes;
    auto cmp=[](const Edge* lhs,const Edge* rhs)->bool{return lhs->weight>rhs->weight;};
    priority_queue<Edge*,vector<Edge*>,decltype(cmp)> pri_queue(cmp);
    //for循环是为了处理森林问题，有可能图是不连通的，但是这又是按点选取，不像k算法按照边构造最小生成树
    for(auto every_node_pair:graph->nodes){
        //随便选一个点
        if(selected_nodes.find(&every_node_iter.second)==selected_nodes.end()){
            selected_nodes.(&every_node_iter.second);
        }
        for(auto elem:every_node_pair.second.edges){
            pri_queue.push(elem);
        }
        while(!pri_queue.empty()){
            Edge* current_edge=pri_queue.top();
            pri_queue.pop();
            Node* current_to=current_edge->to;
            //如果没有处理to节点
            //每次选取的最小边就是最小生成树的一部分，当然只有边的to节点未处理才行
            results.emplace_back(current_edge);
            if(selected_nodes.find(current_to)==selected_nodes.end()){
                selected_nodes.insert(current_to);
                for(auto elem:current_to->edges){
                    pri_queue.push(elem);
                }
            }
        }
    }
    return std::move(results);
}
```

- 简单来说p算法就是按点选，先随便选一个点，选取属于该点并且权值最小的边
- 然后再看该边的to节点是否已经处理过，**如果没有处理过**，就将属于to节点的边加入候选边组，并且将该变加入结果集,不是是个最小边就要加入哦(只有边的to节点没处理才会加入，这是为了防止形成环，)
- 然后再从候选边组选最小的边

### 最短路径

这里使用迪杰斯特拉算法，求一个节点到所有节点的路径

``` c++
unordered_map<Node*,int,HashNodePtr,EqualNodePtr> djstla(Node* head){
    if(head ==nullptr){
        return unordered_map<Node*,int,HashNodePtr,EqualNodePtr>{};
    }
    unordered_map<Node*,int,HashNodePtr,EqualNodePr> distance_map;
    //同样需要对点实行一种注册机制
    unordered_set<Node*,HashNodePtr,EqualNodeptr> selected_nodes;
    //选取距离源节点距离最短并且没有注册过的节点
    distance_map[head]=0;
    Node* current=GetMinDistanceAndUnused(distance_map,selected_nodes);
    //当所有节点都注册后，表示源点对所有节点的最短距离已经求出
    //current距离源点的最短距离已经求出，下面操作为了求出下一层的邻居节点的最短距离
    while(current != nullptr){
        //取得当前距离源点的最短距离
        int current_dis=distance_map.at(current);
        //更新源点经过current节点到下一邻居节点的距离
        for(auto edge_to:current->edges){
            //如果to节点没有加入distance_map,直接加入即可，因为一定可以到达，因为是不断通过next到达node_to的
            Node* node_to=edge_to->to;
            if(distance_map.find(node_to)==distance_map.end()){
                distance_map[node_to]=current_dis+edge_to->weight;
            }
            //加入过node_to节点，需要比较源点到node_to的距离和经过current后距离源点的距离
            distance_map[node_to]=min(current_dis+edge_to->weight,distance_map[node_to]);
        }
        //所有current节点的边都已处理完成
        selected_nodes.inert(current);
        current=GetMinDistanceAndUnused(distance_map,selected_nodes);
    }
    return std::move(distance_map);
}

Node* GetMinDistanceAndUnused(const unordered_map<Node*,int,HashNodePtr,EqualNodePtr>& distance_map,const unordered_set<Node*,HashNodePtr,EqualNodePtr>& selected_nodes){
    //选取距离源点最小并且没有注册过的点
    Node* result{nullptr};
    int distance=INT_MAX;
    //遍历map，通过set过滤
    for(auto elem:distance_map){
        if(selected_nodes.find(elem.first)==selected_nodes.end()
        && elem,second<distance){
            result=elem.first;
            distance=elem.second;
        }
    }
    return result;
}
```

这个注册过的点表示源点至该点的最小距离已经确定，无需更改。

**hash table相关的自定义hash function**
```c++ 
struct HashNode {
    std::size_t operator()(const Node& nodes) const
    {
        using std::size_t;
        using std::hash;

        return ((hash<int>()(nodes.value)
            ^ (hash<int>()(nodes.in) << 1)) >> 1)
            ^ (hash<int>()(nodes.out) << 1);
    }
};
struct EqualNode
{
    bool operator () (const Node& lhs, const Node& rhs) const
    {
        return lhs.value == rhs.value
            && lhs.in == rhs.in
            && lhs.out == rhs.out
            &&lhs.nexts==rhs.nexts
            &&lhs.edges==rhs.edges;
    }
};
struct HashNodePtr {
    std::size_t operator()(const Node* nodes) const
    {
        using std::size_t;
        using std::hash;

        return ((hash<int>()(nodes->value)
            ^ (hash<int>()(nodes->in) << 1)) >> 1)
            ^ (hash<int>()(nodes->out) << 1);
    }
};
struct EqualNodePtr
{
    bool operator () (const Node* lhs, const Node* rhs) const
    {
        return lhs->value == rhs->value
            && lhs->in == rhs->in
            && lhs->out == rhs->out
            && lhs->nexts == rhs->nexts
            && lhs->edges == rhs->edges;
    }
};
struct HashEdge {
    std::size_t operator()(const Edge& edges) const
    {
        using std::size_t;
        using std::hash;

        return ((hash<int>()(edges.weight)
            ^ (hash<int>()(edges.from->value) << 1)) >> 1)
            ^ (hash<int>()(edges.to->value) << 1);
    }
};
struct EqualEdge
{
    bool operator () (const Edge& lhs, const Edge& rhs) const
    {
        //权重、首尾节点相同，边就相同
        return lhs.weight == rhs.weight
            && lhs.from->value == rhs.from->value
            && lhs.to->value == rhs.to->value;
    }
};
```
