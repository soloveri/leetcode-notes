---
title: 04. 幂集
mathjax: true
data: 2020-05-21 14:22:46
updated:
tags:
- [递归，位运算]
categories:
- algorithm
---

### 0x1 题目详情

[原题链接](https://leetcode-cn.com/problems/power-set-lcci/)

> 幂集。编写一种方法，返回某集合的所有子集。集合中不包含重复的元素。
说明：解集不能包含重复的子集。
---

**测试用例:**

> 输入： nums = [1,2,3]
 输出：
[
  [3],[1],[2],[1,2,3],[1,3],[2,3],[1,2],[]
]

### 0x2 解题思路

**思路一:**

这道题我的第一想法不就是从左往右单向的递归模型吗？想都不用想。

对于每一个数，我们都有两种选择：

- 选择要当前数
- 选择不要当前数。

递归的base case就是当我们的下标已经达到最大时，停止递归。时间复杂度为$O(N)$,因为递归终止时我们扫描了一遍数组。

递归

**思路二:**

采用位运算，我们可以使用1个bit代表数组的中一个数，因为1个bit的状态只有两种，每个数选择不选择的状态也只有两种，与上边讲得递归状态一致。
求幂集就是在对应bit数组成的不同的二进制数中，把每个二进制数中bit状态为1的数加入当前结果集。然后处理完一个二进制数后把当前结果集加入最后的结果集即可。(其中讲二进制数是不准确的，应该是组成的不同数中)。

以长度为2的数组为例：两位bit能够组成的数有四个：00、01、10、11，对应的结果集[[],[2],[1],[1,2]]。

### 0x3 代码实现

递归法：

``` java 
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result=new ArrayList<>();
        if(nums== null || nums.length==0){
            result.add(new ArrayList<Integer>());
            return result;
        }
        List<Integer> curResult=new ArrayList<>();
        recur(nums,0,curResult,result);
        return result;

    }
    private void recur(int[] nums,int index,List<Integer> curResult,List<List<Integer>> result){
        if(index== nums.length){
            result.add(curResult);
            return;
        }
        //不要当前元素
        recur(nums,index+1,curResult,result);

        //要当前元素
        List<Integer> temp=new ArrayList<>(curResult);
        temp.add(nums[index]);
        recur(nums,index+1,temp,result);

    }
}

```

位运算：我咋感觉时间复杂度还高了？

``` java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result=new ArrayList<>();
        if(nums== null){
            return result;
        }
        //需要求出个数为nums.length的bit数能够组成的二进制数的个数
        int power=(int)Math.pow(2,nums.length);
        for(int i=0;i<power;i++){
            List<Integer> curResult=new ArrayList<>();
            //j代表右移的位数
            for(int j=0;j<nums.length;j++){
                if(((i>>>j)&1)==1){
                    curResult.add(nums[j]);
                }
            }
            result.add(curResult);
        }
        return result;
    }
}
```

### 0x4 课后总结

递归方法很简单没什么好说了，倒是位运算的方法给我了一种新的思路。

如果递归时一个数的状态**只有两种**，那么就可以把递归改成位运算的实现方法。