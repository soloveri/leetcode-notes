---
title: 416. 分割等和子集
mathjax: true
data: 2020-08-28 19:33:22
updated:
tags:
- 动态规划
- 递归
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/partition-equal-subset-sum)

---
### 0x0 题目详情

>给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

>注意:
每个数组中的元素不会超过 100
数组的大小不会超过 200

---
**测试用例:**
>示例 1:
输入: [1, 5, 11, 5]
输出: true
解释: 数组可以分割成 [1, 5, 5] 和 \[11].

>示例 2:
输入: [1, 2, 3, 5]
输出: false
解释: 数组不能分割成两个元素和相等的子集.

### 0x1 解题思路

这道题可以转化为最基础的0-1背包问题。转化后的问题就是使用数组中的值填满容量为`sum(nums)/2`的背包。

那么我采用的做法时从左向右依次判断每个物品是否放入背包。递归函数返回的是`[index,nums.length)`能否产生符合要求的放置方案。

### 0x2 代码实现

**递归做法:**
``` java
class Solution {
    public boolean canPartition(int[] nums) {
        if(nums==null || nums.length==0){
            return false;
        }
        int sum=0;
        for(int i=0;i<nums.length;i++){
            sum+=nums[i];
        }
        if(sum%2!=0){
            return false;
        }
        sum/=2;
        return recur(nums,0,0,sum); 
    }
    //重量从nums中选择
    boolean recur(int[] values,int index,int already,int bag){
        boolean[][] dp=new boolean[values.length+1][bag+1];
        int N=values.length;
        int M=bag;

        if(already== bag){
            return true;
        }
        if(already>bag){
            return false;
        }
        //虽然重量没超，但是没有数据可选了
        if(index==values.length){
            return false;
        }
        boolean p1=recur(values,index+1,already,bag);
        boolean p2=recur(values,index+1,already+values[index],bag);
        return p1|p2;
    }
}
```

---

**动态规划:**

``` java
class Solution {
    public boolean canPartition(int[] nums) {
        if(nums==null || nums.length==0){
            return false;
        }
        int sum=0;
        for(int i=0;i<nums.length;i++){
            sum+=nums[i];
        }
        if(sum%2!=0){
            return false;
        }
        sum/=2;
        return recur(nums,0,0,sum); 
    }
    //重量从nums中选择
    boolean recur(int[] values,int index,int already,int bag){
        boolean[][] dp=new boolean[values.length+1][bag+1];
        int N=values.length;
        int M=bag;
        for(int i=0;i<=N;i++){
            dp[i][M]=true;
        }
        for(int i=N-1;i>=0;i--){
            for(int j=M-1;j>=0;j--){

                dp[i][j]=dp[i+1][j];
                if(j+values[i]<dp[0].length){
                    dp[i][j]|=dp[i+1][j+values[i]];
                }
            }
        }
        return dp[index][already];
    }
    boolean getValue(boolean[][] dp,int row,int col){
        if(row<0 || row>=dp.length || col<0 || col>=dp[0].length){
            return false;
        }
        return dp[row][col];
    }
}

```

### 0x3 课后总结

背包问题还没有系统的学习过,不过我的参考文章是基础班-Code07_Knapsack。

不过一般的做法是:我们可以尝试把当前物品放入背包,但是是否会超重还需要判断递归函数的返回值,下面是一份经典的代码:

``` java
public static int getMaxValue(int[] w, int[] v, int bag) {
		return process(w, v, 0, 0, bag);
	}

	// index... 最大价值
	public static int process(int[] w, int[] v, int index, int alreadyW, int bag) {
		if (alreadyW > bag) {
			return -1;
		}
		// 重量没超
		if (index == w.length) {
			return 0;
		}
		int p1 = process(w, v, index + 1, alreadyW, bag);
        //尝试把当前物品放入背包
		int p2next = process(w, v, index + 1, alreadyW + w[index], bag);
		int p2 = -1;
        //判断当前物品放入背包后是否会超重
		if (p2next != -1) {
			p2 = v[index] + p2next;
		}
		return Math.max(p1, p2);

	}
```