---
title: 139. 单词拆分
mathjax: true
data: 2020-08-12 21:47:08
updated:
tags:
- 递归
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/word-break)

---

### 0x0 题目详情

>给定一个非空字符串 s 和一个包含非空单词列表的字典 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。
说明：
拆分时可以重复使用字典中的单词。
你可以假设字典中没有重复的单词。

---

**测试用例:**
>示例 1：
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。

>示例 2：
输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。注意你可以重复使用字典中的单词。

>示例 3：
输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false

### 0x1 解题思路

这道题的暴力做法思路也很简单,就是遍历目标字符串的每一个可能的切割点,然后分别判断左侧和右侧是否都可以拆分为一个或者多个在字典中出现的单词。

当然从上述递归代码也能够改造成为动态规划,但是时间复杂度太高,结果不是很理想。

我们试试从从[132题](132-Palindrome-Partitioning-II.md)中找到的规律。

我们声明一个dp数组。dp[i]表示区间\[0,i)的字串能否能够转化为一个或多个字典中字符串,i表示左侧的字符串长度。那么dp\[i]肯定和左侧的某个位置j有关系。如果区间[j,i)的字串能够转化为一个在字典中存在的字符串,那么:
>dp[i]=true if(dp[j] == true && str[j,i] is in dic)

那么我们可以遍历区间\[0,i)之间的每一个位置,把每一个位置都当作j,直到dp[i]=true或者遍历完每一个位置j,都无法完美转换成字典中的一个或多个字符串。

dp数组的初始化就比较简单了,dp[0]=true,因为如果区间\[0,i)符合结果,那么就需要判断dp\[0]是否符合结果。这种情况是符合题目要求的,所以dp[0]应该是true,而不是false,否则会漏掉`[0,i)`能够转化为一个或多个字典中的值。

### 0x2 代码实现

``` java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        if(s==null || s.length()==0){
            return false;
        }
        HashSet<String> dic=new HashSet<>(wordDict);
        boolean[] dp=new boolean[s.length()+1];
        dp[0]= true;
        for(int i=1;i<dp.length;i++){
            for(int j=0;j<i;j++){
                if(dp[j] && dic.contains(s.substring(j,i))){
                    dp[i]=true;
                    break;
                }
            }
        }
        return dp[s.length()];
    }
}

```

### 0x3 课后总结

所以对于字符串的动态规划问题,一般有两种思路:

- 递归思路:
  - 从左到右,递归遍历每一个字符,查看当前当前字符是否符合题目的某种规则,然后将符合规则的字串起来直到遍历完每一个字符,最后串起来的子串就是答案。
  - 这种方法不再不是遍历每一个字符,而是遍历字符串中的每一个切割点,所谓的切割点就是能够把一个完整的字符串分为左子串和右子串(一般采用子串的长度作为循环变量)。然后判断左子串和右子串是否符合规则。如何判断,当然是递归地判断辣！
- 动态规划思路:
  - 要么就是从遍历每一个字符的递归方案改造成动态规划
  - 要么就是从遍历每一个切割点的递归方法改造成动态规划,但是这种方法复杂度一般都很高
  - 直接动态规划思路,思路也是迭代的遍历每一个切割点`i`,dp数组的一般含义都是从左侧下标0开始直到i,长度为i的子串是否符合规则。然后再从`i`的左侧区间\[0,i)找到另外一个切割点`j`,找到从dp\[j]递推到dp[j]的规律。**一般都是区间\[j,i)是否符合规则,然后左侧区间\[0,j)是否符合规则的结果就采用dp\[j]代替,如果要找最值,就遍历[0,i)之间的每一个位置,因为我们可以把每个位置都当作j**。