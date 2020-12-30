---
title: 堆的问题
mathjax: true
data: 2020-02-16 14:16:58
updated:
tags: 
- sort
categories:
- algorithm
- sort
---
## 堆的基本操作

什么是堆我也不说了，大家都懂，这里讲讲大根堆的两个基本操作，小根堆类似

### heapInsert(插入操作)

{% note info %}
`heapInsert`仅完成调整动作，无需关心堆的大小
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
`heapify`仅仅完成调整操作，同样无需关心堆的大小
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

### 堆排序

#### 大根堆的构建

**常规方法**
有了堆的两个基本操作，堆排序就很简单了。如果给定一个序列，将序列中的值依次插入堆中，就可构建一个大根堆。

``` c++
for(auto index=0;index<target.size();++index){//O(N)
    heapInsert(target,index);//O(log N)
}
```

时间复杂度为$O(N*logN)$，因为每插入一个节点其最差都可能走到堆顶点，复杂度为$log N$,所以插入N个节点的时间复杂度为$O(N*log N)$

**高效方法**
上述常规方法是基于一次只给一个值的情况构建一个大根堆，如果一次给了所以所有值，可以假设现在已经有了一个堆，只需要使用`heapify`操作快速构建大根堆，值的调整顺序从下往上，从右往左。这样保证了每一颗子树都是大根堆。

``` c++

for(auto index=target.size()-1;index >= 0;--index){
    //假设现在已经有个一个target.size()的堆，只需调整即可
    heapify(target,index,target.size());
}
```
时间复杂度为$O(N)$,计算过程有点复杂，以后有时间再讲。至于为什么不是$O(N*log N)$，因为每个节点不一定都移动$log N$次。比如最下层节点，最坏情况也是移动0次，跟常规方法不同。

#### 堆构建后的排序过程

现在已经拿到了一个大根堆，每次将堆顶点依次和堆的最后一个顶点交换结合，即最大值放到序列后侧，当大根堆的大小为0时，就完成了堆排序

``` c++
while(heapSize){//O(N)
    swap(target[0],target[heapSize-1]);
    heapify(target,0,--heapSize);//O(log N)
}
```

时间复杂度为$O(N*log N)$,因为每个值最差时都需要调整$log N$次，所以总的时间复杂度为$O(N* logN)$

### 堆排序复杂度

- 时间：$O(N*log N)$
- 空间：$O(1)$

## 堆结构

**系统实现vs自己实现**
什么时候用系统的,什么时候需要自己写？
除非需要在数据插入到堆后，还要更改这个数据的大小，否则一般情况都可以用系统实现的堆结构

**比较器**
比较器就是比较规则，一般遵循一下规定：

- 返回正数，第二个数据排在前面
- 返回负数，第一个数据排在前面
- 0， 返回谁无所谓

### 相关问题

> 已知一个几乎有序的数组，几乎有序是指，如果把数组排好顺序的话，每个元素移动的距离可以不超过k，并且k相对于数组来说比较小。请选择一个合适的排序算法针对这个数据进行排序给定一个int数组A，同时给定A的大小n和题意中的k，请返回排序后的数组。

{% note info %}
几乎有序，元素移动不超过k就会到达最总位置，可以使用小根堆结构构造(k+1)个元素中查找最小值，此时找到的最小值一定是整个序列的最小值，因为元素移动不会超过k就会归位。
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
		//heapSize= k==n-1? heapSize-1:heapSize;
		//k = k == (target.size() - 1) ? k : k + 1;
	}
}

```
需要注意的地方有俩点：

- 移动不会超过k个距离，则需要准备大小为(k+1)的小根堆，因为要包括自身，如从0位置移动3位置，即总长度为4
- 最后一个窗口需要额外处理，当处理晚最后一个长度为(k+1)的窗口时，就没有新数据进行heapInsert了。