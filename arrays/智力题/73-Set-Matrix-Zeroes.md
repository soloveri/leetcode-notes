---
title: 73. 矩阵置零
mathjax: true
data: 2020-05-29 14:20:38
updated:
tags:
- [数组,脑筋急转弯]
categories:
- algorithm
---

[原题链接](https://leetcode-cn.com/problems/set-matrix-zeroes/)

### 0x1 题目详情

>给定一个 m x n 的矩阵，如果一个元素为 0，则将其所在行和列的所有元素都设为 0。请使用原地算法。

---

**测试用例:**

>示例 2:
输入:
[
  [0,1,2,0],
  [3,4,5,2],
  [1,3,1,5]
]
输出:
[
  [0,0,0,0],
  [0,4,5,0],
  [0,3,1,0]
]

### 0x2 解题思路

**思路一:**
这道题的难点在于原地修改。我最开始的想法时首先遍历一遍数组，把行中有0的行数记录下来，把列中有0的列数记录下来，然后再根据记录下来的行数与列数对矩阵进行置零操作。当然这并不满足题意。

**思路二:**

这种方式也是很巧妙奥，使用数组本身来保存哪一行或者哪一列的信息，最后统一置0。

- 首先判断首行首列是否包含0，因为后面含0的行列数据就需要保存在首行或首列中。使用两个变量作为标记
- 判断每个元素是否为0，如果为零，则将当前行的第一个元素置为0，将当前列的第一个元素置0，直到遍历完整个数组
- 进行置零操作，遍历每一个元素，如果当前行首个元素为0，则将当前行所有元素置0，如果当前列首个元素为0，则将当前列所有元素置0
- 根据先前获得标记变量来决定是否要把首行、首列置0

### 0x3 代码实现

**思路1:**

``` java
class Solution {
    public void setZeroes(int[][] matrix) {
        //用来记录包含0的行数
        List<Integer> rows=new ArrayList<>();
        //用来记录包含0的列数
        List<Integer> cols=new ArrayList<>();
        for(int i=0;i<matrix.length;i++){
            for(int j=0;j<matrix[0].length;j++){
                if(matrix[i][j]==0){
                    rows.add(i);
                    cols.add(j);
                }
            }
        }
        for(Integer elem:rows){
            for(int i=0;i<matrix[0].length;i++){
                matrix[elem][i]=0;
            }
        }
        for(Integer elem:cols){
            for(int i=0;i<matrix.length;i++){
                matrix[i][elem]=0;
            }
        }
        return;
    }
}
```

---

**思路2:**

``` java
class Solution {
    public void setZeroes(int[][] matrix) {
        if(matrix== null || matrix.length==0){
            return;
        }
        boolean cowFlag=false;
        boolean rolFlag=false;

        //首先查看第一行、第一列是否包含零，当然这一步是为了判断最后需不需要把首行首列全部置零
        //这步必须先做，否则后面置0操作完成后就分不清是原来就有0还是后面改的了
        for(int i=0;i<matrix[0].length;i++){
            if(matrix[0][i]==0){
                cowFlag=true;
                break;
            }
        }
        for(int i=0;i<matrix.length;i++){
            if(matrix[i][0]==0){
                rolFlag=true;
                break;
            }
        }
        //查找零
        for(int i=1;i<matrix.length;i++){
            for(int j=1;j<matrix[0].length;j++){
                if(matrix[i][j]==0){
                    matrix[0][j]=0;
                    matrix[i][0]=0;
                }
            }
        }
        //置零操作
        for(int i=1;i<matrix.length;i++){
            for(int j=1;j<matrix[0].length;j++){
                if(matrix[i][0]== 0 || matrix[0][j]==0){
                    matrix[i][j]=0;
                }
            }
        }
        //对首行进行操作
        if(cowFlag){
            for(int i=0;i<matrix[0].length;i++){
                matrix[0][i]=0;
            }
        }
        //对首列进行操作
        if(rolFlag){
            for(int i=0;i<matrix.length;i++){
                matrix[i][0]=0;
            }
        }
    }
}
```

### 0x4 课后总结

emmm，智力题。
