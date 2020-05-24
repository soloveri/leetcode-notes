---
title: 40. 组合总数 II 
mathjax: true
data: 2020-05-25 01:28:42
updated:
tags:
- [递归,求路径]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/combination-sum-ii/)

### 0x1 题目详情

> 给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
candidates 中的每个数字在每个组合中只能使用一次。
说明：
所有数字（包括目标数）都是正整数。
解集不能包含重复的组合。 

---

**测试用例:**
>示例 1:
输入: candidates = [2,5,2,1,2], target = 5,
所求解集为:
[
  [1,2,2],
  [5]
]

### 0x2 解题思路

这道题是第39题[组合问题](39-Combination-Sum.md)的改版，但是比39题还是要简单点。这道题数组种的元素只有选择要还是不要，但是数组种的元素又是重复的，所以对于求路径的问题，还是不方便使用第一种思路(即考虑要不要当前元素的方法),所以我们采用第二种方法，将数组中的每一个元素作为路径上的一种尝试。
![组合总数II](images/40-combination-sum-II.drawio.svg),

在进行去重的过程中，我们可以采取第39题的措施，但是这并不够，还需要处理同一级递归中的相同元素，如果在同一级的探索中，出现了重复的数字，那么我们可以直接跳过。这里的同一级是在一个函数的for循环中，当前的循环内才算是同一级。

### 0x3 实现代码

``` java
class Solution {
public List<List<Integer>> combinationSum2(int[] candidates, int target) {
    List<List<Integer>> result=new ArrayList<>();
    if(candidates== null || candidates.length==0){
        return result;
    }
    Arrays.sort(candidates);

    recur(candidates,0,target,new ArrayList<Integer>(),result);
    return result;

}
private void recur(int[] candidates,int index,int target,List<Integer> curResult,List<List<Integer>> result){
    if(target==0){
        result.add(new ArrayList<Integer>(curResult));
        return;
    }

    for(int i=index;i<candidates.length;i++){
        if(target<candidates[i]){
            return;
        }
        //在同一递归函数内完成循环才算是同一级
        //注意这里的i>index,而不是i>0,因为递归函数有多个递归子过程，在子过程中开始的循环起点就不是0了啊
        //在这里也搞了半天
        if(i>index && candidates[i]==candidates[i-1]){
            continue;
        }
        curResult.add(candidates[i]);
        recur(candidates,i+1,target-candidates[i],curResult,result);
        curResult.remove(curResult.size()-1);
    }
}
}
```

### 0x4 课后总结

这道题吧，和39题差不多，都是采取将每个元素作为路径中的一种尝试的方式。而不是考虑要不要当前元素。只不过去重的过程中还需要根据递归树进行相应的总结。