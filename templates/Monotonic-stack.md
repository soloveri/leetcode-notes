---
title: 单调栈
mathjax: true
data: 2020-03-25 18:15:04
updated:
tags:
- 单调栈
categories:
- algorithm
---

单调栈可以用来求在一个数组中，位置`i`上的数左右离它**最近**的且比i位置上的**数大(或小)**的第一个数。首先，如果要求最近且比位置`i`上的数大，那么就要维持一个从栈底到栈顶由大到小的状态。如果新进来的数大于栈顶的数，那么栈顶数`x`右边最大的数就是即将新进来的数。`x`左边的最大的数就是栈顶地下的数。(这适用于数组中没有重复的数字)。

如果有重复值，那么栈中就不存放下标了，存放一个数组(保存的是大小相同的元素下标)，每次弹出时，左边最近比它的是它下边队列后的最后一个下标位置的元素。

``` c++ "数组中无重复元素"
//单调栈结构，求解i位置上左右离他最近且比它小的值
struct MonotonicStack {
    stack<int> mono;
    //返回一个二维数组，每一行表示该位置左右离它最近的数，0左1右
    vector<vector<int>>  GetNearLessNoRepeat(vector<int>& nums) {
        if (nums.empty()) {
            return vector<vector<int>> {};
        }
        //一定要先开辟空间，否则无法使用下标
        vector<vector<int>> results(nums.size(),vector<int>(2));
        //处理每个位置上的元素
        for (int i = 0; i < (int)nums.size(); ++i) {
            //单调栈，栈底到栈顶，由小到大
            while (!mono.empty() && nums[mono.top()] > nums[i]) {
                //栈不空，且栈顶元素比即将入栈的元素大，破坏了有大到小的结构(从栈顶到栈底)
                int current = mono.top();
                mono.pop();
                results[current][1] = i;
                results[current][0] = mono.empty() == true ? -1 : mono.top();
            }
            //栈不为空且栈顶元素小于nums[i]
            mono.push(i);
        }
        //如果栈不为空，还需结算处理
        while (!mono.empty()) {
            int popIndex = mono.top();
            mono.pop();
            results[popIndex][1] = -1;
            results[popIndex][0] = mono.empty() == true ? -1 : mono.top();
        }
        return std::move(results);
    }

```


``` c++ "数组有重复元素"
 vector<vector<int>> GetLessRepeat(vector<int>& nums) {
        if (nums.empty()) {
            return vector<vector<int>>{};
        }
        stack<vector<int>> mono;
        vector<vector<int>> results(nums.size(),vector<int>(2));
        
        for (int i = 0; i < (int)nums.size(); ++i) {
            //比是和第一个元素比，求最左时是最后一个元素
            while (!mono.empty() && nums[mono.top().front()] > nums[i]) {
                vector<int> current_sets = mono.top();
                mono.pop();
                //弹栈时需要求每个元素的最左最右值
                for (auto elem : current_sets) {
                    results[elem][1] = i;
                    results[elem][0] = mono.empty() == true ? -1 : mono.top().back();
                }
            }
            //插入时要看是和top相等还是比top大
            if (!mono.empty() && nums[mono.top().back()] == nums[i]) {
                mono.top().push_back(i);
            }
            //比栈顶大或者为空，那就直接压栈就完事了
            else {
                mono.push(vector<int>{i});
            }
        }
        //清零操作
        while (!mono.empty()) {
            auto temp = mono.top();
            mono.pop();
            for (auto elem : temp) {
                //如果栈不为空，可以先取栈顶元素，避免多次访问栈
                results[elem][1] = -1;
                results[elem][0] = mono.empty() == true ? -1 : mono.top().back();
            }
        }
        return std::move(results);
    }

```
