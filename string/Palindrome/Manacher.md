---
title: Manacher
mathjax: true
data: 2020-03-24 19:20:31
updated:
tags:
-最长回文字串
categories:
-算法
---

## 前言

manacher算法是用来求最长回文字串的，最长回文字串有一种经典求法：就是在源字符串中的每个字符~~前添加一个额外字符~~后面添加一个字符,并且额外在第一个字符前添加相同的符号(这个额外字符并要求一定是源字符串中没有出现过的字符)。这么做是因为要将字符串数量升级为奇数,因为对于偶数长度的字符串,中轴是虚拟的,最大回文串的长度可能会被错过。

添加完后，依次求每一个位置上的最大回文字串，然后除以2，就是源字符串对应位置的最长回文字串。例如：`122131221`改为`#1#2#2###1#2#2#1#`。

manacher算法中有以下几个概念(适用范围：添加额外字符后的字符串)，以`#1#2#2#1#3#1#2#2#1#`为例：

- 回文直径:一个位置上的最大回文子串长度
- 回文半径:一个位置上的最大回文字串的一半，**要包含中点**，比如字串`#1#2#2#1#`,左`2`位置的回文半径为2，最大半径为`2#`,完整回文串为`#2#`,回文直径为3
- 最大右边界:一个位置上的最大回文字串最右边界位置：比如`#1#2#2#1#`,左`2`的最大右边界下标为4,如果有新位置产生了新的最大右边界，那么需要更新(**这个最大有边界是全局的奥**)
- 最大左边界:与最大右边界类似
- 取得最大右边界时的中心点：仍拿上述字串为例：`#1#2#2#1#`,左`2`的中心点下标为3。

## 算法基本流程

manacher算法求最大回文字串有两种大分支，第二个大分支有三种小情况(下述的对称点是基于最大回文直径的中点计算出的)：

- 第一种情况：当前i位置大于最大右边界R，这时就硬求最长回文字串
- 第二种情况：当前i位置小于最大有边界R，这是有三种情况：
  - `i`位置的对称`i'`的最长回文字串完全在最大左边界`L`的右侧，即完全被包含，这时`i'`的最长回文串半径就是`i`位置的最长回文串半径,假设`i'`的最长回文串前一字符和下一字符为`x`和`y`，x不等于y，所以`i`位置的最长回文串的上一和下一字符为`m`和`n`，m和n肯定不是相等的，因为m等于y，n等于x(因为这两个子串都被包含于[L,R]中)
  - `i`位置的对称`i'`的最长回文串跨过了最大左边界`L`，这时`i`位置的最长回文半径就是右边界`R`到`i`的长度。因为假设L的上一字符为`x`,R的下一字符为`y`，x和y肯定是不相等的，因为x和y都在最大的回文串[L,R]外，说明i为位置的最大回文串半径就是`R-i`的长度
  - `i`位置的对称`i'`的最长回文串恰好在最大左边界`L`，这是`i`位置的最长回文串半径不知道，只能知道最小回文串是右边界`R`到`i`的长度，此时仍需从下标`i`开始向左右两边扩散求最长回文串。

``` c++ 
int MaxLcpsLength(const string& s){
    if(s.empty()){
        return 0;
    }
    string str=GetManacherString(s);
    int C{-1};//最大回文半径的中点
    int R{-1};//最大右边界的下一位置，方便写
    //最长回文半径数组，保存了每个位置的最长回文半径
    vector<int> pArr(str.size());
    int Max=INT_MIN;
    //求每个位置的最长回文子串
    for(int i=0;i<str.size();++i){
        //先求出最小的回文半径,三种情况放一块了，2*C-i是对称的i'位置，因为对称点距离左右两个边界偏移是相同的，R-i是i位置距离R的最大回文半径，二者取小
        //实际上代码并没有体现出最大左边界这一点,不管i'的回文串左边界有没有恰好覆盖最大左边界L,都会走到循环,通过覆盖左边界这种情况会进入循环内部,
        //未超过或者超过L都不会执行循环部分
       pArr[i]=R>i?min(pArr[2*C-i],R-i):1;
       //pArr[i]是i位置的最小回文半径，i+pArr[i]就是下一个待比对的字符
        while(i+pArr[i]<str.size() &&i-pArr[i]>-1){
            //同样也是四合一
            //即当前位置i在最大右边界R外侧,这时就要硬求最大长度
            //当前i对称点i'的最长回文串左边界恰好压在了最大左边界L上,这时候也需要硬求
            //剩下来的两种情况并不会进入循环

            //下面一句代码实际是比对的i左右两侧的字符,而不是i的右边字符和i'的左边字符
            if(str[i+pArrp[i]]==str[i-pArr[i]]){
                pArr[i]++;
            }
            else{
                break;
            }
        }
        //i+pArr[i]实际是最大右边界的下一个字符，比如i为8，pArr[i]=3,长度计算已经包含了8这个点;说明最大右边界实际为10，因为这里算上8，下一位置就是i+pArr[i]=11

        //注意这里的R是最大右边界,而不是最大回文半径
        if(i+pArr[i]>R){
            R=i+pArr[i];
            C=i;
        }
        Max=max(pArr[i],Max);
    }
    //因为Max是包含多余字符的最长回文半径，Max-1就是源字符串的最长回文长度
    return Max-1;
}
```

---

下面是java版本:

``` java
public class Code02_Manacher {

	public static char[] manacherString(String str) {
		char[] charArr = str.toCharArray();
		char[] res = new char[str.length() * 2 + 1];
		int index = 0;
		for (int i = 0; i != res.length; i++) {
			res[i] = (i & 1) == 0 ? '#' : charArr[index++];
		}
		return res;
	}

	public static int maxLcpsLength(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		char[] str = manacherString(s); // 1221 ->  #1#2#2#1#
		int[] pArr = new int[str.length]; // 回文半径数组
		int C = -1; // 中心
		int R = -1; // 回文右边界的再往右一个位置    最右的有效区是R-1位置
		int max = Integer.MIN_VALUE; // 扩出来的最大值
		for (int i = 0; i != str.length; i++) { // 每一个位置都求回文半径
			// i至少的回文区域，先给pArr[i]
			pArr[i] = R > i ? Math.min(pArr[2 * C - i], R - i) : 1;
			while (i + pArr[i] < str.length && i - pArr[i] > -1) {
				if (str[i + pArr[i]] == str[i - pArr[i]])
					pArr[i]++;
				else {
					break;
				}
			}
			//注意,这里R是最大右边界,而不是最大回文半径
			if (i + pArr[i] > R) {
				R = i + pArr[i];
				C = i;
			}
			max = Math.max(max, pArr[i]);
		}
		return max - 1;
	}

	public static void main(String[] args) {
		String str1 = "abc1234321ab";
		System.out.println(maxLcpsLength(str1));
	}
}

```