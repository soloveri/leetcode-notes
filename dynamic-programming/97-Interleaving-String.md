---
title: 97. 交错字符串
mathjax: true
data: 2020-08-06 19:07:20
updated:
tags:
- 递归
- 动态规划
- 字符串
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/interleaving-string/)

---

### 0x0 题目详情

>给定三个字符串 s1, s2, s3, 验证 s3 是否是由 s1 和 s2 交错组成的。

---

**测试用例:**

>示例 1：
输入：s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
输出：true

>示例 2：
输入：s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"
输出：false

### 0x1 解题思路

前提条件就是`s1`的长度与`s2`的长度之和等于`s3`的长度。否则不可能匹配成功。

然后可以维护三个指针`i`、`j`、`k`分别指向字符串`s1`、`s2`、`s3`。递归的选择`s1`、`s2`中的字符来匹配`s3`中的字符:

- 如果`i`指向的字符匹配成功了`k`指向的字符,那么就选择`i`指向字符,`i++`,`k++`
- 如果`j`指向的字符匹配成功了`k`指向的字符,那么就选择`j`指向字符,`j++`,`k++`

两种情况有一个返回true就可以直接返回了,或者两种情况都遍历完一起返回也没有问题。当然前者速度可能快一点点。如果`k`走到了尽头,也就是s3的长度,那么就说明匹配成功。

这是最普通的做法,需要三个指针,我们可以将`k`这个指针使用`i+j`优化掉,`i+j`表示`s3`中已经匹配的长度,dp数组由三维转向了二维。

---

**Extension:**

这道题还可以通过第64题[最小路径和](64-Minimum-Path-Sum.md)的思路求解,这里借[一张图](https://leetcode-cn.com/problems/interleaving-string/solution/lei-si-lu-jing-wen-ti-zhao-zhun-zhuang-tai-fang-ch/):

![mininum-path-sum](images/97.png)

可以看到,就是在二维数组中找到一条路径符合目标字符串。每次走的方向只能选择向下或者向右。

于是可定义 boolean[][] dp ，dp[i][j] 代表 s1 前 i 个字符与 s2 前 j 个字符拼接成 s3 的 i+j 字符，也就是存在目标路径能够到达 i ,j ；
状态方程：

边界 1：dp[0][0] = true;
边界 2：if i=0 : dp[0]dp[j] = s2[0-j) equals s3[0,j) 遇到 false 后面可以直接省略
边界 3：if j=0 : dp[i]dp[0] = s1[0-i) equals s3[0,i) 遇到 false 后面可以直接省略

其他情况，到达（i，j）可能由（i-1,j）点向下一步，选择 s1[i-1] 到达；也可能由 （i,j-1） 点向右一步，选择 s2[j-1] 到达；
dp[i,j] = (dp[i-1][j] &&s3[i+j-1] == s1[i-1]) || (dp[i][j-1] && s3[i+j-1] == s2[j-1])

>作者：gousiqi
链接：https://leetcode-cn.com/problems/interleaving-string/solution/lei-si-lu-jing-wen-ti-zhao-zhun-zhuang-tai-fang-ch/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

---

### 0x2 代码实现

最普通的做法,三个指针,注释部分是原始递归代码:

``` java
class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        if(s1==null ||s2==null || s3==null){
            return false;
        }
        if(s1.length()+s2.length() !=s3.length()){
            return false;
        }
        return recur(s1,s2,s3,0,0,0);
    }
    private boolean recur(String s1,String s2,String s3,int i,int j,int k){
        boolean[][][] dp=new boolean[s3.length()+1][s1.length()+1][s2.length()+1];
        int row=s1.length();
        int col=s2.length();
        int high=s3.length();
        for(int x=0;x<=row;x++){
            for(int y=0;y<=col;y++){
                dp[high][x][y]=true;
            }
        }
        // if(k==s3.length()){
        //     return true;
        // }
        for(int z=s3.length()-1;z>-1;z--){
            for(int x=s1.length();x>-1;x--){
                for(int y=s2.length();y>-1;y--){
                    if(x<s1.length() && s1.charAt(x)==s3.charAt(z)){
                        dp[z][x][y]=dp[z+1][x+1][y];
                    }
                    if(dp[z][x][y]){
                        continue;
                    }
                    if(y<s2.length() && s2.charAt(y)==s3.charAt(z)){
                        dp[z][x][y]=dp[z+1][x][y+1];
                    }
                }
            }
        }
        return dp[0][0][0];
        // boolean result=false;
        // if(i<s1.length() && s1.charAt(i)==s3.charAt(k)){
        //     result=recur(s1,s2,s3,i+1,j,k+1);
        // }
        // if(result){
        //     return result;
        // }
        // if(j<s2.length() && s2.charAt(j)==s3.charAt(k)){
        //     result=recur(s1,s2,s3,i,j+1,k+1);
        // }
        // return result;
    }
}
```

---

优化做法,只有两个指针,注释部分也为原始递归代码:

``` java
class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        if(s1==null ||s2==null || s3==null){
            return false;
        }
        if(s1.length()+s2.length() !=s3.length()){
            return false;
        }
        return recur(s1,s2,s3);
    }
    private boolean recur(String s1,String s2,String s3){

        boolean[][] dp=new boolean [s1.length()+1][s2.length()+1];
        int m=s1.length();
        int n=s2.length();
        dp[m][n]=true;
        for(int i=n-1;i>-1;i--){
            if(s2.charAt(i)==s3.charAt(m+i)){
                dp[m][i]=dp[m][i+1];
            }
        }
        for(int i=m-1;i>-1;i--){
            if(s1.charAt(i)==s3.charAt(n+i)){
                dp[i][n]=dp[i+1][n];
            }
        }
        for(int i=m-1;i>-1;i--){
            for(int j=n-1;j>-1;j--){
                if(s1.charAt(i)==s3.charAt(i+j)){
                    dp[i][j]=dp[i+1][j];
                }
                if(!dp[i][j] && s2.charAt(j)==s3.charAt(i+j)){
                    dp[i][j]=dp[i][j+1];
                }
            }
        }
        return dp[0][0];
        // if(i+j==s3.length()){
        //     return true;
        // }
        // boolean result=false;
        // if(i<s1.length() && s1.charAt(i)==s3.charAt(i+j)){
        //     result=recur(s1,s2,s3,i+1,j);
        // }
        // if(result){
        //     return result;
        // }
        // if(j<s2.length() && s2.charAt(j)==s3.charAt(i+j)){
        //     result=recur(s1,s2,s3,i,j+1);
        // }
        // return result;
    }
}
```

### 0x3 课后总结

字符串的暴力递归我怎么就是想不到呢???

更新:
现在对字符串的相关暴力递归好像有点感觉了,一般都是查看每个字符是否符合规则,不符合怎么样,符合怎么样,直到将整个字符串遍历完成,大致都差不多是这个方向。