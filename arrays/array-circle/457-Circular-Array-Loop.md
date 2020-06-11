---
title: 457. 环形数组循环
mathjax: true
data: 
updated:
tags:
- [数组环]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/circular-array-loop/)

### 0x0 题目详情

>给定一个含有正整数和负整数的环形数组 nums。 如果某个索引中的数 k 为正数，则向前移动 k 个索引。相反，如果是负数 (-k)，则向后移动 k 个索引。因为数组是环形的，所以可以假设最后一个元素的下一个元素是第一个元素，而第一个元素的前一个元素是最后一个元素。
确定 nums 中是否存在循环（或周期）。循环必须在相同的索引处开始和结束并且循环长度 > 1。此外，一个循环中的所有运动都必须沿着同一方向进行。换句话说，一个循环中不能同时包括向前的运动和向后的运动。

---

**测试用例:**

>示例 1：
输入：[2,-1,1,2,2]
输出：true
解释：存在循环，按索引 0 -> 2 -> 3 -> 0 。循环长度为 3 。
示例 2：

>输入：[-1,2]
输出：false
解释：按索引 1 -> 1 -> 1 ... 的运动无法构成循环，因为循环的长度为 1 。根据定义，循环的长度必须大于 1 。
示例 3:

>输入：[-2,1,-1,-2,-2]
输出：false
解释：按索引 1 -> 2 -> 1 -> ... 的运动无法构成循环，因为按索引 1 -> 2 的运动是向前的运动，而按索引 2 -> 1 的运动是向后的运动。一个循环中的所有运动都必须沿着同一方向进行。

>提示：
-1000 ≤ nums[i] ≤ 1000
nums[i] ≠ 0
0 ≤ nums.length ≤ 5000

### 0x1 解题思路

这道题是在数组中找环，当然也可以使用快慢指针。不过这道题有一个方向要求。我们只要找到一个环就可以返回。那么如果我们找了一圈，没找到环，那么我们本次遍历的元素在下一轮找环时还需要在查看一遍吗？当然不需要，因为本次所遍历的元素已经无法构成环，肯定是某个元素出现了问题，那么在下一轮找环中，这些元素肯定也无法构成环，不然如果能构成环在本轮就直接返回true了。

**因为对于同一个可能的环，你正向遍历和反向遍历，遇到的元素都是相同的，并不会因为我们查找的起点不同而构成的环不同**。所以我们可以在查找的环的同时，为我们遇到的元素打上标记，避免下一轮再使用的相同的元素作为起点。

### 0x2 代码实现

``` java
class Solution {
    public boolean circularArrayLoop(int[] nums) {

        if(nums==null || nums.length==0){
            return false;
        }
        int[] visited=new int[nums.length];
        for(int i=0;i<nums.length;i++){
            // Set<Integer> sets=new HashSet<>();
            if(visited[i]==1){
                continue;
            }
            int slow=i;
            int fast=i;
            boolean forward=nums[i]>=0;
            do{
                //这底下注释的代码是错误的，因为在本轮设置后，下一轮if条件必然成立，直接退出，提前终止了查询操作
                // if(visited[slow]==1 || visited[fast]==1){
                //     break;
                // }


                //给我们遇到的元素打上标记，因为slow一定会一步一步遍历fast所遇到元素，所以我们设置一个就行了
                visited[fast]=1;
                slow=getNextIndex(nums,forward,slow);
                fast=getNextIndex(nums,forward,fast);

                if(fast!=-1){
                    //fast指针因为会多走一步，所以也需要打上标记
                    visited[fast]=1;
                    fast=getNextIndex(nums,forward,fast);
                }
            }while(slow!=-1 && fast!=-1 && fast!=slow);
            //slow为-1，那么fast肯定为-1，反过来就不一定了，
            //还是那句话，数组的快慢指针得一步一步走，不能像链表那样直接跳两步，数组得一步一步跳
            if(slow!=-1 && fast==slow){
                return true;
            }

        }
        return false;
    }

    //forward为起点元素规定的方向，如果与当前元素的方向不一致，那么直接终止本轮的查找
    //index为当前元素，我们返回的就是index之后要跳的索引位置
    private int getNextIndex(int[] nums,boolean forward,int index){
        boolean direction=nums[index]>=0;
        if(direction!=forward){
            return -1;
        }
        int nextIndex=(index+nums[index])%nums.length;
        //处理负的步数
        if(nextIndex<0){
            nextIndex+=nums.length;
        }
        if(index==nextIndex){
            nextIndex=-1;
        }
        return nextIndex;


    }
}
```

### 0x3 课后总结

代码中的剪枝操作还是值得借鉴的。这里说明：对于数组中的快慢指针找环操作，快指针得一步一步跳，跳两步。而链表的快指针可以直接通过next直接跳两步，不一样。