---
title: 75. 颜色排序
mathjax: true
data: 2020-05-29 14:13:38
updated:
tags:
- [数组,快排,多指针]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/sort-colors/)

### 0x1 题目详情

>给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。
此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。
注意:
不能使用代码库中的排序函数来解决这道题。

---

**测试用例:**
>示例:
输入: [2,0,2,1,1,0]
输出: [0,0,1,1,2,2]

### 0x2 解题思路

这道题呢，就是荷兰国旗问题，参考快速排序的partition过程。快速排序就是多次partition，荷兰国旗就是一次partition。比快排简单多了。

这个partition如果非要给他分个类呢，应该是算作三指针的应用。

首先将数组划分为三个区域：

- 小于1的区域
- 等于1的区域
- 大于1的区域

我们可以使用left指针指向小于区域的下一个位置，用right指针指向right区域的上一个位置。用index指针来遍历数组，当index>right，这就表示我们把数组元素都处理完了。

使用多指针的核心步骤是什么？考虑在什么情况下移动指针，以及需要定义一个标准来完成上述的移动操作。

- 如果nums[index]<1，那么就可以和小于区域的下一个元素交换，这里交换的过来的元素，要么是nums[index]本身，要么是和nums[index]相等的元素，**index和left之间不可能出现比nums[index]小的数**。交换完成后，移动指针，left++，index++
- 如果nums[index]==1,直接移动index
- 如果nums[index]>1,需要和大于区域的上一个元素交换，并且right--,为什么不移动index指针呢？因为交换过来的元素我们还没有查看过。

### 0x3 代码实现

``` java
class Solution {
    public void sortColors(int[] nums) {
        //很简单的一次partition操作
        int index=0;
        int left=0;
        int right=nums.length-1;
        while(index<=right){
            if(nums[index]==1){
                index++;
            }
            else if(nums[index]<1){
                int temp=nums[left];
                nums[left]=nums[index];
                nums[index]=temp;
                left++;
                index++;
            }
            else{
                int temp=nums[right];
                nums[right]=nums[index];
                nums[index]=temp;
                right--;
            }
        }
        return;
    }
}
```

### 0x4 课后总结

荷兰国旗问题就是快速排序的子集。荷兰国旗已经每次比较的标准确定好了，而快排在每次partition时都需要随机指定标准。(上述所谓的标准就是荷兰国旗中的数字1)。
