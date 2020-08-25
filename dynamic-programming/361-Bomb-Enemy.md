---
title: 361. 轰炸敌人
mathjax: true
data: 2020-08-23 11:38:46
updated:
tags:
- 动态规划
- 递归
categories:
- algorithm
---


[原题链接](https://leetcode-cn.com/problems/bomb-enemy)

---

### 0x0 题目详情

>想象一下炸弹人游戏，在你面前有一个二维的网格来表示地图，网格中的格子分别被以下三种符号占据：
'W' 表示一堵墙
'E' 表示一个敌人
'0'（数字 0）表示一个空位

![361](images/361_Bomb_Enemy.gif)

>请你计算一个炸弹最多能炸多少敌人。
由于炸弹的威力不足以穿透墙体，炸弹只能炸到同一行和同一列没被墙体挡住的敌人。
注意：你只能把炸弹放在一个空的格子里

---

**测试用例:**

>示例:
输入: [\["0","E","0","0"],["E","0","W","E"],["0","E","0","0"]]
输出: 3 
解释: 对于如下网格

>0 E 0 0 
E 0 W E 
0 E 0 0

假如在位置 (1,1) 放置炸弹的话，可以炸到 3 个敌人

### 0x1 解题思路

这道题暴力递归还是很简单的。首先我们需要遍历每一个位置,看看它能不能放炸弹。如果能放炸弹,我们就遍历该位置的上下左右四个方向。因为递归遍历时需要方向信息,所以递归函数需要额外添加一个方向参数。

>注意:如果有`[E,0,E,0]`这种情况,在最右边放置炸弹是可以炸到最左边的人的

至于动态规划,可以照着上面的递归改改,但是很慢,不知道为什么。

### 0x2 代码实现

自己实现的暴力递归:

``` java
class Solution {
    
    public int maxKilledEnemies(char[][] grid) {
        if(grid==null || grid.length==0 || grid[0].length==0){
            return 0;
        }
        
        int result=0;
        int[][][] dp=new int[4][grid.length][grid[0].length];
        recur(grid,dp);
        for(int i=0;i<grid.length;i++){
            for(int j=0;j<grid[0].length;j++){
                if(grid[i][j]=='W'|| grid[i][j]=='E'){
                    continue;
                }
                result=Math.max(result,recur(grid,i-1,j,0)+recur(grid,i+1,j,1)+recur(grid,i,j-1,2)+recur(grid,i,j+1,3));
                
            }
        }
        return result;

    }
    private void recur(char[][] grid,int row,int col,int flag){
        
        
        if(row<0 || row>=grid.length || col<0 || col>=grid[0].length){
            return 0;
        }
        int result=0;
        if(grid[row][col]=='W'|| grid[row][col]=='0'){
            return 0;
        }
        else{
            switch(flag){
                case 0:
                    result= recur(grid,row-1,col,0)+1;
                    break;
                case 1:
                    result= recur(grid,row+1,col,1)+1;
                    break;
                case 2:
                    result= recur(grid,row,col-1,2)+1;
                    break;
                case 3:
                    result= recur(grid,row,col+1,3)+1;
                    break;
            }
        }
        return result;
    }
}
```

---

动态规划版本:

``` java
class Solution {
    
    public int maxKilledEnemies(char[][] grid) {
        if(grid==null || grid.length==0 || grid[0].length==0){
            return 0;
        }
        
        int result=0;
        int[][][] dp=new int[4][grid.length][grid[0].length];
        recur(grid,dp);
        for(int i=0;i<grid.length;i++){
            for(int j=0;j<grid[0].length;j++){
                if(grid[i][j]=='W'|| grid[i][j]=='E'){
                    continue;
                }
                
                result=Math.max(result,search(dp,0,i-1,j)+search(dp,1,i+1,j)+search(dp,2,i,j-1)+search(dp,3,i,j+1));
            }
        }
        return result;


    }
    private int search(int[][][] dp,int flag,int row,int col){
        if(row<0 ||row>=dp[0].length || col<0 || col>=dp[0][0].length){
            return 0;
        }
        return dp[flag][row][col];
    }
    private void recur(char[][] grid,int[][][] dp){
        
        int m=grid.length;
        int n=grid[0].length;
        for(int j=0;j<n;j++){
            for(int i=0;i<m;i++){
                if(grid[i][j]=='W'){
                    continue;
                }else if(grid[i][j]=='0'){
                    dp[0][i][j]=i-1<0?0:dp[0][i-1][j];  
                }else{
                    dp[0][i][j]=i-1<0?1:dp[0][i-1][j]+1; 
                }
                 
            }
        }
        for(int j=0;j<n;j++){
            for(int i=m-1;i>-1;i--){
                if(grid[i][j]=='W'){
                    continue;
                }
                else if(grid[i][j]=='0'){
                    dp[1][i][j]=i+1>=m?0:dp[1][i+1][j];  
                }else{
                    dp[1][i][j]=i+1>=m?1:dp[1][i+1][j]+1; 
                }
                  
            }
        }
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(grid[i][j]=='W'){
                    continue;
                }
                else if(grid[i][j]=='0'){
                    dp[2][i][j]=j-1<0?0:dp[2][i][j-1];  
                }else{
                    dp[2][i][j]=j-1<0?1:dp[2][i][j-1]+1; 
                }
            }
        }
        for(int i=0;i<m;i++){
            for(int j=n-1;j>-1;j--){
                if(grid[i][j]=='W'){
                    continue;
                }
                else if(grid[i][j]=='0'){
                    dp[3][i][j]=j+1>=n?0:dp[3][i][j+1];  
                }else{
                    dp[3][i][j]=j+1>=n?1:dp[3][i][j+1]+1; 
                }  
            }
        }
        
        
    }
}
```

---

然而我看到别人的暴力求解根本不需要递归:

``` java
class Solution {
  public int maxKilledEnemies(char[][] grid) {
        int max = 0;
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                int count = 0;
                if (grid[i][j] == '0') {
                    // 上下搜索
                    int up = i - 1;
                    int down = i + 1;
                    // 向上搜索
                    while (up >= 0) {
                        if (grid[up][j] == 'W') {
                            break;
                        }
                        if (grid[up][j] == 'E') {
                            count++;
                        }
                        up--;

                    }
                    // 向下搜索
                    while (down < grid.length) {
                        if (grid[down][j] == 'W') {
                            break;
                        }
                        if (grid[down][j] == 'E') {
                            count++;
                        }
                        down++;
                    }
                    // 左右搜索
                    int left = j - 1;
                    int right = j + 1;
                    while (left >= 0) {
                        if (grid[i][left] == 'W') {
                            break;
                        }
                        if (grid[i][left] == 'E') {
                            count++;
                        }
                        left--;

                    }
                    // 向下搜索
                    while (right < grid[0].length) {
                        if (grid[i][right] == 'W') {
                            break;
                        }
                        if (grid[i][right] == 'E') {
                            count++;
                        }
                        right++;
                    }
                    max = Math.max(count, max);
                }
            }
        }
        return max;
    }
}
```

### 0x3 课后总结

有时候暴力求解不一定需要用到递归奥。    