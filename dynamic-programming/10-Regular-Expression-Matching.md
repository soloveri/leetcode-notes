---
title: 10. 正则表达式匹配
mathjax: true
data: 2020-07-30 19:16:36
updated:
tags:
- 动态规划
- 递归
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/regular-expression-matching/)

---

### 0x0 题目详情

给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。

>'.' 匹配任意单个字符
'*' 匹配零个或多个前面的那一个元素
所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。

>说明:
s 可能为空，且只包含从 a-z 的小写字母。
p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。


### 0x1 解题思路

这道题匹配正确的要求是被匹配串能够被完全匹配,而且当成功匹配时,匹配串的字符应该也被使用完成。

例如:
>待匹配串:`bbbba`,匹配串`.*.*b`

返回的结果应该是false,虽然匹配串中的`.*`已经足够匹配出完整的`bbbba`,但是因为匹配串最后有一个字符`b`,导致匹配失败。

我们使用指针`strIndex`指向匹配串中的字符,`patternIndex`指向匹配串中对应的字符,匹配的具体规则如下:

- 如果**当前字符匹配成功**,或者匹配串中对应的字符为`.`,那么就需要查看匹配串中对应的下一个字符是否为`*`:
  - `patternIndex`下一个位置的字符为`*`,那么将有三种情况:
    - 仅使用当前匹配串`patternIndex`对应的字符一次,也就是`*`不起作用,`strIndex+=1`,`patternIndex+=2`
    - 使用当前匹配串`patternIndex`对应的字符多次,`strIndex+=1`,`patternIndex+=0`
    - 不使用当前匹配串`patternIndex`对应的字符串,那么对应的指针变化为`strIndex+=0`,`patternIndex+=2`
  - `patternIndex`下一个位置的字符不为`*`,那么直接将`strIndex`和`patternIndex`各自加1,匹配下一个字符
- 如果当前字符匹配失败,那么需要查看`patternIndex`的下一个位置是否为`*`:
  - 下一个位置如果为`*`,那么在匹配串中直接当前字符和下一字符, `strIndex+=0`,`patternIndex+=2`
  - 下一个位置不为`*`,那么匹配失败,返回false

上述规则是递归的思路,递归写出来了,而且是无后效性的,直接改为动态规划即可。

### 0x2 代码实现

递归版本:

``` java "递归版本"
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
        //匹配完成
        if(strIndex==str.length && patternIndex==pattern.length){
            return true;
        }

        //被匹配串还没被用完,但是匹配串用完了
        if(strIndex!=str.length && patternIndex==pattern.length){
            return false;
        }
        if(strIndex==str.length){
            if(patternIndex+1<pattern.length && pattern[patternIndex+1]=='*'){
                return recur(str,pattern,strIndex,patternIndex+2);
            }else{
                return false;
            }
        }
        
        //当前字符匹配成功
        if(str[strIndex]==pattern[patternIndex] || pattern[patternIndex]=='.'){
            //当前字符匹配成功,并且下一个字符是*
            //那么当匹配串的字符可以用一次,或者用多次
            if(patternIndex+1<pattern.length && pattern[patternIndex+1]=='*'){
                return 
                //继续使用当前字符
                recur(str,pattern,strIndex+1,patternIndex) || 
                //只用一次
                recur(str,pattern,strIndex+1,patternIndex+2)||
                //一次不用
                recur(str,pattern,strIndex,patternIndex+2);

            }
            //当前字符匹配成功,但是下一个字符不是*
            else{
                return recur(str,pattern,strIndex+1,patternIndex+1);
            }
        }
        //当前字符匹配不成功,当时可以跳过当前匹配字符
        else if(patternIndex+1<pattern.length && pattern[patternIndex+1]=='*'){
            return recur(str,pattern,strIndex,patternIndex+2);
        }
        return false;
        
    }
}
```

---

动态规划版本:

``` java "动态规划版本"
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
        for(int i=0;i<M;i++){
            dp[i][N]=false;
        }
        for(int i=N-1;i>-1;i--){
            if((i+1)<N && pattern[i+1]=='*'){
                dp[M][i]=dp[M][i+2];
                
            }else{
                dp[M][i]=false;
            }
        }
        for(int i=M-1;i>-1;i--){
            for(int j=N-1;j>-1;j--){
                if(str[i]==pattern[j]|| pattern[j]=='.'){
                   if((j+1)<N && pattern[j+1]=='*'){
                        dp[i][j]=dp[i+1][j]||dp[i+1][j+2]||dp[i][j+2];
                    }
                    else{
                        dp[i][j]=dp[i+1][j+1];
                    }
                }else if((j+1)<N && pattern[j+1]=='*'){
                    dp[i][j]=dp[i][j+2];
                }else{
                    dp[i][j]=false;
                }
            } 
        }
        return dp[0][0];
        // //匹配完成
        // if(strIndex==str.length && patternIndex==pattern.length){
        //     return true;
        // }

        // //被匹配串还没被用完,但是匹配串用完了
        // if(strIndex!=str.length && patternIndex==pattern.length){
        //     return false;
        // }
        // if(strIndex==str.length){
        //     if(patternIndex+1<pattern.length && pattern[patternIndex+1]=='*'){
        //         return recur(str,pattern,strIndex,patternIndex+2);
        //     }else{
        //         return false;
        //     }
        // }
        
        // //当前字符匹配成功
        // if(str[strIndex]==pattern[patternIndex] || pattern[patternIndex]=='.'){
        //     //当前字符匹配成功,并且下一个字符是*
        //     //那么当匹配串的字符可以用一次,或者用多次
        //     if(patternIndex+1<pattern.length && pattern[patternIndex+1]=='*'){
        //         return 
        //         //继续使用当前字符
        //         recur(str,pattern,strIndex+1,patternIndex) || 
        //         //只用一次
        //         recur(str,pattern,strIndex+1,patternIndex+2)||
        //         //一次不用
        //         recur(str,pattern,strIndex,patternIndex+2);

        //     }
        //     //当前字符匹配成功,但是下一个字符不是*
        //     else{
        //         return recur(str,pattern,strIndex+1,patternIndex+1);
        //     }
        // }
        // //当前字符匹配不成功,当时可以跳过当前匹配字符
        // else if(patternIndex+1<pattern.length && pattern[patternIndex+1]=='*'){
        //     return recur(str,pattern,strIndex,patternIndex+2);
        // }
        // return false;
        
    }
}

```

### 0x3 课后总结

这题还是有点难度的,尤其是下面这种情况:
>待匹配串:`bbbba`,匹配串`.*.*b`

虽然使用`x*`匹配成功后,但是不使用这个组合我是没想到的(x代表任意字符)。