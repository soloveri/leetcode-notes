---
title: O(N*logN)的排序算法
mathjax: true
data: 2020-02-14 17:04:42
updated:
tags:
categories:
- algorithm
- search
---
## 前言

本篇讲述关于ologn的排序算法，包括归并排序、快速排序、堆排序

## 基本概念

递归时间复杂度T(N)基本原理:假设规模为n，左右侧递归处理规模为n/2，其余代码的复杂度为O(1)，那么复杂度为$T(N)=2T(\frac{N}{2})+O(1)$

base case:

### master公式

$T(N)=aT(\frac{N}{b})+O(N^d)$

**公式解读**
子问题规模都为$\frac{N}{b}$,一共处理子问题$a$次，除去子问题外的时间复杂度为$O(N^d)$, $d$就是复杂度的最高阶，最后复杂度分三种情况：

1. $\log_ba < d$ , 则$T(N) = O(N^d)$
2. $\log_ba > d$ , 则$T(N) = O(N^{\log_ba})$
3. $\log_ba = d$ , 则$T(N) = O(N^d {\times \log N})$

**只能用来求子规模相等的递归情况**，但是不一定非要把子规模等分，比如从左起规模为2n/3的子过程和从右起规模也为2n/3的子过程，虽然子过程中间有重合的部分，但是仍可以用master公式

## 归并排序

简而言之，就是将待目标序列分割成两组，先将左组和右组分别排好序，在最后将这两组合并，在合并的过程中实现整个序列的有序。

### 递归形式

**base case**
当递归到序列的长度为1时就停止分割序列，开始一次合并序列，这里序列采取左闭右开的形式。

``` c++
bool MergeSort(vector<int>& target) {
  if (!target.size())
    return false;
  custom_sort(target, target.begin(), target.end());
    return true;
}
void custom_sort(vector<int>& target, vector<int>::iterator begin, vector<int>::iterator end) {
  //base case
  if (begin+1 == end)
    return;

  auto mid = begin + (end - begin) /2;
  custom_sort(target, begin, mid);
  custom_sort(target, mid, end);
  merge(target, begin,  mid, end);
}

void merge(vector<int>& target, vector<int>::iterator begin, 
  vector<int>::iterator mid, vector<int>::iterator end) {
  vector<int>help(distance(begin,end));
  auto help_point=help.begin(), left_point = begin, right_point = mid;
  while (left_point < mid && right_point < end) {
    /*
    if (*right_point < *left_point) {
        for (auto index = left_point; index != mid; ++index)
        cout << "(" << *index << "," << *right_point << ")" << " ";
    }
    */
   //此句先拷贝左值维持稳定性
    *help_point++ = *left_point <= *right_point ? *left_point++ : *right_point++;
  }
  while (left_point < mid)
   * help_point++ = *left_point++;
  while (right_point < end)
   * help_point++ = *right_point++;
  for (auto index : help)
   * begin++ = index;
}

```

在合并数组时，需要注意：

- 需要开辟长度为左右序列和的辅助空间，因为合并时需要将左序列和右序列的值有序的拷贝到辅助数组，最后再拷贝至原序列
- 当左值a和右值b相等时，**先拷贝左值**至辅助数组就会保持原序列的稳定性，因为两个数相等时，a先进辅助数组，当拷贝b时，b仍会在a的后面，保持稳定性

### 时空复杂度

- 时间：应用master公式，求得时间复杂度为$O(N*log N)$
- 空间：因为每次合并，都需要开辟额外空间，最多开辟长度为N的辅助空间，所以空间复杂度为$O(N)$

**相关问题**

1. 为什么比$O(N^2)$算法快？

    因为$O(N^2)$算法在大量的浪费比较行为，前一次的比较跟后一次的比较毫无关系。而归并不同，前一次排序的结果能够为下一次排序使用。

### 小和问题

**什么是小和？**
假设有序列`{2,3,5,4,1,7}`,值`4`产生的小和即为序列中位置在`4`左侧且值小于`4`的值产生的和，`4`产生的小和值为：`2+3=5`，以此类推，计算序列中每个值的小和，加起来即为序列的小和。暴力求法很简单在这就不说了，主要讲讲利用归并求小和的方法。

**归并求法**
仍以上述序列为例。`{2,3,5,4,1,7}`，在`2`的左侧有`3、5、4、7`都大于`2`，所以在求`3、5、4、7`的小和时，都会将把`2`加一遍，即总共会把`2`加4遍。那么我们就可以换一种求法。

对于值`2`，其右侧大于`2`的值有四个，所以小和`sum += 2*4`；然后对于值`3`，其右侧大于`3`的值有俩个，所以小和`sum +=3*2`;以此类推，对每个值都如此操作，就会得到序列的小和。

在归并排序的同时，可以顺带解决这个问题。

比如在合并`{2,3,5}` `{1,4,7}`两个**有序**子序列时，假设现在左序列指针指向2，右序列指针指向1，2>1,则不会产生新小和，将1拷贝到额外数组中的同时右指针指向4，此时2<4，从4开始距离尾部的长度为2(大于`2`的值有两个)，所以此时新产生的小和为：`sum += 2*2`

以此类推，在合并的同时对左侧序列的每个值都是如此操作即可求出小和。
{% note info %}

- 小和值= 合并当前左序列本身时得到的小和+合并当前右序列本身时得到的小和值+合并当前左右序列的小和
- 新的部分小和值只会在合并序列时产生

{% endnote %}

**相关问题**

1. 为什么只对左侧序列中的值进行上述操作？

    因为左侧序列和右侧序列的小和都已确定，只有合并产生的小和待计算。  

2. 与归并排序有什么不同？

    在合并左右序列时，**如果左值与右值相等，必须先拷贝右值到额外数组中，但会失去稳定性。** 这与原始归并排序不同。原始归并排序在左右值相等时，会先拷贝左值并保持稳定性。
    因为在查看右侧序列中有多少个值大于左值时，依靠的是当前右值距离尾部的长度，如果先拷贝左值，就会失去一部分小和。  
    比如左右序列`{1,2,5}`、`{1，3，6}`，左右值都为1，如果先拷贝左值，那么本身左`1`产生的部分小和：`1*2=2(右侧序列有两个值大于1)`就会被忽略，但是先拷贝右值就会避免这种情况。
3. 为什么要排序？

    因为是通过下标计算的，所以序列必须有序，才能够通过下标确定个数

### 逆序对问题

#### 什么是逆序对

> 逆序对问题。给一列数$a_1,a_2,…,a_n$，求它的逆序对数，即有多少个有序对(i,j)，使得i < j 但 $a_i>a_j$。n可以高达$10^6$。

#### 解决思路

这与小和问题的解决思路很相似，小和问题是求右序列中有多少个值大于左序列中的当前值，这道题是求左序列中有多少个值大于右序列中的当前值，很相似，这里就不赘述了。

## 快速排序

快速排序可以由荷兰国旗问题改进而成，下面先来看看荷兰国旗。

### 荷兰国旗问题

> 现有红白蓝三个不同颜色的小球，乱序排列在一起，请重新排列这些小球，使得红白蓝三色的同颜色的球在一起。这个问题之所以叫荷兰国旗问题，是因为我们可以将红白蓝三色小球想象成条状物，有序排列后正好组成荷兰国旗。

**解决思路**
可以将排在中间的颜色定位目标num，将序列分割成三个部分：

- 左侧：小于num的部分
- 中间：等于num的部分
- 右侧：大于num的部分

---
荷兰国旗是对序列中特定数进行分割，如果将分割粒度细化到长度为1的序列，对每个数都进行分割，就会完成序列的升序排序

### partition操作

完成一次荷兰国旗问题，就完成了一次partition操作，也就是完成了对特定num的划分。至于特定num选谁？这又是一个问题。

- 每次都选待分割序列的最后一个数，待分割完成后，将特定num与大于num区域的第一个数交换
- 随机选择待分割序列的任意一个数，将其与最后一个数进行交换后，进行分割，然后将特定num与大于num区域的第一个数交换

``` c++
void QSort(vector<int>& target,
  int beg, int end) {
  //base case
  if (beg  >= end)
    return;

  auto index = parition(target, beg, end);
  QSort(target, beg, index.first);
  QSort(target, index.second, end);
}
//return the low bound of small area and upper bound of large area
pair<int,int>  parition(vector<int>& target,
int beg, int end) {
  auto small_point = beg, large_point = end - 1;
  //version 2.0
  //auto num = target[end];
  //version 3.0
  random_device rd;
  //获取[beg,end]范围的随机数
  uniform_int_distribution<int> dis(beg, end);
  default_random_engine gen{ rd() };
  std::swap(target[dis(gen)], target[end]);
  while (beg <= large_point) {
    if (target[beg] < num)
        swap(target, beg++, small_point++);
    else if (target[beg] == num)
        ++beg;
    else
        swap(target, beg, large_point--);
    }
  swap(target[end], target[++large_point]);
  return make_pair( --small_point,++large_point );

}
```

### 时空复杂度

- 时间：基于特定num的选取，快排的时间复杂度有两种情况
  - 最坏复杂度：如果每次选取的基数num的最终位置都处于序列的两极，比如最左或最右侧，那么每次partition只能分割出两个区域。假设num每次都处于最右侧，那么每次都只能分割出两个区域，每次都将num放置正确位置的比较次数分别为$n-1、n-2、n-3... 1$，那么partition `N`次的代价为$O(N^2)$
  - 最好复杂度：如果每次选取的num都能将序列正好划分为两半，应用master公式，那么最好的就是$O(N* log N)$
- 空间：空间复杂度同样分为最好和最差
  - 最差：最差的情况出现与num处于极端位置，那么递归树的高度为（n-1），即空间复杂度为$O(N)$
  - 最好：num每次都将序列分成两半，递归数高度为$O(log N)$,因为每次二分，二分$log N$次，序列长度变为1
- 改进:可以改进的地方就是num的选取，每次都随机选取的话，那么平均时空复杂度就为：$O(N*log N)$和$O(log N)$,当然由于概率问题，这是最后的期望值

## 堆排序

什么是堆我也不说了，大家都懂，这里讲讲大根堆的两个基本操作。以大根堆为例。`heapInsert`操作将index处的数重新调整位置，将堆重新调整为大(小)根堆。`heapify`操作将index处的数重新调整，同样将堆重新调整为大(小)跟堆。

### heapInsert(插入操作)

{% note info %}
`heapInsert`仅完成index处的调整动作，无需关心堆的大小
{% endnote %}
在index处插入新值，默认在`[0...index-1]`已经是一个堆了，插入后需要重新调整在`[0...index]`范围的堆

``` c++ "大根堆"
void heapInsert(vector<int>& target,int index){
    while(target[index] > target[(index-1)/2]){
        swap(target[index],target[(index-1)/2]);
        //交换后需要更新index
        index = (index-1)/2;
    }
}

```

当当前节点的值大于其父节点的值时，才会交换二者的值，当index达到堆顶点时，`target[index]=target[(index-1)/2]`,会终止循环

**时间复杂度**
由于堆的高度为$log N$,所以一个数从堆底部到达堆顶只需要交换$log N$次，复杂度即$O(log N)$
> 当数组已经达到最大时，需要将数组扩容才能继续插入数据构建大根堆，假设当前数组长度为1，一次扩大一倍，总共需要扩容$log N$次至N的大小。
当扩容完成后，还需将原数组的数据拷贝至新扩容的数组，是$O(N)$的复杂度，总的扩容复杂度为$O(N*log N)$ ,平均到每个元素，也是$\frac{O(N*log N)}{N}=O(log N)$

### heapify(弹出操作)

{% note info %}
`heapify`仅仅完成index处的调整操作，同样无需关心堆的大小
{% endnote %}

所要做的就是调整index处的值，使堆重新调整大根堆

``` c++
void heapify(vector<int>& target,int index,int heapSize){
    auto left = index*2 + 1;
    //当左子节点存在时
    while(left< heapsize){
        //在子节点中挑出大的
        auto largest= (left+1)<heapsize && target[left]<target[left+1]? left+1 : left;
        //在index和其较大子节点中挑出大的
        largest = target[index]< target[largest]? largest:index;
        //如果最大的就是index本身，无需继续调整
        if(largest == index)
            break;
        swap(target[targest],target[index]);
        //如果最大的值是index子节点，将二者交换，并将index更新为largest
        index = largest;
        left = index*2 +1;
    }
}
```

**时间复杂度**
同样的，一个数从堆顶到达堆底，所需的操作次数也为堆的高度：$log N$次,所以弹出操作的时间复杂度同样为$O(log N)$

### 堆排序的完整过程

#### 大根堆的构建

**常规方法$(O(N*logN))$**
有了堆的两个基本操作，堆排序就很简单了。如果给定一个序列，将序列中的值依次插入堆中，就可构建一个大根堆。

``` c++
for(auto index=0;index<target.size();++index){//O(N)
    heapInsert(target,index);//O(log N)
}
```

时间复杂度为$O(N*logN)$，因为每插入一个节点其最差都可能走到堆顶点，复杂度为$log N$,所以插入N个节点的时间复杂度为$O(N*log N)$

**高效方法$(O(N))$**
上述常规方法是基于一次只给一个值的情况构建一个大根堆，如果一次给了所以所有值，可以假设现在已经有了一个堆，只需要使用`heapify`操作快速构建大根堆，值的调整顺序从下往上，从右往左。这样保证了每一颗子树都是大根堆。

``` c++

for(auto index=target.size()-1;index >= 0;--index){
    //假设现在已经有个一个target.size()的堆，只需调整即可
    heapify(target,index,target.size());
}
```

时间复杂度为$O(N)$,计算过程有点复杂，以后有时间再讲。至于为什么不是$O(N*log N)$，因为每个节点不一定都移动$log N$次。比如最下层节点，最坏情况也是移动0次，跟常规方法不同。

#### 大根堆构建后的排序过程

现在已经拿到了一个大根堆，每次将堆顶点依次和堆的最后一个顶点交换结合，即最大值放到序列后侧，当大根堆的大小为0时，就完成了堆排序

``` c++
while(heapSize){//O(N)
    swap(target[0],target[heapSize-1]);
    heapify(target,0,--heapSize);//O(log N)
}
```

时间复杂度为$O(N*log N)$,因为每个值最差时都需要调整$log N$次，所以总的时间复杂度为$O(N* logN)$

#### 堆排序复杂度

- 时间：$O(N*log N)$
- 空间：$O(1)$

### 堆结构

**什么时候用系统的,什么时候需要自己写？**
除非需要在数据插入到堆后，还要更改这个数据的大小，否则一般情况都可以用系统实现的堆结构

**比较器**
比较器就是比较规则，一般遵循一下规定：

- 返回正数，第二个数据排在前面
- 返回负数，第一个数据排在前面
- 0， 返回谁无所谓

### 相关问题

> 已知一个几乎有序的数组，几乎有序是指，如果把数组排好顺序的话，每个元素移动的距离可以不超过k，并且k相对于数组来说比较小。请选择一个合适的排序算法针对这个数据进行排序给定一个int数组A，同时给定A的大小n和题意中的k，请返回排序后的数组。

{% note info %}
几乎有序，元素移动不超过k就会到达最总位置，可以使用小根堆结构构造 **(k+1)** 个元素中查找最小值，此时找到的最小值一定是整个序列的最小值，因为元素移动不会超过k就会归位。
以此类推，将大小为k的窗口依次向后移动，每移动一次窗口，就会求出来一个最小值。总共移动n次，即可将数组排序。

{% endnote %}

``` c++
void almost_heapify(vector<int>& target, int index, int heapSize) {
  auto left = index * 2 + 1;
  while (left < heapSize) {
    auto minst = left + 1 < heapSize && target[left + 1] < target[left] ? left + 1 : left;
    minst = target[index] < target[minst] ? index : minst;
    if (minst == index)
        break;
    swap(target[index], target[minst]);
    index = minst;
    left = index * 2 + 1;
  }
}

void almost_heapInsert(vector<int>& target, int elem, int index) {
  target[index] = elem;
  while (target[index] < target[(index - 1) / 2]) {
    swap(target[index], target[(index - 1) / 2]);
    index = (index - 1) / 2;
  }
}

void almost_sort(vector<int>& target, int k) {
  //首先建立大小为（k+1）的小根堆
  vector<int> temp(k+1);
  int heapSize = 0;
  for (auto index = 0; index < k+1; ++index) {
    almost_heapInsert(temp, target[index], heapSize++);
  }

  //滑动窗口n次
  auto right_point = k+1;
  for (auto index = 0; index < (int)target.size(); ++index) {
    //heapSize = (index + 3) > (target.size() - 1) ? heapSize - 1 : heapSize;
    target[index] = temp[0];
    swap(temp[0], temp[heapSize - 1]);
    almost_heapify(temp, 0, --heapSize);
    //当index+k == target.size()-1时，已经没有数可以入堆了，即最后一个长度为(K+1)的窗口已经处理完了
    if((index + k) < (target.size() - 1))
      almost_heapInsert(temp, target[right_point++], heapSize++);
  }
}

```

需要注意的地方有俩点：

- 移动不会超过k个距离，则需要准备大小为(k+1)的小根堆，因为要包括自身，如从0位置移动3位置，即总长度为4
- 最后一个窗口需要额外处理，当处理晚最后一个长度为(k+1)的窗口时，就没有新数据进行heapInsert了。