---
title: 90. 子集 II
mathjax: true
data: 2020-06-02 01:14:29
updated:
tags:
- [递归，路径数]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array-ii/)

### 0x1 题目详情

>给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。
说明：解集不能包含重复的子集。

---

**测试用例:**
>示例:
输入: [1,2,2]
输出:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]

### 0x2 解题思路

看到这道题我的第一思路就行从左到右单向的递归模型，但是这里要去重，这个模型就不是很好用了。

看到题解中有一种思路是采用路径树的思路。这里所谓的路径树是我自己取的，具体思路就是在每层递归时数组中的每个元素都可以被选择。然而这里的限制的是每个元素只能使用0次或多次。看到这种思路我就在想，这样做岂不是每个元素都会被选择了？仔细一瞧发现我是对这种递归模型没有理解透彻。

``` java
for(int i=index;i<nums.length;i++){
        if(i>index && nums[i]==nums[i-1]){
            continue;
        }
        //begin
        curResult.add(nums[i]);
        recur(nums,i+1,curResult,result);
        //end
        curResult.remove(curResult.size()-1);
    }
```

注意begin和end下方的两条语句。处理每个元素时，首先会计算加入当前元素后的结果，然后当回到本层递归时，end语句会将原来在本层添加的元素删除掉，然后处理下一个元素。那这样不就表示不要当前元素的那种情况了嘛。顺序就是先计算包含当前元素的结果，然后计算不包含当前元素的结果。这和我总结的单向递归模型好用一点啊，单向的似乎只适合简单一点的。

当然这道题最重要的点就是去重，采用的方法也是在同一级的循环中，如果当前元素和前一个元素相同，那么则直接跳过。这种方法在组合系列的问题中也出现过。[组合问题 II](40-Combination-Sum-II.md)

### 0x3 代码实现

注意代码中加入结果的条件。每次递归时首先会将当前结果加入结果集。空集不会重复加入，因为每向下递归一层时，传进去的当前结果都不可能为空，因为进入递归前都会有一个加入动作。每一轮循环都是以当前位置元素开头的结果集。比如当前当前索引为1，即表示此轮循环完成了寻找以1开头的结果。

``` java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> result=new ArrayList<>();
        //对于路径树还是没有理解透彻
        if(nums== null || nums.length==0){
            return result;
        }
        Arrays.sort(nums);
        recur(nums,0,new ArrayList<Integer>(),result);
        return result;

    }
    private void recur(int[] nums,int index,List<Integer> curResult,List<List<Integer>> result){
        //什么时候加入
        result.add(new ArrayList<Integer>(curResult));

        for(int i=index;i<nums.length;i++){
            if(i>index && nums[i]==nums[i-1]){
                continue;
            }
            curResult.add(nums[i]);
            recur(nums,i+1,curResult,result);
            curResult.remove(curResult.size()-1);
        }

    }
}

```

### 0x4 课后总结

说实话，对于子集啊，组合问题啊，这些问题用递归树不要太容易奥，剪枝问题也容易想。