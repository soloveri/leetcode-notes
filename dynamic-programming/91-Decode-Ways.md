---
title: 91. 解码方法
mathjax: true
data: 2020-08-12 23:44:59
updated:
tags:
- 递归
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/decode-ways/)

---

### 0x0 题目详情

>一条包含字母 A-Z 的消息通过以下方式进行了编码：
'A' -> 1
'B' -> 2
...
'Z' -> 26
给定一个只包含数字的非空字符串，请计算解码方法的总数。

---

**测试用例:**

>示例 1:
输入: "12"
输出: 2
解释: 它可以解码为 "AB"（1 2）或者 "L"（12）。

>示例 2:
输入: "226"
输出: 3
解释: 它可以解码为 "BZ" (2 26), "VF" (22 6), 或者 "BBF" (2 2 6) 

### 0x1 解题思路

这道题思路还是很简单的:

- 对于字符`1`,是否使用后面的字符可以分为两种情况
- 对于字符`2`,能够使用下一个字符`c`的前提是`0`<=`c`<=`6`
- 对于字符`0`,我刚开始思考了很久,我思考的方向是如果下一个字符为`0`，那么就需要提前处理,这样我们永远都不需要处理字符`0`,然而这样失败了,情况过于复杂。后来我换了种思路,如果当前处理的字符为`0`,那么就表示当前的字符无法成功转码,直接返回0即可(函数返回值表示的是能够成功转码的次数)

### 0x2 代码实现

代码也分为两种,暴力递归和动态规划。这道题还是比较常规,动态规划的代码可以通过递归的代码改出来。

**暴力递归版本:**

``` java
class Solution {
    public int numDecodings(String s) {
        char[] str=s.toCharArray();
        if(str[0]=='0'){
            return 0;
        }
        return recur(str,0);
    }

    private int recur(char[] str,int index){
        int result=0;
        if(index==str.length){
            return 1;
        }
        //不可能和后面的字符产生组合
        if(str[index]=='0'){
            return 0;
        }
        else if(str[index]>'2' && str[index]<='9'){
            return recur(str,index+1);
        }
        else if(str[index]=='1'){
            result+=recur(str,index+1);
            if(index+1<str.length){
                result+=recur(str,index+2);
            }
        }else{
            result+=recur(str,index+1);
            if(index+1<str.length && str[index+1]>='0' && str[index+1]<='6'){
                result+=recur(str,index+2);
            }
        }
        return result;
    }
}

```

---

**动态规划版本:**

``` java
class Solution {
    public int numDecodings(String s) {
        char[] str=s.toCharArray();
        if(str[0]=='0'){
            return 0;
        }
        return recur(str,0);
    }

    private int recur(char[] str,int index){
        int[] dp=new int[str.length+1];
        int length=str.length;
        dp[length]=1;
        for(int i=length-1;i>-1;i--){
            if(str[i]=='0'){
                dp[i]=0;
            }else if(str[i]>'2' && str[i]<='9'){
                dp[i]=dp[i+1];
            }else if(str[i]=='1'){
                dp[i]=dp[i+1];
                if(i+1<length){
                    dp[i]+=dp[i+2];
                }
            }else{
                dp[i]=dp[i+1];
                if(i+1<length && str[i+1]>='0' && str[i+1]<='6'){
                    dp[i]+=dp[i+2];
                }
            }
        }
        return dp[0];
    }
}

```

### 0x3 课后总结

比较常规的动态规划题目,只不过要注意字符`0`的处理。