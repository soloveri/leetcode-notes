---
title: Bucket-sort
mathjax: true
data: 2020-02-17 21:40:38
updated:
tags:
- algorithm
- sort
categories:
- algorithm
- sort
---
## 桶排序的种类

桶排序一般有两种：计数排序和基数排序
桶排序是有序的，因为入桶的顺序和出桶的顺序是一样的(即队列操作)，所以保持了原序列的顺序。

正常的桶排序需要十个桶，而以下的方法只用了一个桶实现：

``` c++
void BucketSort(vector<int>& target) {
  if (target.size() < 2)
    return;
  BucketSort(target,0, target.size(), getMaxBits(target));
}
void BucketSort(vector<int>& target,
  int beg, int end, int maxBits) {

  //序列中最大数字有几位，就完成几次入桶出桶操作
  for (int index = 1; index <= maxBits; ++index) {
    vector<int> bucket(end - beg, 0);
    vector<int> help(10, 0);
    //此循环完成了第index位的数字统计，即0有多少、1有多少...9有多少
    for (auto i = beg; i < end; ++i) {
      auto digital = getDigit(target[i], index);
      ++help[digital];
    }

    //此循环完成了第index位<=1、<=2...<=9的数共有几个，即第i位与i-1位相加，统计了<=i的数字个数
    for(auto i=1;i<(int)help.size();++i){
      help[i] += help[i - 1];
    }
    //此循环从后向前处理目标序列，获得第i处的数后，查看该数第index位的数digital，再看有多少个数<=digitial，然后将该数入桶。假设有k个数<= digital,那么就该将此数放入桶的第(k-1)处
    for (auto i = end-1; i >= beg; --i) {
      auto digitial = getDigit(target[i], index);
      bucket[--help[digitial]] = target[i];
    }

    //将桶中数据拷贝至源数组，然后进行下一轮的桶排序
    for (auto i = beg; i < end; ++i)
      target[i] = bucket[i];
    }
}
int getMaxBits(vector<int>& target) {
  int max{ 0 };
  for (auto elem : target)
    max = max < elem ? elem : max;
  int result{ 0 };
  while (max) {
    max /= 10;
    ++result;
  }
  return result;
}
//获取数据elem的第digitalNum处的数，首先将想要获得数移动至个位，在进行模除10，余数即目标数据
//假设782，获取第2位数据，因为只需要移动一次，除以10^(2-1)，然后模除10即可
int getDigit(int elem, int digitalNum) {
  return (elem / (int)pow(10, digitalNum - 1)) % 10;
}

```

## 总结

### 时空复杂度

- 时间：$O(N)$
- 空间：取决与目标数据，即为$O(M)$

### 缺陷

桶排序是非基于比较的排序，所以应用场景十分有限，只能应用于正整数类型的序列