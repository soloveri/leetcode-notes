---
title: 44. 通配符匹配
mathjax: true
data: 2020-07-31 15:39:25
updated:
tags:
- 动态规划
- 递归
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/wildcard-matching/)

---

### 0x0 题目详情

>给定一个字符串 (s) 和一个字符模式 (p) ，实现一个支持 '?' 和 '*' 的通配符匹配。
'?' 可以匹配任何单个字符。
'*' 可以匹配任意字符串（包括空字符串）。
两个字符串**完全匹配**才算匹配成功。

>说明:
s 可能为空，且只包含从 a-z 的小写字母。
p 可能为空，且只包含从 a-z 的小写字母，以及字符 ? 和 *

### 0x1 解题思路

这道题就是第10题[正则表达式匹配](10-Regular-Expression-Matching.md)的简单版本,你会第10题,这道题必会。二者的区别就是后者的`*`字符能够匹配零个或多个字符。

同样维护两个指针`strIndex`和`patternIndex`分别指向被匹配串和匹配串,匹配规则如下:

- 如果当前字符匹配成功或者`patternIndex`指向的字符为`?`,说明当前字符匹配成功,直接同时移动两个指针匹配下一个字符
- 如果匹配不成功但是`patternIndex`指向的字符为`*`,那么同样会产生三种情况:
  - 使用`*`字符0次,`strIndex+=0`,`patternIndex+=1`
  - 使用`*`字符1次,`strIndex+=1`,`patternIndex+=1`
  - 使用`*`字符多次,`strIndex+=1`,`patternIndex+=0`
- 当前字符匹配失败,并且`patternIndex`指向的字符不为`*`,那么匹配失败,直接返回false

比第10题简单多了。递归版本很容易写出来,最后花点时间改成dp即可。

### 0x2 代码实现

改进后的动态规划版本,时间复杂度为O(N^2):

``` java
class Solution {
    public boolean isMatch(String s, String p) {
        if(s==null && p==null){
            return true;
        }
        if(s.length()==0 && p.length()==0) {
            return true;
        }
        if(s.length()!=0 && (p==null || p.length()==0)){
            return false;
        }
        char[] str=s.toCharArray();
        char[] pattern=p.toCharArray();
        int strIndex=0;
        int patternIndex=0;
        return recur(str,pattern,0,0);
    }
    private boolean recur(char[] str,char[] pattern,int strIndex,int patternIndex){
        boolean[][] dp=new boolean[str.length+1][pattern.length+1];
        int M=str.length;
        int N=pattern.length;
        dp[M][N]=true;
        for(int i=N-1;i>-1;i--){
            if(pattern[i]=='*'){
                dp[M][i]=dp[M][i+1];
            }
        }
        for(int i=M-1;i>-1;i--){
            for(int j=N-1;j>-1;j--){
                if(str[i]==pattern[j]||pattern[j]=='?'){
                    dp[i][j]=dp[i+1][j+1];
                }else if(pattern[j]=='*'){
                    dp[i][j]=dp[i+1][j+1]||dp[i+1][j]||dp[i][j+1];
                }
            }
        }
        return dp[0][0];
    }
}

```

---

原始递归版本:

``` java
class Solution {
    public boolean isMatch(String s, String p) {
        if(s==null && p==null){
            return true;
        }
        if(s.length()==0 && p.length()==0) {
            return true;
        }
        if(s.length()!=0 && (p==null || p.length()==0)){
            return false;
        }
        char[] str=s.toCharArray();
        char[] pattern=p.toCharArray();
        int strIndex=0;
        int patternIndex=0;
        return recur(str,pattern,0,0);
    }
    private boolean recur(char[] str,char[] pattern,int strIndex,int patternIndex){
        
        if(strIndex==str.length && patternIndex==pattern.length){
            return true;
        }
        if(patternIndex==pattern.length){
            return false;
        }
        if(strIndex==str.length){
            //被匹配串已经用完了，查看当前匹配串的字符是否为*,
            //是*才能跳过,否则就是false,因为匹配串无法往后走了
            if(pattern[patternIndex]=='*'){
                return recur(str,pattern,strIndex,patternIndex+1);
            }
            return false;
        }
        if(str[strIndex]==pattern[patternIndex]||pattern[patternIndex]=='?'){
            return recur(str,pattern,strIndex+1,patternIndex+1);
        //对于'*'字符,我们有三种选择,用1次,用多次,或者一次都不用
        }else if(pattern[patternIndex]=='*'){
            return recur(str,pattern,strIndex+1,patternIndex+1)
            || recur(str,pattern,strIndex+1,patternIndex)
            || recur(str,pattern,strIndex,patternIndex+1);
        }
        return false;
    }
}

```
### 0x3 课后总结

正则表达式这种题算是会了。

