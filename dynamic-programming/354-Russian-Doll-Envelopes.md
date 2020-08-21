---
title: 354. 俄罗斯套娃信封问题
mathjax: true
data: 2020-08-21 19:12:48
updated:
tags:
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/russian-doll-envelopes)

---

### 0x0 题目详情

给定一些标记了宽度和高度的信封，宽度和高度以整数对形式 (w, h) 出现。当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。

请计算最多能有多少个信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。

说明:
不允许旋转信封。

。
---

**测试用例:**

>示例:
输入: envelopes = [[5,4],[6,4],[6,7],[2,3]]
输出: 3 
解释: 最多信封的个数为 3, 组合为: [2,3] => [5,4] => [6,7]。

### 0x1 解题思路

这道题有两种做法:动态规划做法和求最长子序列的做法。

**动态规划:**

我们首先需要对信封大小排序,这里按边宽排序即可。然后定义dp数组,`dp[i]`表示第`i`个信封里套了多少个信封。那么为了套最多的信息,我们可以在范围内`[0,i)`遍历dp数组,找到一个索引`j`使得`dp[j]`最大且信封`j`能套到信封`i`即可。

**最长子序列做法:**

不知道还记不记得第300题[最长子序列](300-Longest-Increasing-Subsequence.md),其实这道题跟它有点像。不过这道题的数据是二维的,有点难度。不过我们可以把二维数据降成一维的,然后套娃最大的次数就是最长子序列的长度。那么怎么降呢?

我们可以首先对信封按宽度排序,如果宽度相同,再降序排序高度。为什么要降序呢?因为如果宽度相同,这两个信封是没法套娃的。那么就需要在最长子序列中排除它。例如`[3,4]`、`[3,5]`,按高度升序排会把他们计入最长子序列。所以我们应该按降序排。那么高度相同呢?这就无所谓了,因为我们求的是严格升序的最长子序列。

那么求最长子序列就可以使用耐心排序咯。

### 0x2 代码实现

**动态规划:**
``` java
class Solution {
    public int maxEnvelopes(int[][] envelopes) {
        if(envelopes==null || envelopes.length==0){
            return 0;
        }
        Arrays.sort(envelopes,new Comparator<int[]>(){
            @Override
            public int compare(int[] lhs,int[] rhs){
                return lhs[0]-rhs[0];
            }
        });
        int[] dp=new int[envelopes.length];
        dp[0]=1;
        int result=1;
        for(int i=1;i<envelopes.length;i++){
            dp[i]=1;
            for(int j=0;j<i;j++){
                if(envelopes[i][0]>envelopes[j][0] && envelopes[i][1]>envelopes[j][1]){
                    dp[i]=Math.max(dp[i],dp[j]+1);
                }
            }
            result=Math.max(result,dp[i]);
        }
        return result;

    }
}
```
---

**最长子序列做法:**

``` java
class Solution {
    public int maxEnvelopes(int[][] envelopes) {
        if(envelopes==null || envelopes.length==0){
            return 0;
        }
        Arrays.sort(envelopes,new Comparator<int[]>(){
            @Override
            public int compare(int[] lhs,int[] rhs){
                return lhs[0]==rhs[0]?rhs[1]-lhs[1]:lhs[0]-rhs[0];
            }
        });
        
        int count=0;
        int[][] buckets=new int[envelopes.length][2];
        for(int i=0;i<envelopes.length;i++){
            int left=0;
            int right=count;
            while(left<right){
                int mid=left+(right-left)/2;
                if(buckets[mid][1]>=envelopes[i][1]){
                    right=mid;
                }else{
                    left=mid+1;
                }
            }
            buckets[left]=envelopes[i];
            if(left==count){
                count++;
            }
            
        }
        return count;

    }
}

```

### 0x3 课后总结

求最长子序列一般都是一维的,如果测试用例是多维的,我们需要根据题目特性将多维数据改造成一维。