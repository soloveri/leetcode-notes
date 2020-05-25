---
title: 39. 组合总数 
mathjax: true
data: 2020-05-24 23:43:36
updated:
tags:
- [递归]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/combination-sum/)

### 0x1 题目详情

> 给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
candidates 中的数字可以无限制重复被选取。
说明：
所有数字（包括 target）都是正整数。
解集不能包含重复的组合。

---

**测试用例:**
> 示例 1:
输入: candidates = [2,3,6,7], target = 7,
所求解集为:
[
  [7],
  [2,2,3]
]

### 0x2 解题思路

这道题的难点在于如何去重。初次看到这道题我就想不是和[求幂集](04-Power-Set-LCCI.md)这道题差不多嘛。只不过是一个数能够选择一次或多次。说干就干。

对于一个数，我们有三种处理方式：

- 放弃当前数
- 使用当前数，但是只使用一次
- 使用当前数，下一轮仍可使用该数
结果发现有了大量的重复解，这个去重问题困扰了我很久，最后在答题区获得了答案。这种解法类似于深度优先遍历，我们将数组的每一个元素都作为路径的起点去处理，
![搜索路径图](images/39-combination-sum.drawio.svg)

具体的思路在图上也已经标明了，在这里仍要声明一下：排序是为了更快的找到答案，设置下一次搜索的起点才是去重的根本原因。至于为什么要选择设置下一次的搜索起点呢，这个通过画具体的路径图是可以得出来得。

### 0x3 代码实现

1. 第一次实现得错误代码,这种思路对于那种每个元素只需要一次或零次的，且没有重复元素的好使一点。对于有重复元素的，会出现重复的结果。

``` java
class Solution {
  public List<List<Integer>> combinationSum(int[] candidates, int target) {
    List<List<Integer>> result=new ArrayList<>();
    if(candidates==null || candidates.length==0){
        return result;
    }
    Arrays.sort(candidates);
    recur(target,candidates,0,result,new ArrayList<Integer>());
    return result;
  }
  private void recur(int target,int[] candidates,int index,List<List<Integer>> result,List<Integer> curResult){
    if(target==0){
        result.add(curResult);
        return;
    }

    if(target<0 || index>=candidates.length){
      return 0;
    }
    //对于一个元素我们有三种选中
    List<Integer> temp=new ArrayList<>(curResult);
    List<Integer> temp=new ArrayList<>(curResult);
    //不要当前元素
    recur(target,candidates,index+1,result,curResult);
    //只要当前元素一次
    recur(target-candidates[index],candidates,index+1,result,curResult);
    //要当前元素多次
    recur(target-candidates[index],candidates,index,result,curResult);
  }
}

```

---

2. 下面是能够正确去重得代码,采用深度优先遍历的思想，虽然也是从头到尾找到目标路径，但不是像上面这种考虑每个元素要还是不要。而是考虑将每个元素都作为路径上的值，遍历所有的尝试，最后再根据题目特点去重、剪枝等等。

``` java
class Solution {
  public List<List<Integer>> combinationSum(int[] candidates, int target) {
      List<List<Integer>> result=new ArrayList<>();
      if(candidates==null || candidates.length==0){
          return result;
      }
      Arrays.sort(candidates);
      recur(target,candidates,0,result,new ArrayList<Integer>());
      return result;

  }
  private void recur(int target,int[] candidates,int index,List<List<Integer>> result,List<Integer> curResult){
      if(target==0){
          result.add(new ArrayList<Integer>(curResult));
          return;
      }
      //如果当前target已经小于最小的可用元素了，就不用再往下看了

      for(int i=index;i<candidates.length;i++){
          if(target<0||target<candidates[index]){
              return;
          }
          curResult.add(candidates[i]);
          recur(target-candidates[i],candidates,i,result,curResult);
          curResult.remove(curResult.size()-1);
      }
  }
}
```

### 0x4 课后总结

对于找路径的问题，尤其是递归的问题，怎么说呢，我总结了两种常用的尝试方法：

- 第一种就是像该开始我思考的方向，从头到尾遍历元素，考虑的是我们要还是不要当前元素，要的话要多少，最后探测出base case即可。感觉这种对于没有重复的元素并且一个元素只有0或1这种选择时，比较好用。
- 第二种就是我们不再思考哪个元素要，哪个元素不要。而是非常暴力对每个元素都作为路径上的一种尝试，遍历所有的结果。最后再根据路径时剪枝、去重等等。

而且而且，我怎么感觉这道题跟全排序挺像的，只是结果集中元素的个数没有要去。全排列也是找路径，只不过得用上所有的元素。
