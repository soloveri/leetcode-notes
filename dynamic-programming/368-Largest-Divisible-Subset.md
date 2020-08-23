---
title: 368. 最大整除子集
mathjax: true
data: 
updated:
tags:
- 动态规划
- 递归
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/largest-divisible-subset)

---

### 0x0 题目详情

>给出一个由无重复的正整数组成的集合，找出其中最大的整除子集，子集中任意一对 (Si，Sj) 都要满足：Si % Sj = 0 或 Sj % Si = 0。
如果有多个目标子集，返回其中任何一个均可。

---

**测试用例:**

>示例 1:
输入: [1,2,3]
输出: [1,2] (当然, [1,3] 也正确)

>示例 2:
输入: [1,2,4,8]
输出: [1,2,4,8]

### 0x1 解题思路

这道题如果求的是最大整除子集的个数那是没什么难度的。对于在位置`i`上的数`nums[i]`,我们需要在`0~i-1`之间找到一个`nums[j]`,使得截至到`nums[j]`为止的最大整除子集个数最大。这样截止到`nums[i]`为止的最大整除子集的元素个数就求出来了,遍历完整个数组,最大整除子集中的元素个数也出来了。关键在于最开始我想在遍历完数组后同时也将答案收集好。

我的初始思路,对于每一个数`nums[i]`维护一个对应的`ArrayList<Integer>`,其中存储的是截至到`nums[i]`为止的最大整除子集。采用上述求最大子集元素个数的思路,但是这样复杂度太高了。导致我迟迟无法下笔。

后来看到答案,恍然大悟,我们只需提供一个前缀数组,其中`nums[i]=j`表示从元素`nums[i]`跳到其前面的元素`nums[j]`。这样如果我们找到了最大整除子集的最后一个元素,一个一个的往前跳,每跳一次,就保存一个元素。当无法可跳时表示收集完毕。

### 0x2 代码实现

``` java
class Solution {
    public List<Integer> largestDivisibleSubset(int[] nums) {
        LinkedList<Integer> result=new LinkedList<>();
        if(nums==null || nums.length==0){
            return result;
        }
        Arrays.sort(nums);
        int[] dp=new int[nums.length];
        int[] pre=new int[nums.length];
        dp[0]=1;
        pre[0]=-1;
        int point=0;
        int right=0;
        for(int i=1;i<nums.length;i++){
            dp[i]=1;
            pre[i]=-1;
            for(int j=i-1;j>-1;j--){
                if(nums[i]%nums[j]==0){
                    dp[i]=Math.max(dp[i],1+dp[j]);
                    //i的前缀只能是使得dp[i]更大的位置j
                    pre[i]=dp[i]==1+dp[j]?j:pre[i];
                }
            }
            point=dp[i]>dp[point]?i:point;
        }
        while(pre[point]!=-1){
            result.offerFirst(nums[point]);
            point=pre[point];
        }
        result.offerFirst(nums[point]);
        return result;
    }
}
```

### 0x3 课后总结

有时候对于dp的问题,不一定能一次遍历就能求解。有时候需要一次正向遍历完毕然后从末尾到头再来一次遍历回溯求解。

