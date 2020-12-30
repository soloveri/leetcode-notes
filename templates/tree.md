---
title: tree
mathjax: true
data: 2020-02-21 22:59:13
updated:
tags:
- binary tree
categories:
- basics
---

## 二叉树的先中后序遍历

先中后序的遍历使用递归非常简单。但是这里说一下递归序的概念：
> a
/\\
b c

使用递归遍历一颗二叉树时，如果当前不符合bascase，每个节点都会被碰见三次(遇到一次算碰一次)。例如：

首先遇到a节点，遇见1次，处理完左子树回到a节点，遇见第2次，再处理右子树回到a节点，遇见第三次，然后退出。所以每个节点会被碰见3次当不是basecase时。

``` c++
void iter(BinNode* head)
{
    if(!head)
    return;
    //1 cout<<head->value;
    iter(head->left);
    //2 cout<<...
    iter(head->right);
    //3 cout<<...

}
```

在第一遇见时打印节点值，即为先序遍历。第二次遇见时打印，即为中序遍历。第三次遇见时，即为后续遍历。

**Tips：**
在普通的递归操作中，当前数据都会被遇见多次若当前不是basecase。

### 先序遍历（非递归）

需要一个栈结构。首先，头节点先入栈，以下两步操作高度固定：

- 处理栈的头节，弹出该元素(打印)。然后然后如果有右孩子入栈，如果有左孩子入栈。(先右后左)
- 继续处理栈的头节点，直至栈为空。
入栈顺序为右左，弹栈即为左右。

``` c++
void PreOrder(BinNode* head)
{
    if (!head)
    {
        return;
    }
    stack<BinNode*> temp;
    BinNode* cur{ nullptr };
    temp.push(head);
    cout << "PreOrder:";
    while (!temp.empty())
    {
        cur = temp.top();
        cout << cur->value << " ";
        temp.pop();
        if (cur->right)
        {
            temp.push(cur->right);
        }
        if (cur->left)
        {
            temp.push(cur->left);
        }
    }
    return;
}

```

### 后序遍历（非递归）

需要准备两个栈结构。跟先序差不多，不过入栈的顺序是先中再左再右。然后弹栈时，先不打印，先依次存入另一个栈中，入栈顺序为中右左。待第一个栈中的数据处理完成后，再依次打印第二个栈中的数据，即左右中，即后续遍历。

``` c++
void PostOrder(BinNode* head)
{
    if (!head)
        return;
    stack<BinNode*> temp, print_node;
    BinNode* cur{ nullptr };
    temp.push(head);
    while (!temp.empty())
    {
        cur = temp.top();
        temp.pop();
        print_node.push(cur);
        if (cur->left)
        {
            temp.push(cur->left);
        }
        if (cur->right)
        {
            temp.push(cur->right);
        }
    }
    cout << "PostOrder:";
    while (!print_node.empty())
    {
        cout <<  print_node.top() << " ";
        print_node.pop();
    }
    cout << endl;
    return;
}

```

### 中序遍历（非递归）

将整棵树利用左中节点来处理。将右节点转化为左节点。
首先一路压栈左节点直至为空，然后处理栈的头节点，然后再将当前节点的右孩子压栈。继续使用左中节点处理当前树。
因为入栈是一路压左子树，即中左，那么弹出的顺序就是左中，然后右节点继续按照中左概念处理。

``` c++
void InOrder(BinNode* head)
{
    if (!head)
    {
        return;
    }
    stack<BinNode*> temp;
    cout << "InOrder:";
    //栈为空但是处理节点不为空代表当前处理的节点是整棵树的最高节点，可能是第一次处理该节点或是该节点及其左树已经处理完
    //表示该节点的左子树已经处理完和本节点已经处理完，该处理右子树了
    while (!temp.empty() || head != nullptr)
    {
        if (head)
        {
            temp.push(head);
            head = head->left;
        }
        else
        {
            head = temp.top();
            temp.pop();
            cout << head->value << " ";
            head = head->right;
        }
    }
}

```

### 求二叉树的最大宽度

层遍历整棵树。在遍历的同时，判断当前的层数是否已经进入下一层。如果是则更新最大宽度，更新当前处理层数，否则宽度自增。
节点和自身层数的关系使用map记录。

``` c++
int MaxWide(BinNode* head)
{
    if (!head)
    {
        return 0;
    }
    int max_wide{ 0 }, cur_wide{ 0 }, cur_level{ 1 };
    BinNode* process{ nullptr };
    map<BinNode*, int>relation;
    queue<BinNode*> all_node;
    all_node.push(head);
    relation.insert(pair<BinNode*, int>(head, 1));
    while (!all_node.empty())
    {
        process = all_node.front();
        all_node.pop();
        if (process->left)
        {
            relation.insert(pair<BinNode*, int>(process->left, relation.at(process) + 1));
            all_node.push(process->left);
        }
        if (process->right)
        {
            relation.insert(pair<BinNode*, int>(process->right, relation.at(process) + 1));
            all_node.push(process->right);
        }
        if (relation.at(process) == cur_level)
        {
            ++cur_wide;
        }
        else
        {
            max_wide = cur_wide > max_wide ? cur_wide : max_wide;
            cur_wide = 1;
            ++cur_level;
        }
    }
    //最后一层不会自动更新
+    max_wide = cur_wide > max_wide ? cur_wide : max_wide;
    return max_wide;
}

```

最后一层的宽度是没有办法更新的，因为只有进入下一层时，才会更新最大宽度。所以增加的语句处理了最后一层的宽度。

**层遍历**
头节点入队，然后处理队列首元素，如果有左指针，则入队；如果有右指针，则入队。
继续处理队列首元素。

## 二叉树的递归套路

在解二叉树的题目，递归都是有套路。

1. 先看看问题需要解决什么。
2. 问题能否通过左右子树传回的信息解决。
3. 能够向左右子树获取信息。
4. 确定要什么信息，并写好信息结构。
5. 如果递归的basecase中，处理节点为nullptr时不知道返回什么信息，那么就返回nullptr，并在使用时人为判断。
6. 在处理当前节点时，要通过左右节点返回的信息加工本层需要返回的信息。

{% note info %}
因为是递归，所以向左右子树要的信息必须一样。如果当前获得的信息不能解决问题，那么尝试换一种信息，并罗列所有可能。
{% endnote %}

### 判断满二叉树

判断标准：$size= 2^L-1$
size=左子树size+右子树size；L=max(右子树高度，左子树高度)+1。
所以向子树要其高度和节点个数即可。

``` c++
bool IsFull(BinNode* head)
{
    FullInfo info = process(head);
    return info.size == (1 << info.height) - 1 ? true : false;
}
FullInfo process(BinNode* head)
{
    if (!head)
    {
        return FullInfo(0, 0);
    }
    FullInfo left = process(head->left);
    FullInfo right = process(head->right);
    FullInfo info(0, 0);
    info.size = left.size + right.size + 1;
    info.height = max(left.height, right.height) + 1;
    return info;
}
```

### 求一棵树的最大距离

最大距离就是点a到点b经过的节点数最大是多少？
思考：如果正在处理的节点是x，那么当前的最大距离可能有两种结果：

- 经过x节点：最大距离是左边最远的节点到右子树的最右节点，即左树高度+1右树高度+1
- 不经过x节点：那么最大距离是左子树的最大距离或右子树的最大距离
然后在加工本层函数，即x节点需要返回的信息。

``` c++
int MaxDis(BinNode* head)
{
    MaxDisInfo info = ProMax(head);
}
MaxDisInfo ProMax(BinNode* head)
{
    if (!head)
    {
        return MaxDisInfo(0, 0);
    }
    MaxDisInfo left = ProMax(head->left);
    MaxDisInfo right = ProMax(head->right);
    MaxDisInfo result{ 0,0 };
    result.height = max(left.height, right.height) + 1;
    result.max_dis = max(left.height + right.height + 1, max(left.max_dis, right.max_dis));
    return result;
}

```

### 判断搜索二叉树

搜索二叉树定义：子树为搜索二叉树，左子树的最大值小于x->value,右子树最小值小于x->value.
比较简单，在此不赘述。
可以用递归套路或者是中序遍历。如果使用中序遍历(非递归)，如果遍历的结果

``` diff "中序非递归"
void InOrder(BinNode* head)
{
    if (!head)
    {
        return;
    }
    int pre=min;
    stack<BinNode*> temp;
    cout << "InOrder:";
    //栈为空但是处理节点不为空代表当前处理的节点是整棵树的最高节点，表示该节点的左子树已经处理完
    while (!temp.empty() || head != nullptr)
    {
        if (head)
        {
            temp.push(head);
            head = head->left;
        }
        else
        {
            head = temp.top();
-            cout << head->value << " ";
+           if(head->value<pre)
+           {
+               return;
+           }
+           pre= head->value;
            head = head->right;
        }
    }
}
```

是否递归套路时，如果节点为空时，最小值和最大值怎么返回？返回0有可能会干扰判断。

### 判断是否为完全二叉树

利用宽度遍历

- 首先判断是否有右孩子而无左孩子，如果成立则不是
- 当发现一个节点左右孩子不双全，则剩下的节点必须是叶子节点，否则不是完全二叉树


解决所有树形dp问题的套路

### 判断两节点的最低公共祖先

**经典做法**

``` c++
BinNode* LowestAncestor(BinNode* head, BinNode* left, BinNode* right)
{
    if (head == nullptr || head == left || head == right)
    {
        return head;
    }
    BinNode* left_result = LowestAncestor(head->left, left, right);
    BinNode* right_result = LowestAncestor(head->right, left, right);
    if (left_result != nullptr && right_result != nullptr)
    {
        return head;
    }
    return left_result != nullptr ? left_result : right_result;
}
```

- 如果遇到了`left`，那么就返回`left`;如果遇到了`right`,那么就返回`right`,否则如果为`null`，则返回`null`
- 如果左数返回的值和右树返回的值都不为空，那么表示`head`时最低公共祖先，因为`left`和`right`都已找到
- 否则，找到了谁就返回谁，即`left`不为空就返回`left`,`right`依然。

**递归套路做法**

### 计算当前节点的后继节点

找到节点x的后继节点(基于中序遍历，每个节点都有左右孩子和父节点)
两种情况

节点x有右孩子，则找到右子树的最左孩子，即是x的后继
节点x没有右孩子，查看x的父节点y，看父节点是否是其父节点的左孩子，如果是，则y的父节点是节点x的后继

否则，继续查看y的父节点z是否是其父节点的左孩子，直至处理节点的父节点为空或者是父节点的左孩子
处理节点的父节点为空是因为所求节点是树的最右节点，所求节点仍是最远父亲的右孩子


### 二叉树的序列化和反序列化

**序列化**
即将内存中的数以字符串的形式记录下来，并且能通过该字符串恢复原二叉树并且无歧义。经典做法是先序遍历。

需要注意的点：

- 节点值为负数，需要特殊处理
- 没有boost库，字符串的分割需要特殊处理

``` c++
//先序递归遍历，字符序列化
string TreeToStr(BinNode* head)
{
    if (!head)
    {
        return string{ "#_" };
    }
    string result{ "" };
    result += to_string(head->value)+"_";
    result += TreeToStr(head->left);
    result += TreeToStr(head->right);
    return result;
}
BinNode* StrToTree(string& serial)
{
    //字符串分割入队列，然后依次消费队列中的字符
    vector<string> str;
    boost::algorithm::split(str, serial, boost::is_any_of("_"));
    queue<string> temp;
    /*
    //没有分割函数时，需要自己分割
    string sniff{""};
    for(auto elem:serial){
        if(elem == '_'){
        //这么做是因为有可能一个数字位数大于1，知道遇到分隔符时才加入队列
            temp.push(sniff);
            sniff="";
            continue;
        }
        sniff+=elem;
    }
    */
    for (auto index : str)
    {
        temp.push(index);
    }
    return ConvertStr(temp);
}

BinNode* ConvertStr(queue<string>& str)
{
    //每个节点的左右子树都会被记录，
    string temp = str.front();
    str.pop();
    if (temp == "#")
    {
        return nullptr;
    }
    else if(temp=="-"){
        //这两句是处理负数的情况
        temp+=str.front();
        str.pop();
        
        BinNode* head = new BinNode(stoi(temp,0,10));
        head->left = ConvertStr(str);
        head->right = ConvertStr(str);
        return head;
    }
    else{
        BinNode* head = new BinNode(stoi(temp,0,10));
        head->left = ConvertStr(str);
        head->right = ConvertStr(str);
        return head;
    }
}



```

**Tips:**
判断一棵树是否是另一颗子树的子树->字符序列化加kmp算法

### 折纸问题

待定，简单的中序遍历

### 前缀树

什么是前缀树就不说了，简单说一下我的理解。
字符的值不在节点中，而是到目标节点的路上，目标节点中有两个值{pass，end}，假设字符的值为`a`，pass表示`a`总共出现了多少次，end表示以`a`结尾的字符串有多少个。

管理字符`a`的信息存储在下一层节点中，`a`出现在路上，node A-----a---->node B。

``` c++
struct TrieNode
{
    int pass;
    int end;
    //next数组表示当前节点到下一节点是否有路，如果是字符，那么总共可能有26条路
    array<TrieNode*, 26> next {nullptr};
    TrieNode(int p,int e):pass(p),end(e){}
};

class TrieTree
{
public:
    TrieTree()
    {
        root = new TrieNode(0, 0);
    }
    void Insert(string str);
    int Search(string str);
    int Prefix(string str);
    void Delete(string str);

private:
    TrieNode* root;
};


void TrieTree::Insert(string str)
{
    if (!str.empty())
    {
        return;
    }
    int index = 0;
    TrieNode* tnode = root;
    //根节点的pass值代表前缀树总共有插入了多少个字符串
    ++tnode->pass;
    for (auto elem : str)
    {
        index = (char)elem - 'a';
        if (tnode->next[index] == nullptr)
        {
            tnode->next[index] == new TrieNode(0, 0);
        }
        //每个字符的pass值是在a的路的终点增加
        tnode = tnode->next[index];
        ++tnode->pass;
    }
    //因为节点在字符的下面，所以字符遍历完后，node一定是最后一个节点
    //处理完所有字符后，最后一个节点的end值自增
    ++tnode->end;
    return;
}

int TrieTree::Search(string str)
{
    if (!str.empty())
    {
        return 0;
    }
    TrieNode* tnode = root;
    int index{ 0 };
    for (auto elem : str)
    {
        index = (char)elem - 'a';
        //如果字符还没遍历完，路径就结束了，那么肯定不存在待查询字符
        if (tnode->next[index] == nullptr)
        {
            return 0;
        }
        tnode = tnode->next[index];
    }
    return tnode->end;
}

int TrieTree::Prefix(string str)
{
    if (!str.empty())
    {
        return 0;
    }
    TrieNode* tnode = root;
    int index{ 0 };
    for (auto elem : str)
    {
        index = (char)elem - 'a';
        //同理，如果路径提前结束
        if (tnode->next[index] == nullptr)
        {
            return 0;
        }
        tnode = tnode->next[index];
    }
    //求前缀数量使用pass信息，因为最后一个节点的pass表示节点的值出现的次数
    return tnode->pass;
}
void TrieTree::Delete(string str)
{
    if (!TrieTree::Search(str))
    {
        return;
    }
    TrieNode* tnode = root;
    int index{ 0 };
    TrieNode* deleted_node{ nullptr };
    int deleted_index{ -1 };
    set<TrieNode*> deleting_node;
    --tnode->pass;
    for (auto elem : str)
    {
        index = (char)elem - 'a';
        if(--tnode->next[index]->pass== 0)
        {
            //虽然pass值为0表示该节点已经可以删除了，但是当前得的后续节点也必须记录，因为要手动释放内存
            deleted_node = deleted_node == nullptr ? tnode : deleted_node;
            deleted_index = deleted_index == -1 ? index : deleted_index;
            deleting_node.insert(tnode->next[index]);
        }
        tnode = tnode->next[index];
    }
    //当前删除的字符串end值减1即可，无论该节点是否会被删除
    tnode->end--;
    deleted_node->next[index] = nullptr;
    for (auto elem : deleting_node)
    {
        delete elem;
    }
}
```

### 贪心策略
 
用对数器解决算法的正确性，完整证明非常困难。**将算法与暴力解比较**即可验证算法是否正确。
那么贪心策略如何决策？这就要靠经验了。
贪心几乎都要使用排序和堆