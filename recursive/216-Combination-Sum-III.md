---
title: 216. 组合总数 III
mathjax: true
data: 2020-05-25 13:26:40
updated:
tags:
- [递归]
categories:
- algorithm
---
## 前言

[原题链接](https://leetcode-cn.com/problems/combination-sum-iii/)

相似题目:

- [组合总数](39-Combination-Sum.md)
- [组合总数II](40-Combination-Sum-II.md)
- [组合总数IV](377-Combination-Sum-IV.md)

### 0x1 题目详情

> 找出所有相加之和为 n 的 k 个数的组合。组合中只允许含有 1 - 9 的正整数，并且每种组合中不存在重复的数字。
说明：
所有数字都是正整数。
解集不能包含重复的组合。

---

**测试用例:**
> 示例 1:
输入: k = 3, n = 7
输出: [[1,2,4]]

### 0x2 解题思路

总的来说，这道题和第39题是第40题的结合体。源数组中没有重复元素，且每个数只能选择0次或1次。也是一个路径遍历的问题。我们的目标不光是找到组成target的组合，而且必须满足组合中的个数为k。会了39题，完全没难度，就是在记录答案时多了一个判断条件而已。至于剪枝的条件和39题相同，就是当我们的目标target小于我们能选择的元素组中的任何一个元素时，就不可能产生答案了。路径图可以看看第39题了，我这不画了，也挺麻烦的。![第39题路径图](images/39-combination-sum.drawio.svg)。

### 0x3 代码实现

代码实现和第39题基本没差。

``` java
class Solution {
    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> result=new ArrayList<>();
        if(n<0){
            return result; 
        }
        if(k<1){
            return result;
        }
        recur(n,1,k,result,new ArrayList<Integer>());
        return result;

    }
    private void recur(int target,int index,int k,List<List<Integer>> result,List<Integer> curResult){
        if(target==0&& k==0){
            result.add(new ArrayList<Integer>(curResult));
            return;
        }
        //如果k<=0时，直接返回，因为已经不满组k个数的要求了
        if(k<=0){
            return;
        }
        for(int i=index;i<10;i++){
            //进行与第39题相同条件的剪枝
            if(target<i){
                return;
            }
            curResult.add(i);
            //因为一个元素只能选择一次
            recur(target-i,i+1,k-1,result,curResult);
            curResult.remove(curResult.size()-1);
        }
    }
}

```

### 0x4 课后总结

我感觉出来了，这个组合系列的问题就是全排列的变种，全排列就是把每一个元素作为路径上的一个节点作为尝试。注意是每一个元素作为路径中的节点，所以对于排列组合这种问题的递归函数中，大部分都会循环处理每一个元素，不然怎么尝试每一个呢？