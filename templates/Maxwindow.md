---
title: Maxwindow
mathjax: true
data: 2020-03-25 21:09:38
updated:
tags:
- 滑动窗口
categories:
- algorithm
---
对一个数组，保持一个滑动窗口，求出每个窗口内的最大值。使用一个从左到右由大到小的队列，每次从队列前端取的数就是窗口的最大值。
如果新进来的数破坏了大小，则不断从队尾弹出元素，直至保持了由大到小的属性(相等时也需要弹出)。

``` c++
struct WindowMax
{
    //求窗口的最大值，需要对队列维护一个信息，由大到小
    //遇到相同元素取后进的，因为虽然一样大，但是后进的晚过期
    int L{ -1 }, R{-1};//窗口的左右边界,R是窗口右边界的下一个位置
    //L和R分别窗口左右的上一个和下一个元素
    vector<int> nums;
    deque<int> Max;//存放元素的下标
    WindowMax(vector<int> n):nums(n),L(-1),R(0){}

    //窗口增大
    void AddFromRight() {
        if (R == nums.size()) {
            return;
        }
        while (!Max.empty() && Max.back() <= nums[R]) {
            Max.pop_back();
        }
        Max.push_back(R);
        R++;
    }
    //窗口减小，如果窗口左边界跨过了最大值队列，最大值队列的相对应小标弹出
    void RemoveFromLeft() {
        if (L > R - 1) {//L>=R
            return;
        }
        L++;
        //L是窗口左边界的上一个位置
        if (L == Max.front()) {
            Max.pop_front();
        }
    }
    int GetMax() {
        if (!Max.empty()) {
            return nums[Max.front()];
        }
        return INT_MIN;
    }

};


```

``` c++ "指定窗口大小时，求每个窗口的最大值"
vector<int> getMax(vector<int>& nums, int k) {
    if (nums.empty() || k<1 || k>nums.size()) {
        return vector<int> {};
    }
    //窗口大小为k
    deque<int> Max;
    vector<int> result(nums.size() - k + 1);
    int index{ 0 };
    for (auto i = 0; i < (int)nums.size(); ++i) {
        //处理每个元素，如果破坏了队列的大小结构
        while (!Max.empty() && Max.back() <= nums[i]) {
            //从队尾弹出元素
            Max.pop_back();
        }
        Max.push_back(i);
        //来到一个新位置，需要处理过期窗口的过期元素，处理队列中的过期最大值
        if (Max.front() == i - k) {
            Max.pop_front();
        }
        //窗口形成后，每一轮都会产生一个最大值
        if (i >= k - 1) {
            result[index++] = nums[Max.front()];
        }
    }
    return std::move(result);

}


```