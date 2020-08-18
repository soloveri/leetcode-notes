---
title: 312. 戳气球
mathjax: true
data: 2020-08-18 21:17:55
updated:
tags:
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/burst-balloons/)

---

### 0x0 题目详情

>有 n 个气球，编号为0 到 n-1，每个气球上都标有一个数字，这些数字存在数组 nums 中。
现在要求你戳破所有的气球。如果你戳破气球 i ，就可以获得 nums[left] * nums[i] * nums[right] 个硬币。 这里的 left 和 right 代表和 i 相邻的两个气球的序号。注意当你戳破了气球 i 后，气球left 和气球 right 就变成了相邻的气球。
求所能获得硬币的最大数量。

>说明:
你可以假设 nums[-1] = nums[n] = 1，但注意它们不是真实存在的所以并不能被戳破。
0 ≤ n ≤ 500, 0 ≤ nums[i] ≤ 100

**测试用例:**

>示例:
输入: [3,1,5,8]
输出: 167 
解释: nums = [3,1,5,8] --> [3,5,8] -->   [3,8]   -->  [8]  --> []
     coins =  3*1*5      +  3*5*8    +  1*3*8      + 1*8*1   = 167



### 0x1 解题思路

这道题如何按照我们先去戳爆一个气球m,然后更新气球`m-1`和`m+1`的新边界,是非常麻烦的,我目前还没有思考出行之有效的方法。

那么我们换一种思路,逆向DP。就跟递归做法一样,我们先找到递归出口。对于dp,我们可以把每一个气球都当作最后一个戳爆。比如我们把气球`m`当作最后一个来戳爆,那么这时总的分数就是气球`m`左边的分数加上气球`m`右边的分数再加上气球`m`作为最后一个戳爆的分数。

可以看到,气球分数的区间是有界的。如果此时气球`m`所在分数区间为`[left,right]`,那么气球`m`作为最后一个戳爆的分数就是`nums[left-1]*nums[m]*nums[right+1]`。这里求`m`的分数用到了区间`[left,right]`之外的数据。

### 0x2 代码实现

**递归版本:**
``` java

class Solution {
    public int maxCoins(int[] nums) {
        if(nums==null || nums.length==0){
            return 0;
        }
        return recur(nums,0,nums.length-1);

    }

    //左闭右闭区间,返回在这个区间内的最高得分
    private int recur(int[] nums,int left,int right){
       
        if(left>right){
            return 0;
        }
        if(left==right){
            return (left-1<0?1:nums[left-1])*nums[left]*(right+1>=nums.length?1:nums[right+1]);
        }

        int result=Integer.MIN_VALUE;
        for(int i=left;i<=right;i++){
            result=Math.max(result,(left-1<0?1:nums[left-1])*nums[i]*(right+1>=nums.length?1:nums[right+1])+
            recur(nums,left,i-1)+recur(nums,i+1,right));
        }
        return result;
    }
}
```

---

**动态规划版本:**

``` java
class Solution {
    public int maxCoins(int[] nums) {
        if(nums==null || nums.length==0){
            return 0;
        }
        return recur(nums,0,nums.length-1);

    }

    //左闭右闭区间,返回在这个区间内的最高得分
    private int recur(int[] nums,int left,int right){
        if(left==right){
            return nums[left];
        }
        int[][] dp=new int[nums.length][nums.length];
        int length=nums.length;
        dp[0][0]=nums[0]*nums[1];
        dp[length-1][length-1]=nums[length-1]*nums[length-2];
        for(int i=1;i<nums.length-1;i++){
            dp[i][i]=nums[i-1]*nums[i]*nums[i+1];
        }
        
        for(int j=1;j<length;j++){
            for(int i=j-1;i>-1;i--){
                for(int q=i;q<=j;q++){
                    dp[i][j]=Math.max(dp[i][j],(i-1<0?1:nums[i-1])*nums[q]*(j+1>=length?1:nums[j+1])+
                    (i>q-1?0:dp[i][q-1])+(q+1>j?0:dp[q+1][j]));
                }
            }
        }
        return dp[0][length-1];
    }
}
```

### 0x3 课后总结

对于将递归改成动态规划的题目,对于改的过程我们需要注意以下几个问题:

1. 我们要注意递归求解的方向。比如我们拥有二维dp数组。我们需要思考递归的过程在从上到下用这张表还是从左到右?

2. 得出递归使用表的方向后,在动态规划中填充表的顺序是与**递归相反的!!!**