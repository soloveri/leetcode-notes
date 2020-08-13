---
title: 140. 单词拆分 II
mathjax: true
data: 2020-08-13 21:30:55
updated:
tags:
- 递归
- 动态规划
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/word-break-ii)

---

### 0x0 题目详情

>给定一个非空字符串 s 和一个包含非空单词列表的字典 wordDict，在字符串中增加空格来构建一个句子，使得句子中所有的单词都在词典中。返回所有这些可能的句子。
说明：
分隔时可以重复使用字典中的单词。
你可以假设字典中没有重复的单词。

---
**测试用例:**

>示例 1：
输入:
s = "catsanddog"
wordDict = ["cat", "cats", "and", "sand", "dog"]
输出:
[
  "cats and dog",
  "cat sand dog"
]

>示例 2：
输入:
s = "pineapplepenapple"
wordDict = ["apple", "pen", "applepen", "pine", "pineapple"]
输出:
[
  "pine apple pen apple",
  "pineapple pen apple",
  "pine applepen apple"
]
解释: 注意你可以重复使用字典中的单词。

>示例 3：
输入:
s = "catsandog"
wordDict = ["cats", "dog", "sand", "and", "cat"]
输出:
[]

### 0x1 解题思路

这道题与[139.单词分割](139-Word-Break.md)相比,我们不仅得判断能够成功分割,而且还得按照从左到右的顺序,把能够分割的单词排列,并且保持与原始字符串的字符顺序相同。

那么这道题首先就需要判断整个字符串能否分割成功,能够完全分割,才需要执行后面的步骤,否则就可以直接返回了。

对于长度为len的字符串,我们需要遍历每一个分割点i(属于区间[1,len]),为每个分割点都创建一个容器,其中存储的是范围在[0,i)的字符串,分割后可能产生的组合,以字符串"pineapplep"为例,当`i`指向最后一个字符`p`时,i对应的容器储存的左侧分割后可能产生的字符串,在这里为"pine apple"和"pineapple"。

在遍历分割点i的时候,我们需要再次遍历范围在[1,i)之间的分割点j,在范围[0,j)之间的字符串能够成功分割的情况下,判断范围在[j,i)的字符串(后文已S表示该字符串)是否在字典中:如果在字典中,那么就判断此时`j`对应的容器是否为空,这里判空的原因是想知道在`j`左侧是否还有已经分割的字符串可以拼接在S的左侧。

例如字符串"pineapple",此时`j`指向字符`a`,`j`对应的容器中存储的字符串为"pine",那么我们就可以将"pine"拼接在字符串"apple"左侧。

最后,如果`j`对应的容器不为空,那么就遍历这个容器,将容器中的每个字符串都拼接在S的左侧,从未不会漏掉答案。

### 0x2 代码实现

``` java
class Solution {
    public List<String> wordBreak(String s, List<String> wordDict) {
        if(s==null || s.length()==0){
            return new ArrayList<String>();
        }
        HashSet<String> dict=new HashSet<>(wordDict);

        boolean[] dp=new boolean[s.length()+1];
        dp[0]=true;
        int length=s.length();
        for(int i=1;i<=length;i++){
            for(int j=0;j<i;j++){
                if(dp[j] && dict.contains(s.substring(j,i))){
                    dp[i]=true;
                    break;
                }
            }
        }
        if(!dp[length]){
            return new ArrayList<String>();
        }
        //不能创建泛型数组
        List<String>[] result=new List[length+1];
        result[0]=new ArrayList<String>();
        for(int i=1;i<=length;i++){
            result[i]=new ArrayList<String>();
            for(int j=0;j<i;j++){
                if(!dp[j]){
                    continue;
                }
                if(!dict.contains(s.substring(j,i))){
                    continue;
                }
                String current=s.substring(j,i);
                //查看当前获取的current前面是否有字串也在字典中,就是判断j前面还有没有已经分割过的字符串
                if(result[j].isEmpty()){
                    result[i].add(current);
                }else{
                    for(String elem:result[j]){
                        result[i].add(new StringBuilder(elem).append(" ").append(current).toString());
                    }
                }
            }
        }
        return result[length];

    }
}

```

### 0x3 课后总结

这种获取分割的字符串的方法简直太奇妙了,厉害厉害!!!