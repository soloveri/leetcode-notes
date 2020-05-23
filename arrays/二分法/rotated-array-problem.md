---
title: 旋转排序数组问题
mathjax: true
data: 2020-05-21 14:22:11
updated:
tags:
- [数组,二分法]
categories:
- algorithm
---

这篇文章是四道旋转数组的合集，因为关联性非常强，我就不分开写了。关于旋转排序数组的问题总共有四道：

[33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

[81. 搜索旋转排序数组 II](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)

[153. 寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

[154. 寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

我将从易到难，依次讲解。

## 搜索旋转排序数组(无重复值)

### 0x1 题目详情

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转。
( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。
搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。
你可以假设数组中不存在重复的元素。
你的算法时间复杂度必须是 O(log n) 级别。

---

**测试用例:**

>输入: nums = [4,5,6,7,0,1,2], target = 0
输出: 4

### 0x2 解题思路

**思路1：**

$O(N)$的算法就没什么好说了。仔细瞧瞧这道题，要在一个范围内找到一组值，且有一定的规则，那么可以考虑使用二分法查找，并且题目中也提醒了我们使用二分。

关键是怎么二分。如何决定该抛弃哪一部分呢？

因为数组中是没有重复元素的。可以发现，将数组一分为二时，肯定有一半是有序的。所以我们排除区间的原则就是：

- 如果nums[mid]==target,代表我们找到了目标元素，直接返回结果即可。
- 如果左边区间有序，那么我们就看target是否在左边区间，如果在左边区间，那么我们就排除右边区间，否则排除左边有序的区间。
- 如果右边区间有序，那么我们同样看target在不在右边区间，如果在，那么就排除左侧区间。否则排序右侧区间。

最后我们的指针可能处于两个地方：

- 不在索引区间之内，表示没有找到，直接返回-1.
- 在索引区间，还需要查看我们找到的数是否符合目标

**思路2：**
我刚开始想到的是一定能找到旋转点，这里找到的旋转点是一对数。找到以后，以旋转点为界，左右区间肯定是有序的。然后在这两个区间分别使用二分查找。虽然最后也能找到元素。但是有一个前提：一定在数组内存在旋转点。

但是其实旋转点并不一定在数组内。这种方法不方便推广旋转有序数组的题目。

### 0x3 代码实现

**方法一:**
这里采用二分法实现，时间复杂度为$(logN)$,排除不需要的区间得代码与上述的思路一致。

``` java
public int search(int[] nums, int target) {
    if(nums== null || nums.length==0){
        return -1;
    }

    int result=-1;
    int left=0;
    int right=nums.length;
    //还是可以采取左闭右开的区间
    while(left<right){
        int mid=left+(right-left)/2;//下中
        if(nums[mid]==target){
            return mid;
        }
        else if(nums[left]<nums[mid]){
            if(nums[left]<=target&&target<nums[mid]){
                right=mid;
            }
            else{
                left=mid+1;
            }
        }
        //那就是右半部分有序
        else{
            //这里的right-1不会越界，因为越界的条件是
            //right=0，而left>=0时，left>=right已经停止循环
            if(nums[mid]<target&&target<=nums[right-1]){
                left=mid+1;
            }
            else{
                right=mid;
            }
        }
    }
    if(right<0 || right>=nums.length){
        return -1;
    }
    return result=nums[right]==target?right:-1;
}
```

**方法二:**

先找到旋转点，再在旋转点两侧分别二分查找目标值。时间复杂度虽然也为$(logN)$，但是还是比上面差点意思。

``` java
class Solution {
    //二分搜索 排除法
    public int search(int[] nums, int target) {
        if(nums==null || nums.length==0){
            return -1;
        }
        if(nums.length==1 && nums[0]!=target){
            return -1;
        }
        //首要目标是找到转折点处的索引i,j
        //这里用二分搜索就不使用左闭右开的区间了，不方便，
        //但是中点还是可以按照左闭右开的方式求
        int begin=0;
        int end=nums.length-1;
        int mid=0;
        //当begin到end的距离为2时就代表我们找到了转折点
        while(begin+1<end){
            mid=begin+(end+1-begin)/2;
            if(nums[begin]<nums[mid]){
                begin=mid;
            }
            else{
                end=mid;
            }
        }
        int result=binarySearch(nums,0,begin+1,target);
        result= result!=-1?result:binarySearch(nums,begin+1,nums.length,target);
        return result;
    }

    //搜索区间为左闭右开
    int binarySearch(int[] nums,int begin,int end,int target){
        int result=-1;
        while(begin<end){
            int mid=begin+(end-begin)/2;
            if(nums[mid]==target){
                return mid;
            }
            else if(nums[mid]<target){
                begin=mid+1;
            }
            else{
                end=mid;
            }
        }
        return result;
    }
}

```

利用二分法

## 搜索旋转排序数组(有重复值)

### 0x1 题目详情

>假设按照升序排序的数组在预先未知的某个点上进行了旋转。
( 例如，数组 [0,0,1,2,2,5,6] 可能变为 [2,5,6,0,0,1,2] )。
编写一个函数来判断给定的目标值是否存在于数组中。若存在返回 true，否则返回 false。

---

**测试用例:**
>示例 1:
输入: nums = [2,5,6,0,0,1,2], target = 0
输出: true

### 0x2 解题思路

当数组有重复元素时，我们再将数组一分为二，就很难判定某个区间是否有序了。例如区间`[4,4,4,2,4]`
但是我们可以淘汰掉相同得元素啊，这样不就把问题转化成无重复元素的情况了么，这么简单得办法我愣是想不到，我真是服了。

### 0x3 代码实现

时间复杂度最坏为$O(N)$，因为当不停得需要使用`++`移动指针时，最差需要移动n-1次，即$O(N)$得复杂度。

但是一般情况下就是二分得复杂度$O(logN)$。

``` java
class Solution {
    public boolean search(int[] nums, int target) {
        if(nums== null || nums.length==0){
            return false;
        }
        if(nums.length==0 && nums[0]!=target){
            return false;
        }

        //还是采取左闭右开区间
        int left=0;
        int right=nums.length;
        int mid=0;
        while(left<right){
            mid=left+(right-left)/2;
            if(nums[mid]==target){
                return true;
            }
            //至于是先淘汰左区间还是先淘汰右区间，我倒觉得无所谓
            //淘汰掉左区间相同得元素
            if(nums[left]==nums[mid]){
                left++;
                continue;
            }
            //淘汰右区间相同得元素
            if(nums[mid]==nums[right-1]){
                right--;
                continue;
            }
            else if(nums[left]<nums[mid]){
                if(nums[left]<=target && target<nums[mid]){
                    right=mid;
                }
                else{
                    left=mid+1;
                }
            }
            else{
                if(nums[mid]<target && target<=nums[right-1]){
                    left=mid+1;
                }
                else{
                    right=mid;
                }
            }
        }
        if(right<0 || right>=nums.length){
            return false;
        }
        boolean result=nums[right]==target?true:false;
        return result;

    }
}

```

## 搜索旋转排序数组的最小值(无重复值)

### 0x1 题目详情

>假设按照升序排序的数组在预先未知的某个点上进行了旋转。
( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。
请找出其中最小的元素。
你可以假设数组中不存在重复元素。

---

**测试用例:**
>示例 1:
输入: [3,4,5,1,2]
输出: 1

### 0x2 解题思路

这道题的思路是我从解题区看到的，是对左闭右开区间的一种转换，实在是佩服。

首先数组中的元素是不重复的，所以我们仍能够找到一个完全升序的区间，而最小值只可能出现在一个长度为2的降序区间内(有一种特殊情况需要考虑，就是整个区间都是升序的，这个会在后面提到)，所以我们要做的就是排除完全升序的区间。那么我们是怎么判定完全升序的区间呢？是先判定左边还是先判定右边？

先思考下最小值可能出现的情况(mid为中间指针)：

- 最小值在左半部分,[6,7,1,2,3]:我们最小值正好在中间，我们需要抛弃右半区间，因为抛弃左边会丢失最小值,(其实这里还提供一个不正当的理由：因为我们要找的是最小值，所以我们肯定倾向于往左边找，所以优先选择保留左半部分)有如下关系：
$nums[left]>num[mid],nums[mid]<nums[right]$

- 最小值在右半部分，[3,6,7,1,2]:最小值右半区间，应该丢弃左半区间。此时有如下关系：
$nums[left]<nums[mid],nums[mid]>nums[right]$

- 特殊情况，[1,2,3,6,7]：旋转点不在数组内，可以我们应该丢弃右半区间。此时有如下关系：
$nums[left]<nums[mid],nums[mid]<nums[right]$

所以我们只需关注右半部分，总结如下：

- 当$nums[mid]<nums[right]$时，丢弃右半区间
- 当$nums[mid]>nums[right]$时，丢弃左半区间

为什么不关注左半部分呢？因为情况比上面的复杂。比如$nums[left]<nums[mid]$时，我们不知道该抛弃哪一部分。

同理：如果我们想找最大值，那么就优先保留右半部分，道理是一样的。

### 0x3 代码实现

这里left与right指针更新的方式采用[二分模板](../../templates/binary-search.md)的更新方式，但是我们不能直接套二分模板，仍需要做出一点改变:这里的二分的区间我们需要采取**左闭右闭**的区间。这样每次取的中点就是上中了。

这里的个性化简直巧妙。

- 当数组中只有一个元素时，left和right肯定指向最小值
- 当数组中有两个元素时：
  - $nums[left]<nums[right]$时，$nums[left]=nums[mid]$,此时right会更新mid，指向最小值
  - $nums[left]>nums[right]$时，$nums[left]=nums[mid]$,此时right会更新mid+1，指向最小值

``` java
class Solution {
    public int findMin(int[] nums) {
        if(nums==null || nums.length==0){
            return 0;
        }
        if(nums.length==1){
            return nums[0];
        }
        int left=0;
        int right=nums.length-1;
        while(left<right){
            int mid=left+(right-left)/2;
            if(nums[mid]<nums[right]){
                //这么做是因为即将排序的右侧有序区间中最左边可能就是最小值
                //需要把他保留下来
                right=mid;
            }
            else{
                left=mid+1;
            }
        }
        return nums[right];
    }
}


```

## 搜索旋转排序数组的最小值(有重复值)

### 0x1 题目详情

>假设按照升序排序的数组在预先未知的某个点上进行了旋转。
( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。
请找出其中最小的元素。
注意数组中可能存在重复的元素。

---

**测试用例:**

>示例 1：
输入: [1,3,5]
输出: 1
示例 2：
输入: [2,2,2,0,1]
输出: 0

### 0x2 解题思路

这和没重复值查找最小值的唯一区别就是有了重复元素。由于我们只关注右半部分，所以只要遇到相同元素淘汰就完事了。

淘汰区间选择的标准和无重复元素的情况是一样的。

### 0x3 代码实现

``` java
class Solution {
    public int findMin(int[] nums) {
        if(nums==null || nums.length==0){
            return 0;
        }
        if(nums.length==1){
            return nums[0];
        }
        int left=0;
        int right=nums.length-1;
        while(left<right){
            //淘汰掉相同元素
            int mid=left+(right-left)/2;
            if(nums[mid]==nums[right]){
                right--;
                continue;
            }
            if(nums[mid]>nums[right]){
                left=mid+1;
            }
            else{
                right=mid;
            }
        }
        return nums[right];

    }
}

```

## 总结

这四道题目可以说是很好的练习了二分法的使用。

所以使用场景：

我们呢期望找一个数，且这个数有范围，并且我们能够通过某种规则淘汰调一部分的时候，就可以考虑使用二分法。**使用二分不一样要严格保证数组是有序的。**

思考方向：

1. 首先我们应该进行分类讨论
2. 然后思考在哪种情况下应该保留哪一部分
3. 最后思考我们应该制定何种规则才能实现上述的区间淘汰方法

二分模板并不是能够完全适用，特殊的情况可以做一些适当的定制。