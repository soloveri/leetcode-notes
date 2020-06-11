---
title: 380. 常数时间插入、删除和获取随机元素
mathjax: true
data: 2020-06-11 15:37:19
updated:
tags:
- [哈希表，设计]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/insert-delete-getrandom-o1/)

### 0x0 题目详情

>设计一个支持在平均 时间复杂度 O(1) 下，执行以下操作的数据结构。
insert(val)：当元素 val 不存在时，向集合中插入该项。
remove(val)：元素 val 存在时，从集合中移除该项。
getRandom：随机返回现有集合中的一项。每个元素应该有相同的概率被返回。

---

**测试用例:**

>示例 :
>// 初始化一个空的集合。
RandomizedSet randomSet = new RandomizedSet();
// 向集合中插入 1 。返回 true 表示 1 被成功地插入。
randomSet.insert(1);
// 返回 false ，表示集合中不存在 2 。
randomSet.remove(2);
// 向集合中插入 2 。返回 true 。集合现在包含 [1,2] 。
randomSet.insert(2);
// getRandom 应随机返回 1 或 2 。
randomSet.getRandom();
// 从集合中移除 1 ，返回 true 。集合现在包含 [2] 。
randomSet.remove(1);
// 2 已在集合中，所以返回 false 。
randomSet.insert(2);
// 由于 2 是集合中唯一的数字，getRandom 总是返回 2 。
randomSet.getRandom();

### 0x1 解题思路

这道题需要通过维护两个哈希表实现。一张是元素和未插入时对应size，一张是size大小和其对应的元素。下标就是当前插入的位置。为size。

``` java

class RandomizedSet {
    Map<Integer,Integer> keyIndexMap;
    Map<Integer,Integer> indexKeyMap;
    int size;
....
}

```

当插入元素时，更新俩张哈希表，并更新size。删除元素时，因为我们要随机选取元素，不同的size对应不同的元素，一旦中间的元素被移除了，在0~size-1范围内就会出现size存在，对应元素不存在的情况。所以为了避免这种情况，我们每删除一个元素后，就拿最后一个元素填充到被删除的位置，并更新size，这样就满足了随机获取的元素的要求。

### 0x2 代码实现

``` java
class RandomizedSet {
    Map<Integer,Integer> keyIndexMap;
    Map<Integer,Integer> indexKeyMap;
    int size;

    /** Initialize your data structure here. */
    public RandomizedSet() {
        keyIndexMap=new HashMap<>();
        indexKeyMap=new HashMap<>();
        size=0;
    }

    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    public boolean insert(int val) {
        if(keyIndexMap.containsKey(val)){
            return false;
        }
        //一个size对应一个元素
        keyIndexMap.put(val,size);
        indexKeyMap.put(size,val);
        size++;
        return true;
    }

    /** Removes a value from the set. Returns true if the set contained the specified element. */
    public boolean remove(int val) {
        if(!keyIndexMap.containsKey(val)){
            return false;
        }
        int deleteIndex=keyIndexMap.get(val);
        int lastKey=indexKeyMap.get(size-1);
        //将最后一个位置上的元素填充被删除的位置
        keyIndexMap.put(replaceKey,deleteIndex);
        indexKeyMap.put(deleteIndex,lastKey);
        keyIndexMap.remove(val);
        indexKeyMap.remove(size-1);
        size--;
        return true;
    }

    /** Get a random element from the set. */
    public int getRandom() {
        if(this.size==0){
            return -1;
        }
        //获取的随机数在[0,size)范围内
        int random=(int)(Math.random()*(this.size));
        return this.indexKeyMap.get(random);
    }
}

```

### 0x3 课后总结

答案是抄的，没什么好说的。