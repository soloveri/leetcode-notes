---
title: 414. 第三大的数
mathjax: true
data: 2020-06-21 18:16:59
updated:
tags:
- [智力]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/third-maximum-number/)

### 0x0 题目详情

>给定一个非空数组，返回此数组中第三大的数。如果不存在，则返回数组中最大的数。要求算法时间复杂度必须是O(n)。。

---

**测试用例:**

>示例 1:
输入: [3, 2, 1]
输出: 1
解释: 第三大的数是 1.

>示例 2:
输入: [1, 2]
输出: 2
解释: 第三大的数不存在, 所以返回最大的数 2 .

>示例 3:
输入: [2, 2, 3, 1]
输出: 1
解释: 注意，要求返回第三大的数，是指第三大且唯一出现的数。
存在两个值为2的数，它们都排第二。

### 0x1 解题思路

这题看上去挺简单的，好吧，确实挺简单的，但是我竟然瞄了一眼评论区，罪过啊。

**解法1:**
第一种是利用TreeSet存储元素，因为TreeSet自带去重加排序属性。如果TreeSet中的元素个数少于三个，说明第三大的元素不存在，那么只用返回最后一个元素即可。如果TreeSet的元素个数为3个，表示存在第三小元素，直接返回第一个元素即可。

**解法2:**
第二种解法就是维持一个长度为3的数组来存储第一大、第二大、第三大的元素，唯一需要的注意的点是如果我们使用int类型的数组，会出现错误，因为当我们需要更新数组的中元素时，肯定需要跟一个值比较，那么如果我们把标杆值设为Integer.MIN_VALUE就比不出来了，因为存在边界值。所以我们维护一个long数组。代码实现还是非常简单的。

### 0x2 代码实现

``` java "解法1"
class Solution {
    public int thirdMax(int[] nums) {
      if (nums == null || nums.length == 0) throw new RuntimeException("error");

        TreeSet<Integer> set = new TreeSet<>();
        for (Integer elem : nums) {
            set.add(elem);
            if (set.size() > 3) set.remove(set.first());
        }
        return set.size() < 3 ? set.last() : set.first();
    }
}

```

---

``` java "解法2"
class Solution {
    public int thirdMax(int[] nums) {
        if(nums==null || nums.length==0){
            return -1;
        }
        long[] result=new long[3];
        for(int i=0;i<result.length;i++){
            result[i]=Long.MIN_VALUE;

        }

        for(int i=0;i<nums.length;i++){
            if(nums[i]>result[0]){
                result[2]=result[1];
                result[1]=result[0];
                result[0]=nums[i];
            }
            else if(nums[i]<result[0] && nums[i]>result[1]){
                result[2]=result[1];
                result[1]=nums[i];

            }else if(nums[i]>result[2] && nums[i]<result[1]){
                result[2]=nums[i];
            }

        }
        int count=0;
        for(int i=0;i<result.length;i++){
            if(result[i]!=Long.MIN_VALUE){
                count++;
            }
        }
        return count!=3?(int)result[0]:(int)result[2];
    }
}
```

### 0x3 课后总结

这题没啥好说的，就嗯解。