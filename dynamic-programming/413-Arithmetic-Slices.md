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

[原题链接](https://leetcode-cn.com/problems/arithmetic-slices)


---

### 0x0 题目详情

>如果一个数列至少有三个元素，并且任意两个相邻元素之差相同，则称该数列为等差数列。
例如，以下数列为等差数列:
1, 3, 5, 7, 9
7, 7, 7, 7
3, -1, -5, -9
以下数列不是等差数列。
1, 1, 2, 5, 7
 

>数组 A 包含 N 个数，且索引从0开始。数组 A 的一个子数组划分为数组 (P, Q)，P 与 Q 是整数且满足 0<=P<Q<N 。
如果满足以下条件，则称子数组(P, Q)为等差数组：
元素 A[P], A[p + 1], ..., A[Q - 1], A[Q] 是等差的。并且 P + 1 < Q 。
函数要返回数组 A 中所有为等差数组的子数组个数。

**测试用例:** 

>示例:
A = [1, 2, 3, 4]
返回: 3, A 中有三个子等差数组: [1, 2, 3], [2, 3, 4] 以及自身 [1, 2, 3, 4]。

### 0x1 解题思路

**思路一:**

我一开始的思路是使用二维数组来记录信息。`dp[i][0]`表示`A[i]`与`A[i-1]`的差。`dp[i][1]`表示连续出现的相等的差的次数。以`[1,2,3,4]`为例,`dp[3][1]=3`,因为在`4`之前出现了三个连续的且相等的差值`1`。对于大于等于`2`的差值`n`。实际可能的等差数列个数为`(n-1)*(n-2)/2`。这个公式在纸上写写就能推断出来。例如数组`[1,2,3,4,5]`,差值`1`出现了5次。那么可能的等差数列个数等于`3+2+1`。就是一个等差数列求和奥。

**思路二:**

上述的思路实际上非常慢,但是核心思想已经若隐若现了。就是查看连续的相等的差值出现个数。所以我们可以用一个数组记录数组中相邻元素的差值。最后遍历这个差值的思路跟上面差不多。只不过求和公式要变一下。对数组`[1,2,3,4,5]`,生成的差值数组为`[1,1,1,1]`。最后的求和为`3+2+1`,此时`n=4`。所以公式就很明显了奥。`(n)(n-1)/2`。

### 0x2 代码实现

**思路一:**

``` java
class Solution {
    public int numberOfArithmeticSlices(int[] A) {
        if(A==null || A.length==0){
            return 0;
        }
        if(A.length<3){
            return 0;
        }
        int[][] dp=new int[A.length][2];
        dp[1][0]=A[1]-A[0];
        dp[1][1]=2;
        int result=0;
        for(int i=2;i<A.length;i++){
            if(A[i]-A[i-1]==dp[i-1][0]){
                dp[i][0]=dp[i-1][0];
                dp[i][1]=dp[i-1][1]+1;
            }else{
                dp[i][0]=A[i]-A[i-1];
                dp[i][1]=2;
            }
            if(dp[i][0]!=dp[i-1][0] && dp[i-1][1]>=3){
                result+=((dp[i-1][1]-2)*(dp[i-1][1])-1)/2;
                continue;
            }
            if(i==A.length-1 && dp[i][1]>=3){
                result+=((dp[i][1]-2)*(dp[i][1]-1))/2;
            }
        }
        return result;
    }
}
```

---

**思路二:**

``` java
class Solution {
    public int numberOfArithmeticSlices(int[] A) {
        if(A==null || A.length<3){
            return 0;
        }
        int[] sub=new int[A.length-1];
        int index=0;
        for(int i=1;i<A.length;i++){
            sub[index++]=A[i]-A[i-1];
        }
        int pre=sub[0];
        int count=1;
        int result=0;
        for(int i=1;i<sub.length;i++){
            if(sub[i]==pre){ 
                count++;
                continue;
            }
            pre=sub[i];
            result+=count*(count-1)/2;
            count=1;
        }
        result+=count*(count-1)/2;

        return result;

    }
}

```

### 0x3 课后总结

我为什么只能想出最基础的做法呢?可恶啊！！！