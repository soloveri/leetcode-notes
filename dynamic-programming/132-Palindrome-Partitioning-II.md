---
title: 132. 分割回文串 II
mathjax: true
data: 2020-08-12 21:48:25
updated:
tags:
- 递归
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/palindrome-partitioning-ii)

---

### 0x0 题目详情

>给定一个字符串 s，将 s 分割成一些子串，使每个子串都是回文串。
返回符合要求的最少分割次数。

---

**测试用例:**

>示例:
输入: "aab"
输出: 1
解释: 进行一次分割就可将 s 分割成 ["aa","b"] 这样两个回文子串。

### 0x1 解题思路

这道题的暴力递归思路还是容易想的,就是遍历每个分割点,判断对于每个分割点,将整个字符串分割成回文串的次数是多少,然后在所有的次数中取最小即可。思路比较简单,就是过不了测试。

那么动态递归的思路呢,就是我们首先定义一个dp数组,dp[i]表示区间\[0,i)之间的字符分割成回文串需要多少次数,`i`表示左侧字符串的长度。dp[i]肯定和之前的某个下标`j`有关系。如果区间\[j,i)的字符串是一个回文串,那么`dp[i]=dp[j]+1`,这个1就表示把区间\[0,i)分割成\[0,j)、\[j,i)两个部分的次数。那么要求最小的dp[i],那么就让区间\[0,i)之间的下标都有机会成为j,取其中最小的一个dp[i]即可。即dp[i]=min(dp[j]+1 for j in[0,i-1))

还有一点很重要的事情就是如何判断区间\[j,i)是一个回文串。我这里采用的[manacher算法](../string/Palindrome/Manacher.md)。具体判断的时候相关细节需要特殊处理,详见代码注释。

>还有一点非常重要:
对dp数组的初始化,对于每一种长度的i,最差的情况就是`i`左侧的每个字符都需要分割,所以应该将dp[i]初始化为i,而不是0。

### 0x2 代码实现


``` java
class Solution {
    public int minCut(String s) {
        if(s==null || s.length()==0){
            return 0;
        }
        int result=Integer.MAX_VALUE;
        char[] chr=transform(s);
        int[] pArr=new int[chr.length];
        manacher(pArr,chr);
        int[] dp=new int[chr.length];
        //非常重要！！！对数组的初始化
        //最差情况,每个地方都需要切割
        for(int i=1;i<chr.length;i++){
            dp[i]=i;
        }
        for(int len=1;len<chr.length;len++){
            //过滤left指向的字符为#的情况
            if(chr[len]=='#'){
                continue;
            //如果i左侧的字符串已经构成了回文串,那么就不需要判断了,切割次数就是0
            }else if(judge(pArr,1,len)){
                dp[len]=0;
                continue;
            }
            //遍历i左侧的每一个切割点
            for(int j=1;j<len;j++){
                //同样过滤j指向的字符为#的情况
                if(chr[j]=='#'){
                    continue;
                }
                if(judge(pArr,j+2,len)){
                    dp[len]=Math.min(dp[len],dp[j]+1);
                }
            }
        }
        return dp[chr.length-2];

    }

    private char[] transform(String s){
        char[] str=s.toCharArray();
        char[] result=new char[str.length*2+1];
        int index=0;
        for(int i=0;i<result.length;i++){
            if((i&1)==0){
                result[i]='#';
            }else{
                result[i]=str[index];
                index++;
            }
        }
        return result;
    }
    //判断[left,right]是否为回文串
    //以字符串#a#b#a#,这里的right使用的是从left开始需要判断的字符串的长度
    //这里right指向#时并不会对结果产生影响,因为这里已经过滤了left指向#的情况
    //pArr是最大回文半径数组
    private boolean judge(int[] pArr,int left,int right){
        int mid=left+(right-left)/2;
        //中点处的回文半径要能够覆盖left,才说明[left,right]为一个回文串
        return mid-pArr[mid]<left?true:false;
    }
    //chr不可能为null或者长度为0
    private void manacher(int[] pArr,char[] chr){
        //最大回文右边界
        int R=-1;
        //中心,包括自己
        int C=-1;
        for(int i=0;i<chr.length;i++){
            pArr[i]=R>i?Math.min(pArr[2*C-i],R-i):1;
            while(i-pArr[i]>-1 && i+pArr[i]<chr.length){
                if(chr[i-pArr[i]]==chr[i+pArr[i]]){
                    pArr[i]++;
                }else{
                    break;
                }
            }
            if(i+pArr[i]>R){
                R=i+pArr[i];
                C=i;
            }
        }
    }

}
```

### 0x3 课后总结

其实已经有点规律了,对于字符串的题目,要求某种**最值**的话,那么对于一个位置i,想要求dp\[i](dp[i]一般表示长度为i的最值),那么就需要在i左侧(或者右侧,看情况)再找到一个位置j。

并且找到**从dp\[j]递推到dp\[i]的规律**。一般都是区间\[j,i)符合某种要求,然后最值dp\[i]=**min**(dp\[j]+? for j in[0,i)),遍历从[0,i)之间的每一个位置,因为每一个位置都可以作为j。`?`随具体问题而定。

这种规律已经出现了139题[单词分割](139-Word-Break.md)。