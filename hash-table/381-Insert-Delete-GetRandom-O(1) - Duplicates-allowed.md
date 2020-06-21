---
title: 381. O(1) 时间插入、删除和获取随机元素 - 允许重复
mathjax: true
data: 2020-06-21 18:15:08
updated:
tags:
- [哈希表，设计]
categories:
- algorithm
---s

[原题链接](https://leetcode-cn.com/problems/insert-delete-getrandom-o1-duplicates-allowed/)

### 0x0 题目详情

>设计一个支持在平均 时间复杂度 O(1) 下， 执行以下操作的数据结构。
注意: 允许出现重复元素。
insert(val)：向集合中插入元素 val。
remove(val)：当 val 存在时，从集合中移除一个 val。
getRandom：从现有集合中随机获取一个元素。每个元素被返回的概率应该与其在集合中的数量呈线性相关。

---

**测试用例:**

>// 初始化一个空的集合。
RandomizedCollection collection = new RandomizedCollection();

>// 向集合中插入 1 。返回 true 表示集合不包含 1 。
collection.insert(1);

>// 向集合中插入另一个 1 。返回 false 表示集合包含 1 。集合现在包含 [1,1] 。
collection.insert(1);

>// 向集合中插入 2 ，返回 true 。集合现在包含 [1,1,2] 。
collection.insert(2);

>// getRandom 应当有 2/3 的概率返回 1 ，1/3 的概率返回 2 。
collection.getRandom();

>// 从集合中删除 1 ，返回 true 。集合现在包含 [1,2] 。
collection.remove(1);

>// getRandom 应有相同概率返回 1 和 2 。
collection.getRandom();

### 0x1 解题思路

这道题与[380题](380-Insert-Delete-GetRandom-O(1).md)的区别就是允许插入重读元素。难就难在重复元素。

如果还像380题使用两个hashmap维持的元素下标就会出现覆盖的情况。所在在这道题中，我们采用一张哈希表实现。key存储的是元素，value存储的是元素的对应的下标。那么元素存哪？所以我们还需要维护一个list，存储具体的元素。

添加元素很简单，没什么好说的。主要是删除元素。我们唯一需要关注的点就是当前要删除的最后一个元素是否为list中的最后一个元素。如果删除的不是最后一个元素，那么我们在使用最后一个元素填充空缺的位置(这个不懂可以去看看第380题)后，还需要更新该元素在其对应的set中的索引。

并且还有一个很奇怪的点，就是随机获取一个元素的时候，我是用`Math.random()`怎么都过不了，但是使用`Math.floor(Math.random())`取随机值后，就能过了，你说奇不奇怪。明明取的随机值都是一样的啊。


### 0x2 代码实现

``` java
class RandomizedCollection {

    List<Integer> values;
    Map<Integer,Set<Integer>> valueIndexMap;
    int size;

    /** Initialize your data structure here. */
    public RandomizedCollection() {
        size=0;
        values=new ArrayList<>();
        valueIndexMap=new HashMap<>();

    }
    
    /** Inserts a value to the collection. Returns true if the collection did not already contain the specified element. */
    public boolean insert(int val) {
        //首先将该元素放到list中
        //再查看map是否有对应的key
        boolean flag= false;
        values.add(val);
        if(!valueIndexMap.containsKey(val)){
            valueIndexMap.put(val,new HashSet<>());
            flag=true;
        }
        valueIndexMap.get(val).add(size);
        size++;
        return flag;
    }
    
    /** Removes a value from the collection. Returns true if the collection contained the specified element. */
    public boolean remove(int val) {
        if(!valueIndexMap.containsKey(val)){
            return false;
        }
        int deleteIndex=valueIndexMap.get(val).iterator().next();

        //使用最后一个元素
        int replaceIndex=size-1;
        int replaceValue=values.get(replaceIndex);
        valueIndexMap.get(val).remove(deleteIndex);
        if(valueIndexMap.get(val).size()==0){
            valueIndexMap.remove(val);
        }
        //查找该元素对应的list
        //查看当前删除的元素是否为最后一个元素
        if(size-1!=deleteIndex){
            Set<Integer> replaceSet=valueIndexMap.get(replaceValue);
            replaceSet.remove(replaceIndex);
            replaceSet.add(deleteIndex);
        }

        values.set(deleteIndex,replaceValue);
        values.remove(replaceIndex);
        size--;

        return true;
    }

    /** Get a random element from the collection. */
    public int getRandom() {
        if(size==0){
            return -1;
        }
        //就是这里
        //使用Math.random()*size怎么都过不了
        int random=(int)Math.floor(Math.random()*size);//0~size-1
        return this.values.get(random);
    }
}


```

### 0x3 课后总结

这题的难点就是重复元素的出现，以前我们是直接在map里存一个下标，这里不行了。所以如果有存储下标的需要，并且有重复元素的出现，那么就可以考虑采用`HashSet<>`或`list`存储重复元素的下标。