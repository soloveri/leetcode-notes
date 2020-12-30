---
title: kmp
mathjax: true
data: 2020-03-24 00:09:41
updated:
tags:
- kmp
categories:
- algorithm
---

kmp算法最重要的就是求next数组。

- 如果匹配成功，返回第一个匹配的位置
- 如果匹配失败，返回`-1`。

kmp对谁匹配谁是有严格要求的奥。

``` c++
int kmp(const string& all,const string& sub){
    if(all.empty()||sub.empty()||sub.size()>all.size()){
        return -1;
    }
    vector<int> nexts=GetNextArray(sub);
    int i1{0},i2{0};
    while(i1<(int)all.size() && i2<(int)sub.size()){
        if(all[i1]==sub[i2]){
            ++i1;
            ++i2;
        //字串的指针不能再向前跳了，长串指针加一
        }else if(nexts[i2]== -1){
            ++i1;
        }else{
            i2=nexts[i2];
        }
    }
    //i2走到头表示匹配成功
    return i2==(int)sub.size()?i1-i2:-1;
}

//i位置的最大前缀不包括i位置本身的字符
vector<int> GetNextArray(const string& s){
    if(s.size()==1){
        return vector<int>{-1};
    }
    vector<int> next(s.size());
    next[0]=-1;//使用下标、数据的时候一定首先要考虑能否可用啊，咋记不住呢
    next[1]=0;
    int i{2};
    int cn{0};//cn表示与i-1相匹配的字符位置，也表示i-1位置的最大前缀长度
    //这不就是同一东西么
    //i位置的最大前缀与i-1有关
    while(i<(int)s.size()){
        if(s[i-1]==s[cn]){
            next[i++]=++cn;
        }else if(cn>0){
            cn=next[cn];
        }
        else{
            next[i++]=0;
        }
    }
    return std::move(next);
}
```

求next数组时，i位置的最大前缀与i-1位置的最大前缀有关，因为要靠i-1的最大前缀的下一位置的字符和i-1位置的字符相比较。
时间复杂度为O(N)，N为被匹配串的长度