---
title: 链表
mathjax: true
data: 2020-02-21 20:14:00
updated:
tags:
- 链表
categories:
- algorithm
---
## 基础知识

1. 单双链表的原地反转 
不使用额外变量的方式就是使用head指针
2. 打印两个有序链表的公共部分

    很简单：两个链表各设一个扫描指针，哪个指针指向的值小移动哪个指针，因为移动较小的指针，才有可能追上较大指针的值。

## 回文操作

判断一个链表时否回文，即正序遍历链表和逆序遍历链表，结果一样就是回文。那么最重要的部分就是找到链表的中点，这里使用快慢指针法，即快指针每次走两步，慢指针每次走一步。并且下文中所有的链表都包含头节点。

``` c++ "快慢指针"
//right_mid is the slow ptr,the cur is the fast ptr
node* right_mid{head->next},*cur{head};
while(cur->next&&cur->next->next){
    right_mid=right_mid->next;
    cur=cur->next->next;
}
```

快慢指针具体的使用需要看具体的情况。比如在**包含头节点的链表**中，将慢指针初始化为首元素节点，快指针初始化为头节点，那么：

- 链表长度为奇数：循环终止时，慢指针恰好位于中点位置
- 链表长度为偶数：循环终止时，慢指针位于链表的右中位置

在不包含头节点的链表中，快慢指针都初始化为首元素节点，那么：

- 链表长度为奇数：循环终止时，慢指针恰好位于中点
- 链表长度为偶数:循环终止时，慢指针位于上中位置。

我个人人为带有头节点的链接更好用，因为在使用反转链表原地判断是否为回文时，更加方便，当链表长度为偶数时，慢指针位于右半序列的第一个元素，更方便反转。
链表的基本操作
 反转链表

 

 面试做法：时间复杂度O(N)
 将后半部分数据反转，即反转单链表，中点位置数据的next指向null，然后头和尾依次比较，比较完成后，还需要将链表调回正常状态
 
 
 算时间用纳秒


## 链表实现荷兰国旗问题

**笔试做法**:先将数据放入数组中，在数组中完成荷兰国旗，然后再将数据中的数据重新串成链表,实现非常简单，再次不过多赘述。

---

**面试做法**：保证稳定性，且额外i空间为常数
小于区域、等于区域、大于区域都有头和尾两个变量表示，初始时都为null

``` c++
void PartitionLinkNoSpace(node* head, int pivot)
{
    if (!head->next)
        return;
    node* les_beg{ nullptr }, * les_end{ nullptr },
    * equ_beg{ nullptr }, * equ_end{ nullptr },
    * gtr_beg{ nullptr }, * gtc++r_end{ nullptr };
    node* cur = head->next;
    node* next{ nullptr };

    while (cur)
    {
+       next = cur->next;
+       cur->next = nullptr;
        if (cur->value < pivot)
        {
            if (les_beg && les_end)
            {
                //end后面的空指针丢就丢了，因为新插入的节点自带null指针
                les_end->next = cur;
                les_end = cur;
            }
            else
            {
                les_beg = les_end = cur;
            }
        }
        if (cur->value == pivot)
        {
            if (equ_beg && equ_end)
            {
                equ_end->next = cur;
            }
            else
            {
                equ_beg = equ_end = cur;
            }
        }
        if (cur->value > pivot)
        {
            if (gtr_beg && gtr_end)
            {
                gtr_end->next = cur;
                gtr_end = cur;
            }
            else
            {
                gtr_beg = gtr_end = cur;
            }
        }
-         cur=cur->next;
+         cur = next;
    }
// link the less equal greater area
//如果小于区域存在，则小于区域尾节点连接equ_beg

if (les_beg)
{
    les_end->next = equ_beg;
    //等于区域存在，则equ_end是等于区域尾节点
    //等于区域不存在，equ_end是小于区域尾节点
    //哪个区域存在，哪个去连接大于区域
    equ_end = equ_end ? equ_end:les_end ;
}
if (equ_end)
{
    equ_end->next  = gtr_beg;
}
    les_beg = les_beg ? les_beg : (equ_beg ? equ_beg : gtr_beg);
    head->next = les_beg;
    return;
}

```

如果使用原来的方法，那么生成的分割链表尾节点是没有`nullptr`的，所以这里将当前待处理节点从原始链表中分割出来，并将当前节点的next设为`nullptr`。

并且在重新连接各个分割后的链表时：

1. 如果小于区域存在，则小于区域尾节点连接等于区域(无论等于区域是否存在)；
2. 调整等于区域的尾指针
3. 最后连接大于区域
4. 最后返回头节点指针(小于、等于、大于三个区域，哪个按序先存在，对应区域头节点为首节点)

> Tips:
    如果只给定单链表的一个指针a，没有前节点的指针。是否能原地删除a指向的节点？
    适用于简单链表：
    - 将a的next节点的值复制到a节点的值域中，再调节a的next指针指向a->next->next节点
    - 但是上述方法不适用于删除最后一个节点

## 复制含有随机指针的链表

**笔试做法**：利用map表，首先将老链表依次复制并放入hash表中
<node,node>分别为老链表的节点和复制好的新节点

``` c++
RandomNode* CopyRandomList(RandomNode* head) {
    if (!head)
        return nullptr;
    map<RandomNode*, RandomNode*> node_map;
    RandomNode* cur = head;
    while (cur)
    {
        node_map.insert(pair<RandomNode*, RandomNode*>
            (cur,new RandomNode(cur->value)));
        cur = cur->next;
    }
    cur = head;
    //cur is  the old node
    //node_map.at(cur) is the corresponding copy node
    while (cur)
    {
        if (cur->next)
        {
            node_map.at(cur)->next = node_map.at(cur->next);
        }
        if (cur->random)
        {
            node_map.at(cur)->random = node_map.at(cur->random);

        }
        cur = cur->next;
    }
    cur = head;

    return node_map.at(head);
}
```

---

**面试做法**：首先复制每个节点，将每个复制后的节点放置在其母体后面，然后每次拿出两个节点设置random指针
比如1的random指针指向3，那么1'的random指针指向3'，而3'就在3的后面，3可以通过1的random指针获得。无需使用map结构。

``` c++
RandomNode* CopyRandomNoSpace(RandomNode* head)
{
    if (!head)
        return nullptr;
    RandomNode* cur{ head }, * cur_next{ head->next };
    //copy node
    while (cur)
    {
        cur_next = cur->next;
        cur->next = new RandomNode(cur->value);
        cur->next->next = cur_next;
        cur = cur_next;
        //cur_next = cur->next;
    }
    cur = head;
    RandomNode* cur_copy{ nullptr};
    RandomNode* new_head{ head->next };
    // set random node
    while (cur)
    {
        cur_next = cur->next->next;
        cur_copy = cur->next;
        cur_copy->random = cur->random ? cur->random->next : nullptr;
        cur = cur_next;
    }
    //seperate the duplicate
    cur = head;
    cur_copy = cur->next;
    while (cur)
    {
        cur_next = cur->next->next;
        cur_copy = cur->next;
        cur->next = cur_next;
        cur_copy->next = cur_next ? cur_next->next : nullptr;
        cur = cur_next;
    }
    return new_head;
}
```

那么能否将上述的设置随机节点和分离操作合并到一个循环是否可以？

当然是不可以的，如果当前节点的随机节点指向其后方节点时并不错，但是如果当前节点的随机节点指向其前方节点，那么随即指针指向的节点`a`的后方已经不是`a`的复制节点`a'`了，而是原始链表中`a`的下一个节点`b`。因为当前节点的前方已经被分离了。

## 判断两个链表(可能有环)的第一个公共节点

要完成上述操作，首先需要链表判断有无环，再去求第一个交叉节点。

### 判断链表是否有环

**笔试做法：**
利用`set`结构，遍历链表时，将每个节点加入`set`中，如果当前节点已经在`set`中出现，那么当前节点就是首个入环节点，否则就无环。

---

**面试做法：** 
无需额外空间，利用快慢指针。

首先快指针走两步，慢走一步，在第一次相遇的时候，慢指针原地不动，快指针回到开头。如果快指针为nullptr，则无环，返回null。

然后快慢指针都依次走一步，再次相遇时的节点则为入环节点。

``` c++
node* GetLoopNode(node* head)
{
    //构成环最少需要两个节点
    //bingqie 如果第二个节点的next不指向第一个节点，也不会构成环
    if(head ==nullptr || head->next==nullptr|| head->next->next==nullptr)
        return nullptr;
    node* fast{ head }, * slow(head);
    fast = fast->next->next;
    slow = slow->next;
    while (slow != fast)
    {
        if (fast->next == nullptr || fast->next->next == nullptr)
        {
            return nullptr;
        }
        slow = slow->next;
        fast = fast->next->next;
    }
    //如果fast能于slow相遇，则一定有环
    fast = head;
    while (fast != slow)
    {
        fast = fast->next;
        slow = slow->next;
    }
    return fast;

}
```

### 判断首个相交节点

因为是单链表结构，所以链表结构只有以下五种。

- 两个无环单链表相交
- 两个有环单链表相交，相交节点在环外
- 两个有环单链表相交，相交节点在环内
- 一个有环、一个无环是永远也不可能相交的
- 两个有环单链表不相交

**判断无环链表是否相交**

1. 首先遍历两条链表，并记录长度和尾节点(遍历长度只需一个变量，即遍历链表a时，n++；遍历链表b时，n--；最后n即使长度只差)
2. 如果两个尾节点的内存地址不相等，则一定没有公共节点，因为是单链表，相交时尾节点一定相同
3. 尾节点相等，则较长的链表先走两条链表长度只差的长度，然后同时遍历两条链表，节点地址相等时的第一个节点便是公共节点头。(即先遍历长链表，使剩余长度和锻炼表长度相等)

``` c++
node* NoLoop(node* lhs, node* rhs,node* end) {
    node* left_ptr{ lhs }, * right_ptr{ rhs };
    int n{ 0 };
    //遍历到尾节点，而不是最后一个空节点
    do
    {
        ++n;
        left_ptr = left_ptr->next;
    } while (left_ptr->next!=end);
    do
    {
        --n;
        right_ptr = right_ptr->next;
    } while (right_ptr->next!=end);
    //n>0,则第一个链表长，否则第二个链表长
    if (!(left_ptr == right_ptr))
    {
        return nullptr;
    }

    //注意布尔值的定义哦
    left_ptr = n>0 ? lhs : rhs;//谁长选谁
    right_ptr = n>0 ? rhs : lhs;//谁短选谁
    n = abs(n);
    while (n--)
    {
        left_ptr = left_ptr->next;
    }
    //right_ptr = left_size < right_size ? lhs : rhs;
    while ((left_ptr!=end && right_ptr!=end)
        &&left_ptr != right_ptr)
    {
        left_ptr = left_ptr->next;
        right_ptr = right_ptr->next;
   }
    return left_ptr;
}
```

**判断有环链表公共节点**

1. 两个链表不相交

2. 两个链表相交，首公共节点在环之外。那么把入环节点当作两条链表的终止节点，此问题就可转化为无环链表求首个相交节点的问题。因为首公共节点肯定在环外之外的节点。
3. 两个链表相交，接入环的节点不同，那么两个接入环的节点都是首公共节点

如何区别1、3两种情况？
让入环节点loop1走一圈，如果遇到入环节点loop2，则是第三种情况，否则是不相交，是第一种情况。

``` c++
node* DoubleLoop(node* lhs, node* rhs, node* left_loop, node* right_loop)
{
    if (left_loop == right_loop)
    {
        return NoLoop(lhs, rhs, left_loop);
    }
    else
    {
        node* temp = left_loop->next;
        while (temp != left_loop)
        {
            if (temp == right_loop)
            {
                return left_loop;
            }
            temp = temp->next;
        }
    }
    return nullptr;
```

## 其他

### cpp的map结构

- 插入操作：在map中时，要构造pair结构
    > map.insert(pair<int,int>(3,3))
使用insert插入时，当添加重复key时，对应的value是最后一个插入的值

- 获取key对应的value，主要有`at`和`[]`两种操作，然而`[]`不会做下标检查，不存在时会自动插入键值对
    > map.at(key)
    > map\[key]

### cpp的stack结构

- 获取栈顶元素
    > stack.pop()，返回栈顶元素的引用
- 出栈
    > stack.pop(),只会弹出栈顶元素，返回void，而不是返回栈顶元素，这么做是因为如果返回的元素复制失败，那么原来的值不见了，也没有复制成功，该值就丢了

