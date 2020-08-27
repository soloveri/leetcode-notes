---
title: 最大值最小化与最小值最大化
mathjax: true
data: 2020-08-27 20:28:46
updated:
tags:
- 二分法
- 最大值最小化
- 最小值最大化
categories:
- algorithm
---

## 前言

最大值最小化与最小值最大化属实容易把人搞晕。这里把相似的题目都放在一起讲解一下。我们以最大值最小化为例,如果我们已经找到了这个最大值的可能区间,那么最右边的最大值是最大的,最左边的最大值是最小的。其中有些最大值是非法的。所以我们可以利用二分法搜索,找到一个可能的最大值`x`,如果该最大值合法,那么就说明`x`的**左侧还有较小的最大值可能符合要求**,所以我们需要收缩右半区间并保留该最大值。如果该最大值非法,那么我们就收缩左区间同时排除该最大值。直到没有区间可以进行搜索。

那么为什么最后收缩区间至无法收缩时最后的值就是答案呢?这是我现在仍旧无法理解的问题。期待高人能够给我讲解一下。总之,解决这种问题有固定的套路:

1. 首先要确定我们要求的值的上限与限
2. 确定`check`函数,来排除非法的值
3. 收缩区间,直到区间不存在元素
4. 根据具体情况酌情确定返回的的值


ok,了解了上述具体思路后,现在来看看几道最经典的例题。

[410. 分割数组的最大值](https://leetcode-cn.com/problems/split-array-largest-sum/)

[1231. 分享巧克力](https://leetcode-cn.com/problems/divide-chocolate/)

[875. 爱吃香蕉的珂珂](https://leetcode-cn.com/problems/koko-eating-bananas/)

[LCP 12. 小张刷题计划](https://leetcode-cn.com/problems/xiao-zhang-shua-ti-ji-hua/)

## 1. 最大值最小化

### 1.1 分割数组的最大和

#### 1.1.1 题目详情

>给定一个非负整数数组和一个整数 m，你需要将这个数组分成 m 个非空的连续子数组。设计一个算法使得这 m 个子数组各自和的最大值最小。

>注意:
数组长度 n 满足以下条件:
1 ≤ n ≤ 1000
1 ≤ m ≤ min(50, n)

---

**测试用例:**

>示例:
输入:
nums = [7,2,5,10,8]
m = 2
输出:
18
解释:
一共有四种方法将nums分割为2个子数组。
其中最好的方式是将其分为[7,2,5] 和 [10,8]，
因为此时这两个子数组各自的和的最大值为18，在所有情况中最小。


#### 1.1.2 解题思路

我们首先需要设一个变量`x`表示我们要求的最大值。然后我们下一步就是确定`x`可能的范围。在这道题中,当`m>=n`,`x`的最小值就是数组中的最大值。当`m==1`时,`x`的最大值就是数组中的元素和。

然后第二步是确定`check`函数。我们会给`check`传入一个`limit`,表示我们当前利用二分法找到的连续子数组的最大和。如果我们对于一个`x`能够产生的**合法子组数**大于`limit`,那么就说明这个`x`是不合理的,需要通过收缩左半区间排除掉。如果`x`能够产生的分组数`<=m`,说明这个`x`还有变小的可能,收缩右半区间。

那么我们以一个什么样的标准来分组呢?这就是解题的精髓了,注意我们`x`表示的是一个子数组的最大可能和。如果产生的某一个子数组和`sum>x`,就表示`sum`这个和太大了,我们可以去除`sum`加的最后一个元素`nums[i]`,这样`sum-nums[i]<=x`,`sum-nums[i]`是一个合法的且子数组和最大的子数组。这样的一个子数组就算一个合法的子数组。当然还有一些代码细节需要注意。


#### 1.1.3 代码实现

``` java
class Solution {
    public int splitArray(int[] nums, int m) {

        if(nums==null || nums.length==0){
            return 0;
        }
        
        int left=Integer.MIN_VALUE;
        int right=0;
        for(int i=0;i<nums.length;i++){
            left=Math.max(left,nums[i]);
            right+=nums[i];
        }
        if(m<2){
            return right;
        }

        while(left<right){
            int mid=left+(right-left)/2;
            if(check(nums,mid,m)){
                right=mid;
            }else{
                left=mid+1;
            }
        }
        return left;


    }
    private boolean check(int[] nums,int sum,int m){
        //这里合法子数组计数变量count初始化为1
        //是为了防止最后一个子数组合法但是没有被计数的情况
        //例如我们当前的sum定为4,对于数组[1,2]
        //当前子数组和3=1+2<4,并不会计数,但是此时遍历已经结束了
        int count=1;
        int current=0;
        for(int i=0;i<nums.length;i++){
            if(current+nums[i]>sum){
                current=0;
                count++;
            }
            
            current+=nums[i];
        }
        //如果合法子数组计算小于等于m,说明当前sum还有减小的空间
        return count<=m;
    }
}
```

因为返回的是最大值,所以当终止搜索时`left`一定指向最小的并且符合要求的连续子数组和,这和最小值最大化恰巧相反。在来一道巩固一下。

### 1.2 爱吃香蕉的珂珂

#### 1.2.1 题目详情

>珂珂喜欢吃香蕉。这里有 N 堆香蕉，第 i 堆中有 piles[i] 根香蕉。警卫已经离开了，将在 H 小时后回来。
珂珂可以决定她吃香蕉的速度 K （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 K 根。如果这堆香蕉少于 K 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。  
珂珂喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。
返回她可以在 H 小时内吃掉所有香蕉的最小速度 K（K 为整数）。


---

**测试用例:**

>示例 1：
输入: piles = [3,6,7,11], H = 8
输出: 4

>示例 2：
输入: piles = [30,11,23,4,20], H = 5
输出: 30

>示例 3：
输入: piles = [30,11,23,4,20], H = 6
输出: 23
 

>提示：
1 <= piles.length <= 10^4
piles.length <= H <= 10^9
1 <= piles\[i] <= 10^9

#### 1.2.2 解题思路

这道题没有明说是最大值的最小化还是最小值的最大化。但是我们可以推断出来,猴子可以一小时全部吃完,但是它希望慢慢吃,直达恰好在规定的小时内把香蕉吃完。很明显,这是最大值的最小化。

那么第一步就是用变量`x`表示一个小时内吃的最多香蕉数,`x`的下界就是一根香蕉,上界就是`sum[piles]`。

第二部确定`check`函数,注意,猴子一次只能选择一堆香蕉,我们要计算猴子把所选的堆吃完要几个小时。同样会传入一个`limit`表示一个小时吃的最多香蕉数,如果吃的总时间`count<=H`,表示猴子还能吃的再慢点,可以让`count`再增大点。也就是让`limit`小点,也就是收缩有区间。与上面的大体操作一摸一样。


#### 1.2.3 代码实现

``` java
class Solution {
    public int minEatingSpeed(int[] piles, int H) {
        if(piles==null || piles.length==0){
            return 0;
        }
        long left=1;
        long right=0;
        for(int i=0;i<piles.length;i++){
            right+=piles[i];
        }
        if(H<2){
            return (int)right;
        }
        if(piles.length>H){
            return 0;
        }
        while(left<right){
            long mid=left+((right-left)>>>1);
            int count=0;
            for(int i=0;i<piles.length;i++){
                // if(piles[i]>limit){
                    count+=(piles[i]+mid-1)/mid;
                // }else{
                    // count++;
                // }
            }
        // return count<=H;
            if(count<=H){
                right=mid;
            }else{
                left=mid+1;
            }
        }
        return (int)left;


    }
    private boolean check(int[] piles,int H,long limit){
        int count=0;
        for(int i=0;i<piles.length;i++){
            // if(piles[i]>limit){
                count+=(piles[i]+limit-1)/limit;
            // }else{
                // count++;
            // }
        }
        return count<=H;

    }
}
```

## 2. 最小值最大化



### 2.1 分享巧克力

#### 2.1.1 题目详情

>你有一大块巧克力，它由一些甜度不完全相同的小块组成。我们用数组 sweetness 来表示每一小块的甜度。
你打算和 K 名朋友一起分享这块巧克力，所以你需要将切割 K 次才能得到 K+1 块，每一块都由一些 连续 的小块组成。
为了表现出你的慷慨，你将会吃掉 总甜度最小 的一块，并将其余几块分给你的朋友们。
请找出一个最佳的切割策略，使得你所分得的巧克力 总甜度最大，并返回这个 最大总甜度。

---

**测试用例:**

>示例 1：
输入：sweetness = [1,2,3,4,5,6,7,8,9], K = 5
输出：6
解释：你可以把巧克力分成 [1,2,3], [4,5], [6], [7], [8], [9]。

>示例 2：
输入：sweetness = [5,6,7,8,9,1,2,3,4], K = 8
输出：1
解释：只有一种办法可以把巧克力分成 9 块。

示例 3：
>输入：sweetness = [1,2,2,1,2,2,1,2,2], K = 2
输出：5
解释：你可以把巧克力分成 [1,2,2], [1,2,2], [1,2,2]。

>提示：
0 <= K < sweetness.length <= 10^4
1 <= sweetness[i] <= 10^5

#### 2.1.2 解题思路

该题目要求每次分得的甜度最小,但是在n种分割策略种我们需要找到一种策略,这个策略的最小甜度是在这个n个最小值里面的最大值。ok,那么就是将最小值最大化。

第一步,我们设一个变量`x`用来表示我们要求的最小值。然后确定`x`的上下限。`x`的下限肯定是`min(sweetness)`。而最大值是`sum(sweetness)/K`。因为跟每个人的甜度都一样已经是将我们能分到的极限。

第二步,确定`check`函数。同样传入一个`limit`表示我们允许的最小甜度。注意`x`表示能够切割出来的最小甜度。所以每一个**合法的分组甜度**总和都应该`>=limit`。我们查看分组总数`count`是否小于`m`。这里为什么不是`<=`?这里感觉有点只可意会不可言传的意思。我尽力说说吧。
因为我们找的是最小值,所以最总答案是靠近左侧的。如果`count<m`,下一步就是要收缩右区间。如果`count<=m`,在收缩右半区间的时候可能错过最终答案。我们要把`count=m`的可能性始终留在左半部分,因为最终`left`一定指向的是第一个`count>m`的位置。不懂这个的,可以去看看我写的[二分法](../../templates/binary-search.md)。


#### 2.1.3 代码实现

``` java
class Solution {
    public int maximizeSweetness(int[] sweetness, int K) {
        if(sweetness==null || sweetness.length==0){
            return 0;
        }
        int left=Integer.MAX_VALUE;
        int right=0;
        for(int i=0;i<sweetness.length;i++){
            left=Math.min(left,sweetness[i]);
            right+=sweetness[i];
        }
        if(K<1){
            return right;
        }
        right=right;
        while(left<right){
            int mid=left+(right-left)/2;
            if(check(sweetness,mid,K+1)){
                right=mid;
            }else{
                left=mid+1;
            }
        }

        //返回的是left-1
        return left-1;

    }
    private boolean check(int[] sweetness,int sweet,int k){
        //这里为什么count又设置为0了呢?
        //因为我们先算和,再判断,不会错误任何一组和
        int count=0;
        int sum=0;
        for(int i=0;i<sweetness.length;i++){
            
            sum+=sweetness[i];
            if(sum>=sweet){
                sum=0;
                count++;
            }
        }
        //是count<k,而不是count<=k
        return count<k;
    }
}

```
#### 2.1.1 题目详情

---

**测试用例:**

#### 2.1.2 解题思路


#### 2.1.3 代码实现
