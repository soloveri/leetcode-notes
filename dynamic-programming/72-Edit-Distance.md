---
title: 72. 编辑距离
mathjax: true
data: 2020-08-03 19:20:13
updated:
tags:
- 动态规划
- 递归
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/edit-distance/)

---

### 0x0 题目详情

>给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。
你可以对一个单词进行如下三种操作：
插入一个字符
删除一个字符
替换一个字符
 
---

**测试用例:**

>示例 1：
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')

>示例 2：
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')

### 0x1 解题思路

好题啊好题。算是一道比较经典的题目了。求最小编辑距离也是从比对字符的角度开始的。我们需要**从后往前**求最小编辑距离。我们需要设立两个参数`m`与`n`:分别表示第一个字符串和第二个字符串还需要编辑的距离。

- 如果下标`m`与`n`所指向的字符相等:例如`xyg`-->`efg`:因为最后一个字符是相等的,那么就不需要编辑。那么`xyg`-->`efg`的最小编辑距离等价于`xy`-->`ef`(后文将使用===表示最小编辑距离的等价,并且这条法则代号法则1)
- 如果下标`m`与`n`所指向的字符是不相等的:例如`xyz`-->`efg`那么我们就需要通过三种操作变换一下求出编辑距离:
  - 替换操作:将`xyz`中的最后一个字符替换为`g`,那么`xyz`-->`efg`===`xyg`-->`efg`+1。又由第一种情况可以推算至如下结果:`xyg`-->`efg`+1===`xy`--->`ef`+1
  - 删除操作:将`xyz`中的最后一个字符删除,那么有`xyz`-->`efg`===`xy`-->`efg`+1
  - 增加操作:在`xyz`最后一个位置之后添加字符`g`(为什么添加g?因为添加别的字符又需要通过替换操作增加一次编辑距离),那么有`xyz`-->`efg`===`xyzg`-->`efg`+1,那么又通过法则1有:`xyzg`-->`efg`+1===`xyz`-->`ef`+1
> 注:上述三种情况都有可能发生,所以最后的结果三者之中取最小
- 当字符串1还需要编辑的字符数m为零时,那么n就是还需要的编辑次数。同理,当字符串2还需要编辑的字符数n为0时,那么m就是还需要的编辑次数(通过添加操作)。

通过上述规则,递归函数就很容易写出来了。随机递归状态变化的参数`m`和`n`分别表示字符串1和字符串2还需要编辑的字符数。返回值为最小编辑次数。递归出口就是`m==0`或者`n==0`
### 0x2 代码实现

递归版本:

``` java
class Solution {
    public int minDistance(String word1, String word2) {
        if(word1==null || word2==null){
            throw new IllegalArgumentException("strings can not be null!");
        }
        char[] chr1=word1.toCharArray();
        char[] chr2=word2.toCharArray();
        return recur(chr1,chr2,chr1.length,chr2.length);
    }

    private int recur(char[] chr1,char[] chr2,int m,int n){
        if(m==0){
            return n;
        }else if(n==0){
            return m;
        }else if(chr1[m-1]==chr2[n-1]){
            return recur(chr1,chr2,m-1,n-1);
        }
        else{
            int first=recur(chr1,chr2,m,n-1)+1;
            int second=recur(chr1,chr2,m-1,n-1)+1;
            int thrid=recur(chr1,chr2,m-1,n)+1;
            return Math.min(first,Math.min(second,thrid));
        }
    }
}
```
---

动态规划版本:进行设置已知答案时,有一点需要注意。比如当`m`等于0时,此时最小的编辑次数是本次递归传进来的参数`n`。而不是整个字符串2的长度n。每个递归函数传进去的`n`都不相同。所以当`m==0`时,不能将第一行的数据`dp[0][i]`都设置为n。

``` java
class Solution {
    public int minDistance(String word1, String word2) {
        if(word1==null || word2==null){
            throw new IllegalArgumentException("strings can not be null!");
        }
        char[] chr1=word1.toCharArray();
        char[] chr2=word2.toCharArray();
        return recur(chr1,chr2,chr1.length,chr2.length);
    }

    private int recur(char[] chr1,char[] chr2,int m,int n){
        int[][] dp=new int[m+1][n+1];
        //这里设置已知答案时要注意,要设置成每次递归传进去的值
        for(int i=0;i<=n;i++){
            dp[0][i]=i;
        }
        for(int i=0;i<=m;i++){
            dp[i][0]=i;
        }
        for(int i=1;i<=m;i++){
            for(int j=1;j<=n;j++){
                
                if(chr1[i-1]==chr2[j-1]){
                    dp[i][j]=dp[i-1][j-1];
                }else{
                    dp[i][j]=dp[i][j-1]+1;
                    dp[i][j]=Math.min(dp[i][j],dp[i-1][j-1]+1);
                    dp[i][j]=Math.min(dp[i][j],dp[i-1][j]+1);
                }
            }
        }
        return dp[m][n];
    }
}
```

### 0x3 课后总结

经典题目:最小编辑距离!!!

