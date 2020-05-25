---
title: 377. 组合总数 IV
mathjax: true
data: 2020-05-25 13:29:40
updated:
tags:
- [递归,动态规划]
categories:
- algorithm
---
## 前言

[原题链接](https://leetcode-cn.com/problems/combination-sum-iv/)

同类型题目:

- [组合总数](39-Combination-Sum.md)
- [组合总数II](40-Combination-Sum-II.md)
- [组合总数III](216-Combination-Sum-III.md)

### 0x1 题目详情

> 给定一个由正整数组成且不存在重复数字的数组，找出和为给定目标正整数的组合的个数。

---

**测试用例:**
>示例:
nums = [1, 2, 3]
target = 4
所有可能的组合为：
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)
请注意，顺序不同的序列被视作不同的组合。
因此输出为 7。

---
**进阶：**
如果给定的数组中含有负数会怎么样？
问题会产生什么变化？
我们需要在题目中添加什么限制来允许负数的出现？

### 0x2 解题思路

这比同类的组合问题中的任何一个都简单，就是**第39题**不需要去重的版本。所以思路没什么好说的，就是递归版本会超时，所以需要将递归式改为动态规划，只要递归能写出来，改dp闭着眼都行。

### 0x3 代码实现

递归版本不需要去重，但是仍然可以剪枝加快速度。

``` java "递归版本"
class Solution {
    public int combinationSum4(int[] nums, int target) {
        if(target<0){
            return 0;
        }
        Arrays.sort(nums);
        return recur(nums,target);
    }
    int recur(int[] nums,int target){
        if(target==0){
            return 1;
        }
        int result=0;
        for(int i=0;i<nums.length;i++){
            //剪枝的条件与第39题没差
            if(target<nums[i]){
                return result;
            }
            result+=recur(nums,target-nums[i]);
        }
        return result;
    }
}

```

但是使用递归版本肯定会超时，所以花了几分钟改为动态规划。

``` java "动态规划版本"

class Solution {
    public int combinationSum4(int[] nums, int target) {
        if(target<0){
            return 0;
        }
        Arrays.sort(nums);
        return recur(nums,target);
    }
    int recur(int[] nums,int target){
        int[] dp=new int[target+1];
        dp[0]=1;
        for(int j=1;j<dp.length;j++){

            for(int i=0;i<nums.length;i++){
                if(j<nums[i]){
                    break;
                }
                //因为j>nums[i]，所以肯定不会越界
                //所以不需要额外进行判断下标是否越界
                dp[j]+=dp[j-nums[i]];
                // dp[j]+=getValue(dp,j-nums[i]);
            }
        }
        return dp[target];
    }
}

```

### 0x4 课后总结

对于进阶部分的思考：

- 如果给定的数组中含有负数会怎么样？

    如果数组中有负数，且源数组中有相反数，例如`[-4,1,2,3,4]`，那么结果中的长度就是无限的了，可以不停的添加相反数对，导致结果中的元素无限多。那这道题就没结果了，因为结果可能有无数种

所以我们遇到负数时，可以直接跳过，但是如果非要在结果中包含负数的话，我们可以做如下限制：

- 我们需要在题目中添加什么限制来允许负数的出现？
  - 因为数组中的元素我们可以是无限选的，所以当我们遇到负数时，在当前结果`curResult`中不能出现相反数
  - 或者限制负数的使用次数
