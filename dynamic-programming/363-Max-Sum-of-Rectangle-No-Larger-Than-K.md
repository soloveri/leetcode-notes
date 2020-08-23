---
title: 363. 矩形区域不超过K的最大数值和
mathjax: true
data: 2020-08-23 10:07:15
updated:
tags:
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/max-sum-of-rectangle-no-larger-than-k)

---

### 0x0 题目详情

>给定一个非空二维矩阵 matrix 和一个整数 k，找到这个矩阵内部不大于 k 的最大矩形和。


>说明：
矩阵内的矩形区域面积必须大于 0。
如果行数远大于列数，你将如何解答呢？

---

**测试用例:**

>示例:
输入: matrix = \[\[1,0,1],\[0,-2,3]], k = 2
输出: 2 
解释: 矩形区域 [[0, 1], [-2, 3]] 的数值和是 2，且 2 是不超过 k 的最大数字（k = 2）。

### 0x1 解题思路

首先我想说,矩阵内的矩形区域面积必须大于0这简直就是在胡说八道,因为测试用例中有`k=-1`,这不是来搞笑的么。

这道题是一道综合题,它结合了:

- [53. 连续子数组的最大和](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)
- [面试题 17.24. 最大子矩阵](https://leetcode-cn.com/problems/max-submatrix-lcci/)
- [560. 和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

首先求连续子数组的和比较简单,不会的可以去参考一哈[题解](../greedy/53-Maximum-Subarray.md)。然后就是最重要的最大子矩阵。

对于求最大子矩阵,我们需要确定的就是**起始行列**和**结束行列**。

试想一下,如果我们的矩阵是一维矩阵`[0,-2,3,1]`。起始行与结束行已经确定了,剩下的就是起始列与结束列。而求起始列与结束列不就是求连续子数组的最大和吗?

那么如果我们的矩阵是二维矩阵:

`[0,-2,3,1]`
`[1,-2,3,0]`

同样在这里,起始行与结束行已经确定了,我们需要做的就是确定起始列与结束列。在这里最大子矩阵就是按列求和后求最大连续子数组和。

如上按列求和后矩阵为`[1,-4,6,1]`,这里最大连续子数组是`[6,1]`。那么对应的列就是第2、第3列。得到最大子矩阵:

`[3,1]`
`[3,0]`

所以我们要做的就是把所有的行两两组合,对每种情况,对列求和,再在这组和中找到最大连续子数组和,这样就确定了起始列与结束列。

最大子矩阵和找到了,我们再来看看题目要求,要求最大子矩阵和不大于k。这里唯一有点难的就是对于连续最大子数组和大于k的情况,我们还需要在这个数组找到最接近k但是不大于k的连续字数和。

这里又会用到第560题的知识点,假设截止到`i`的前缀和为`sum`,那么我们就要在`i`之前找到一个位置`j`,`[0,j]`区间内的子数组和`lb`有`lb>=sum-k`,那么对于区间`[j+1,i]`的子数组和`sum-lb`有`sum-lb<=k`。当然我们需要寻找符合条件且最小的`lb`。

### 0x2 代码实现

因为这道题指定的是行远多于列,那么就可以压缩行,对每一行求和。那么我们在求和的时候,我们可以预先对压缩的行和求一个前缀和。因为这里是压缩行和,所以我们需要使用前缀和矩阵的一行`i`保存矩阵`0~i`列的压缩行和,这是为了赋值方便。什么意思呢?例如:

`[0,-2]`
`[1,-2]`
`[3,1]`
`[3,0]`

对这个矩阵`case`,对于第0列,前缀和矩阵`prefix`使用第`0`行保存`case`矩阵`0~0`列的压缩行和`[0,1,3,3]`,`prefix`使用第`2`行保存的是`case`矩阵`0~1`列的压缩行和,即`[-2.-1,4,3]`。有了这个前缀和矩阵后:

- 如果结束列是`0`,那么直接取`prefix[0]`即可
- 如果想求起始列是`0`,结束列`j`之间的压缩行和,直接取`prefix[j]`即可
- 如果想求起始列是`i`(i!=0),结束列`j`之间的压缩行和,那么取`prefix[j]-prefix[i-1]`的结果即可

``` java
class Solution {
    public int maxSumSubmatrix(int[][] matrix, int k) {
        if(matrix==null || matrix.length==0 || matrix[0].length==0){
            return 0;
        }
        int result=Integer.MIN_VALUE;
        int[][] helpSum=new int[matrix[0].length][matrix.length];
        //int[][] helpSum=new int[matrix.length][matrix[0].length];
        int wide=matrix.length;
        int length=matrix[0].length;
        for(int j=0;j<length;j++){
            for(int i=0;i<wide;i++){
                if(j==0){
                    helpSum[j][i]=matrix[i][j];
                }else{
                    helpSum[j][i]=matrix[i][j]+helpSum[j-1][i];
                }
            }
        }

        for(int i=0;i<length;i++){
            int[] nums=new int[wide];
            for(int j=i;j<length;j++){
                if(j==0){
                    nums=helpSum[0];
                }else if(i==0){
                    nums=helpSum[j];
                }else{
                    for(int q=0;q<wide;q++){
                        nums[q]=helpSum[j][q]-helpSum[i-1][q];
                    }
                }
                //temp为最大连续子数组和
                int temp=longSum(nums);
                if(temp==k){
                    return k;
                }else if(temp<k){
                    result=Math.max(result,temp);
                }else{
                    result=Math.max(result,preSum(nums,k));
                }
            }
        }
        return result;
    }

    //求最大连续子数组和
    private int longSum(int[] nums){
        int result=Integer.MIN_VALUE;
        int pre=0;
        for(int i=0;i<nums.length;i++){
            pre=Math.max(pre+nums[i],nums[i]);
            result=Math.max(pre,result);
        }
        return result;
    }

    //找到一个连续子数组和sum-lb<=k
    private int preSum(int[] nums,int k){
        int result=Integer.MIN_VALUE;
        TreeSet<Integer> sets=new TreeSet<>();
        sets.add(0);
        int sum=0;
        for(int i=0;i<nums.length;i++){
            sum+=nums[i];
            Integer element=0;
            //使用的是TreeSet,不同于560题
            //因为我们要找的sum-k的地板值,也就是最小的且大于等于sum-k的前缀和
            if((element=sets.ceiling(sum-k))!=null){
                result=Math.max(result,sum-element);
            }
            sets.add(sum);
        }
        
        return result;

    }
}

```

### 0x3 课后总结

这道题太综合了,堪比高考最后一道大题,不过又学会了怎么求最大子矩阵。

### 参考文献

1. [最大子矩阵和的问题](http://kubicode.me/2015/06/23/Algorithm/Max-Sum-in-SubMatrix/)

2. [LeetCode 363. 矩形区域不超过 K 的最大数值和（DP+set二分查找）](https://cloud.tencent.com/developer/article/1659822)