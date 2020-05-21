---
title: 31. 下个字典序组合
mathjax: true
data: 2020-05-21 14:22:11
updated:
tags:
- [数组]
categories:
- algorithm
---

### 0x1 题目详情

[原题链接](https://leetcode-cn.com/problems/next-permutation/)

> 实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。
如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。
必须原地修改，只允许使用额外常数空间。

---

**测试用例:**

> 1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1

### 0x2 解题思路

想要获得下一个字典序，那么当前字典序变换到下一个字典序时，所带来的增量必须是最小的。所以：

1. 我们需要从后往前找，找到第一个升序对。下标为[i,j]。此时nums[j]到nums[nums.length-1]一定是降序的。
2. 然后在[j,end)找到第一个大于nums[i]的数nums[k]，然后将nums[k]，与nums[i]交换。此时nums[j]到nums[nums.length-1]仍然保持降序。
3. 最后将[k,end)部分逆序，最后即为得到的答案。
4. 如果没有找到升序对，那么直接逆序整个数组即可。

举个栗子：
对于序列`12654`，找到第一个升序对之后，应该将`2`与`4`交换，得到`14256`，而不是直接交换升序对得到结果`16254`。

下面我们来证明一下：

因为(nums[i],nums[j])是第一个升序对，所以有：$nums[i]<nums[j]$，又因为num[k]是第一个大于nums[i]的值，所以有$nums[k]>nums[i]>=nums[k+1]$及以后的值,因为[j,end)区间是降序的，且有j>=k。

所以有$nums[j]>nums[k-1]>nums[i]>=nums[k+1]$，所以在[j,end)区间数组仍是降序的，此时将[j,end)区间逆序，得到字典序一定是最小的。


### 0x3 代码实现

``` java
class Solution {
    public void nextPermutation(int[] nums) {
        //经典，获取下一个字典序
        //首先从后往前招，找到第一个升序对
        int index;
        for(index=nums.length-2;index>-1;index--){

            if(nums[index]<nums[index+1]){
                break;
            }
        }
        if(index==-1){
            reverse(nums,0,nums.length-1);
            return;
        }
        int k=0;
        //找到[index+1,end)中第一个大于nums[i]的数,下标为k，从后往前
        for(k=nums.length-1;k>index;k--){
            if(nums[k]>nums[index]){
                break;
            }
        }
        int temp=nums[index];
        nums[index]=nums[k];
        nums[k]=temp;
        reverse(nums,index+1,nums.length-1);
        return;
    }

    private void reverse(int[] nums,int begin,int end){
        if(begin>=end){
            return;
        }
        while(begin<end){
            int temp=nums[begin];
            nums[begin]=nums[end];
            nums[end]=temp;
            begin++;
            end--;
        }
    }
}

```

### 0x4 课后总结

这个求下一个字典序的方法实在是妙啊，呜呜呜，为什么我就想不出来呢。不会吧不会吧。

总结：从后往前找，找到第一个升序对[i,j]，然后在j及以后的区间找到第一个比nums[i]大的值，交换完后，逆序[j,end)即可。