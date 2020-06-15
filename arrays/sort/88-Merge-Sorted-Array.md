---
title: 88. 合并两个有序数组
mathjax: true
data: 2020-06-16 00:22:01
updated:
tags:
- [双指针]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/merge-sorted-array/)

### 0x0 题目详情

>给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。
说明:
初始化 nums1 和 nums2 的元素数量分别为 m 和 n 。
你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。

---

**测试用例:**

>示例:
输入:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3
输出: [1,2,2,3,5,6]

### 0x1 解题思路

**思路1**:
先将nums2中的所有元素先拷贝至nums1中，然对对nums1进行排序，这种做法非常简单，没什么好说的。

**思路2**：

在看了评论区后，说可以使用归并排序的思想的做，恍然大悟，随这两个数据进行归并操作。需要注意的是我们必须直接更改nums1中的内容，因为java是值传递，光改引用是没有什么用的。

### 0x2 代码实现

``` java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        if(nums1==null || nums2==null){
            return;
        }
        //拷贝nums1,因为后面我们需要直接对nums1进行操作
        int[] temp=new int[nums1.length];
        for(int i=0;i<m;i++){
            temp[i]=nums1[i];
        }
        int ptr1=0;
        int ptr2=0;
        int index=0;
        while(ptr1<m && ptr2<n){
            if(temp[ptr1]<=nums2[ptr2]){
                nums1[index]=temp[ptr1];
                ptr1++;
                index++;
            }
            else{
                nums1[index]=nums2[ptr2];
                ptr2++;
                index++;
            }
        }
        while(ptr1<m){
            nums1[index]=temp[ptr1];
            index++;
            ptr1++;
        }
        while(ptr2<n){
            nums1[index]=nums2[ptr2];
            index++;
            ptr2++;
        }
    }
}

```

### 0x3 课后总结

代码还是比较简单的，就是没想到可以用归并来做，唉。