---
title: 5. 最长回文子串
mathjax: true
data: 2020-07-30 19:08:33
updated:
tags:
- 回文串
- 字符串
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/longest-palindromic-substring/)

---

### 0x0 题目详情

>给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

### 0x1 解题思路

**注意注意注意:**

凡是求回文相关的操作,都可以通过[Manacher算法](Manacher.md)以O(N)的复杂度实现。还不知道马拉车算法的同学可以看看链接对应的科普文章。

我们找到最大回文串的中点位置之后,通过对应的回文半径,可以直接从整个数组中提取出对应的回文子数组转为字符串即可。

### 0x2 代码实现

时间复杂度为O(N),比动态规划快。
``` java

class Solution {
    public String longestPalindrome(String s) {
        String result="";
        if(s==null || s.length()==0){
            return result;
        }
        int R=-1;
        int C=-1;
        char[] chr=transform(s);
        int max=Integer.MIN_VALUE;
        int maxIndex=0;
        int[] pArr=new int[chr.length];
        for(int i=0;i<chr.length;i++){
            pArr[i]=R>i?Math.min(R-i,pArr[2*C-i]):1;
            
            while(i+pArr[i]<chr.length && i-pArr[i]>-1 ){
                if(chr[i+pArr[i]]==chr[i-pArr[i]]){
                    pArr[i]++;
                }else{
                    break;
                }
            }

            if(i+pArr[i]>R){
                R=i+pArr[i];
                C=i;
            }
            if(pArr[i]>max){
                max=pArr[i];
                maxIndex=i;
            }
        }
        StringBuilder sb=new StringBuilder();
        //题目出最大回文串的子数组
        for(int i=maxIndex-pArr[maxIndex]+1;i<maxIndex+pArr[maxIndex];i++){
            if(chr[i]=='#'){
                continue;
            }
            sb.append(chr[i]);
        }
        return sb.toString();
        
        

    }
    //转换函数,添加特殊字符,是字符数组的总长度升级为奇数
    private char[] transform(String str){
        char[] chr=str.toCharArray();
        char[] result=new char[str.length()*2+1];
        int index=0;
        for(int i=0;i<result.length;i++){
            result[i]=(i&1)==0?'#':chr[index++];
        }
        return result;
    }
}
```

### 0x3 课后总结

遇到回文相关的题目,三个字->马拉车。