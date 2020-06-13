---
title: 442. 数组中重复的数据
mathjax: true
data: 
updated:
tags:
- [自定义哈希表]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/find-all-duplicates-in-an-array/)

### 0x0 题目详情

给定一个整数数组 a，其中1 ≤ a[i] ≤ n （n为数组长度）, 其中有些元素出现两次而其他元素出现一次。

找到所有出现两次的元素。

你可以不用到任何额外空间并在O(n)时间复杂度内解决这个问题吗？

---

**测试用例:**

>示例：
输入:
[4,3,2,7,8,2,3,1]
输出:
[2,3]

### 0x1 解题思路

**解法1：**
这道题有一种非常骚的解法。对于每个元素index，我们将nums[index]的元素变为其的相反数，这样如果有相同的index，那么在设置nums[index]的相反数前，nums[index]一定为负数。所以我们在设置只需要检查一下数据的正负即可，如果为负，则重复的元素为index。

**解法2:**

第二种方法就是抽屉原理，一个萝卜一个坑，8个萝卜要放在7个坑里，则至少有1个坑里至少有2个萝卜，如果num[i]-1!=i，表示nums[i]就是重复元素之一。那么我们如何把正确的萝卜放到属于他自己的坑里面呢？我也不知道。具体看代码实现吧，但是我又找不出反例，代码是抄别人的。这种也算是自定义哈希表吧，哈希规则就是nums[i]应该刚在索引i-1的位置上。

### 0x2 代码实现

``` java "自定义哈希"
class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> result=new ArrayList<>();
        int index=0;
        for(int elem:nums){
            index=Math.abs(elem)-1;
            //检查nums[index]是否为负
            if(nums[index]<0){
                result.add(index+1);
            }else{
                nums[index]=-nums[index];
            }
        }
        return result;
    }
}
```

---

``` java "抽屉原理"
import java.util.ArrayList;
import java.util.List;

public class Solution {

    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> res = new ArrayList<>();
        int len = nums.length;
        if (len == 0) {
            return res;
        }
        for (int i = 0; i < len; i++) {
            while (nums[nums[i] - 1] != nums[i]) {
                swap(nums, i, nums[i] - 1);
            }
        }
        for (int i = 0; i < len; i++) {
            if (nums[i] - 1 != i) {
                res.add(nums[i]);
            }
        }
        return res;
    }

    private void swap(int[] nums, int index1, int index2) {
        if (index1 == index2) {
            return;
        }
        nums[index1] = nums[index1] ^ nums[index2];
        nums[index2] = nums[index1] ^ nums[index2];
        nums[index1] = nums[index1] ^ nums[index2];
    }
}
```

### 0x3 课后总结

这个自定义哈希简直太厉害了，我佛辣。还有这个抽屉原理的解法，emmm，虽然我没办法解释，但是它就是正确的，它的做法似乎是不断把重复的元素往最后面扔，反正也挺巧。