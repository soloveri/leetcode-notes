---
title: 32. 最长有效括号
mathjax: true
data: 2020-07-31 15:25:58
updated:
tags:
- 动态规划
- 递归
- 栈
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/longest-valid-parentheses/)

---

### 0x0 题目详情

>给定一个只包含 '(' 和 ')' 的字符串，找出最长的包含有效括号的子串的长度。

### 0x1 解题思路

#### 栈方法

注意注意:这道题要求的是连续最长子括号的长度,一定得是**连续**的。求一个字符串中括号能否匹配用栈是很简单的。关键是怎么保证连续这个状态。

我刚开始是直接栈中保存左括号或者右括号了,一旦括号匹配,就将结果加2。这种做法只能求出字符串中有效括号的数量。而不是连续的括号数量。所以转而在栈中保存括号对应的下标。基本规则如下:

- 首先要在栈中添加`-1`,这是为了解决`()`这种情况。第一组括号就直接匹配,长度为右括号下标减去栈顶中的下标。也就是`1-(-1)=2`
- 如果当前字符为`(`,那么直接push对应的下标
- 如果当前字符为`)`,首先pop栈顶元素,
  - 如果栈顶为空,那么push当前下标
  - 否则更新结果：`result=Math.max(result,i-stack.peekLast()`。因为i是当前右括号的下标,如果弹出的是左括号,那么新栈顶就是左括号下标j的左边一个`j-1`,二者相减才是这组括号的长度。

从上面的规则可以看出,栈中要么保存一个`)`或者不保存。对于测试用例为`))))`,刚开始栈顶元素为`-1`,遇到第一个`)`,弹出栈顶,计算的结果为0。然后栈为空,会将第二个`)`下标push到栈中。往后都只会保存一个`(`直到遍历结束。

#### 动态规划

由于这道题我实在想不出来递归函数该怎么写,导致最后dp版本也无从下手。下面解析摘抄自:https://leetcode-cn.com/problems/longest-valid-parentheses/solution/dong-tai-gui-hua-si-lu-xiang-jie-c-by-zhanganan042/

对于最优的策略，一定有最后一个元素 s[i]

所以，我们先看第i个位置，这个位置的元素s[i]可能有如下两种情况：
- s[i] == '('
这时，s[i]无法和其之前的元素组成有效的括号对，所以，dp[i] = 0

- s[i] == ')'
这时，需要看其前面对元素来判断是否有有效括号对。

  + 情况1:
    s[i - 1] == `(`
 
    即s[i]和 s[i - 1]组成一对有效括号，有效括号长度新增长度2，i位置对最长有效括号长度为 其之前2个位置的最长括号长度加上当前位置新增的2，我们无需知道i-2位置对字符是否可以组成有效括号对。
    
    那么有：dp[i]=dp[i−2]+2

  + 情况2:
    s[i - 1] == `)`
    这种情况下，如果前面有和s[i]组成有效括号对的字符，即形如`((....))`，这样的话，就要求s[i-1]位置必然是有效的括号对，否则s[i]无法和前面对字符组成有效括号对。</br>
    这时，我们只需要找到和s[i]配对位置，并判断其是否`(`即可。其配对位置为：i−dp[i−1]−1(i-dp[i-1]是s[i-1]的配对位置,再减1就是s[i]的配对位置)。
    
    * 如果：s[i-dp[i-1]-1] == `(`,那么有效括号长度新增长度2，i位置对最长有效括号长度为i-1位置的最长括号长度加上当前位置新增的2，那么有：dp[i]=dp[i−1]+2

    值得注意的是，i−dp[i−1]−1 和 i组成了有效括号对，这将是一段独立的有效括号序列，如果之前的子序列是形如 (...)这种序列，那么当前位置的最长有效括号长度还需要加上这一段。所以：
    dp[i]=dp[i−1] + dp[i−dp[i−1]−2]+2
    >注： 这个在分析时是很容易遗漏的，分析要更细致。我在第一次分析是就遗漏了，提交后，有用例 )()(()))不过，分析后发现是少了这一段。


### 0x2 代码实现

栈版本:

``` java
class Solution {
    public int longestValidParentheses(String s) {
        int result=0;
        if(s==null || s.length()<2){
            return result;
        }
        
        LinkedList<Integer> stack=new LinkedList<>();
        stack.offerLast(-1);
        for(int i=0;i<s.length();i++){
            if(s.charAt(i)=='('){
                stack.offerLast(i);
            }else{
                stack.pollLast();
                if(stack.isEmpty()){
                    stack.offerLast(i);
                }else{
                    result=Math.max(result,i-stack.peekLast());
                } 
            }
        }
        return result;
    }
}
```

---

动态规划版本:

``` c++
class Solution {
public:
    int longestValidParentheses(string s) {
        int size = s.length();
        vector<int> dp(size, 0);

        int maxVal = 0;
        for(int i = 1; i < size; i++) {
            if (s[i] == ')') {
                if (s[i - 1] == '(') {
                    dp[i] = 2;
                    if (i - 2 >= 0) {
                        dp[i] = dp[i] + dp[i - 2];
                    }
                } else if (dp[i - 1] > 0) {
                    if ((i - dp[i - 1] - 1) >= 0 && s[i - dp[i - 1] - 1] == '(') {
                        dp[i] = dp[i - 1] + 2;
                        if ((i - dp[i - 1] - 2) >= 0) {
                            dp[i] = dp[i] + dp[i - dp[i - 1] - 2];
                        }
                    }
                }
            }
            maxVal = max(maxVal, dp[i]);
        }
        return maxVal;
    }
};
```

### 0x3 课后总结

对于测试用例有**连续索引**的题目,如果题目要求最后的结果是**连续的长度**,并且可能会用到栈或队列来求。那么在栈中一般都是**保存元素对应的索引**,而不是真正的元素,因为这样不仅能求出长度,还能保证结果是连续的。