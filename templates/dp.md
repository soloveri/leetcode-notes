---
title: dp
mathjax: true
data: 2020-03-30 18:56:15
updated:
tags:
- dp
categories:
- algorithm
---

动态规划一般都可以有递归改出来，大体的结构为：递归->记忆化搜索动态规划->严格表结构动态规划，记忆化搜索并不严格要求状态之间的依赖。而且记忆化搜索和严格表在某些问题上的时间复杂度是一样的。

- 记忆化搜索
- 严格表结构(还可以继续演化，更精简)。

如果一个函数，不管调用方是谁，函数返回的结果都一样，那么就可以改成一个无后效性(不管谁调，返回值都一样)的动态规划。

一个从暴力递归改成动态规划的栗子：
> 假设有排成一行的 N 个位置，记为 1~N，N 一定大于或等于 2。开始时机器人在其中的 M 位 置上(M 一定是 1~N 中的一个)，机器人可以往左走或者往右走，如果机器人来到 1 位置， 那 么下一步只能往右来到 2 位置;如果机器人来到 N 位置，那么下一步只能往左来到 N-1 位置。 规定机器人必须走 K 步，最终能来到 P 位置(P 也一定是 1~N 中的一个)的方法有多少种。给 定四个参数 N、M、K、P，返回方法数。

递归版本应该是比较简单的。

``` c++
int MaxMethod(int N,int M,int K,int P){
    return recur(N,M,K,P);
}

//rest是剩余的步数，cur是当前的位置
//总的来说，有两个小限制，当走到1或N位置时，只能往回走
//M是目标位置
int recur(int N,int M,int rest,int cur){
    //如果剩余步数为0
    if(rest == 0){
        return cur==M?1:0;
    }
    //1和N位置特别对待
    if(cur == 1){
        return(N,M,rest-1,2);
    }
    if(cur == N){
        return(N,M,rest-1,N-1);
    }
    return recur(N,M,rest-1,cur-1)+recur(N,M,rest-1,cur+1);
}

```

暴力递归为什么不好？因为重复计算的值太多了。那么我们可以想：搞个缓存存放已经计算的值，再次递归时，如果缓存命中，直接返回缓存中的值就行了。不命中再去计算。这就是所谓的**记忆化搜索**。

那么缓存开多大？
注意递归函数，前两个参数`N`、`M`都是固定的，跟递归没有关系。只有后两个参数与会该变递归函数的状态。所以缓存可以搞的二维表。(缓存表的大小、形式跟可以该变递归状态的参数个数有关)。

``` c++ "记忆化搜索"
int MaxMethod(int N,int M,int K,int P){
    //建立缓存表
    //因为位置P的字面值为1~P,所以需要开辟P+1的长度,因为字面值P
    //同样剩余步数rest的字面值为0~K，所以需要开辟K+1的长度因为字面值K
    vector<vector<int>> cache(K+1,vector<int>(P+1,-1));
    //每个位置的元素都初始化为-1，代表没有计算过
    return recur(N,M,K,P,cache);
}
int recur(int N,int M,int K,int P,vector<vector<int>>& cache){
    //首先看当前位置是否已经计算过了
    if(cache[K][P]!= -1){
        return cache[K][P];
    }
    //没有计算过，该递归递归，但是在返回结果前，需要将计算出的结果保存在cache中
    if(P == 1){
        cache[K][P]=recur(N,M,K-1,2);
    }
    if(P == N){
        cache[K][P]=recur(N,M,K-1,N-1);
    }
    cache[K][P]=recur(N,M,K-1,P-1)+recur(N,M,K-1,P+1);
    return cache[K][P];
}
```

首先要建立一种注册机制，如果当前结果没有计算过，那么就正常计算，否则就注册到cache中，这样就能利用已经算出的结果了。

**在将暴力递归改为记忆化搜索时，一定要记得将计算出的结果注册到cache中哦。**

记忆化搜索不需要探索状态与状态之间的关系，在将记忆化搜索改为严格表动态规划时，就需要考虑状态之间的关系了。那么有多少个状态？状态的个数就是前面记忆化搜索时开辟的cache中的结果个数啊。一种结果代表一种状态。
那么如何建立状态之间的关系表？这就可以根据递归或者记忆化搜索的形式来归纳总结了。

假设已K=4，P=2为例，初始位置为2，走四步，最终位置来4。首先建议一个二维表。
| |1 |2 |3 |4|5|
|--|--|-|-|-|--|
|0|-1|-1|-1|-1|-1|
|1|-1|-1|-1|-1|-1|
|2|-1|-1|-1|-1|-1|
|3|-1|-1|-1|-1|-1|
|4|-1|-1|-1|-1|-1|
横坐标代表能够到达的位置，即1~5。横坐标代表剩余步数，即0~4。要求目标为(4,2)的值。表中的要靠递归形式来推。

**那么这张表有哪些位置是可以直接得到答案的？**
> 通过base case就可以直接得到答案

1. 首先来看第一句递归：当剩余步数为0时，如果当前位置为2，则返回1，否则返回0。那么表的第一行就为：

| |1 |2 |3 |4|5|
|--|--|-|- |-|--|
|0|0 |0 |0 |1 |0 |
|1|-1|-1|-1|-1|-1|
|2|-1|-1|-1|-1|-1|
|3|-1|-1|-1|-1|-1|
|4|-1|-1|-1|-1|-1|

2. 再看第二句递归，当当前位置为1，其结果依赖(rest-1,2)的结果(即右上角的结果)。此时rest不可能为0。
3. 再看第三句递归，当当前位置为N时，其结果依赖(rest-1,N-1)的结果(即左上角的结果)。同样rest也不可能为0。
4. 再看最后一句，中间位置的状态，其结果依赖(rest-1,N-1)和(rest-1,N+1)的结果(即依赖为左上角加右上角)
所以位置的状态依赖已经缕清，可以开始填表了。

|  |1 |2 |3 |4 |5 |
|--|--|- |- |- |- |
|0 |0 |0 |0 |1 |0 |
|1 |0 |0 |1 |0 |1 |
|2 |0 |1 |0 |2 |0 |
|3 |1 |0 |3 |0 |2 |
|4 |0 |4 |-1|5 |0 |
通过上面的依赖关系填表，就可以得到(4,2)的值为4。

**第二个栗子。**
> 给定数组 arr，arr 中所有的值都为正数且不重复。每个值代表一种面值的货币，每种面值 的货币可以使用任意张，再给定一个整数 aim，代表要找的钱数，求组成 aim 的最少货币 数。

首先采用暴力递归的方式

``` c++
int IconCounts(vector<int>& nums,int aim){
    return recur(nums,0,aim);
}
//rest表示剩余的钱数
int recur(vector<int>& nums,int index,int rest){
    if(rest< 0){
        return -1;
    }
    else if(rest == 0){
        return 0;
    }
    if(index==(int)nums.size()){
        return -1;
    }
    //rest>0,且有硬币可选
    //不选当前硬币
    int p1=recur(nums,index+1,rest);
    int p2=reucr(nums,index+1,rest-nums[index]);
    //处理两个结果是否有效
    if(p1 == -1 && p2== -1){
        return -1;
    }
    if(p1 == -1){
        return 1+p2;
    }
    if(p2 == -1){
        return p1;
    }
    //p1和p2都有效
    return std::min(p1,1+p2);
}
```

递归函数中只有两个变量与递归有关，所以需要开辟一个二维数组。
查看递归函数，index字面值范围为`0~num.size()`长度为`nums.size()+1`,aim字面值范围为`0~aim`,长度为`aim+1`。

``` c++
int IconCounts(vector<int>& nums,int aim){
    int N=nums.size();
    //列表示剩余钱数，行表示剩余可选硬币数
    vector<vector<int>> dp(N+1,vector<aim+1,-2>);
    //rest为0的一列值都为0
    //第N+1行值都为-1
    for(auto index=0;index<=N;++index){
        dp[index][0]=0;
    }
    for(auto index=1;index<=aim;++index){
        dp[N][index]=-1;
    }
    //当前位置的值都依赖下一行
    for(auto row=N-1;row>=0;row++){
        for(auto col=1;col<=aim;col++){
            //在确定完basecase时，可以直接使用递归时的代码
            //来填充dp其他位置
            int p1=dp[row+1][rest];
            //int p1=recur(nums,index+1,rest);
            int p2=-1;
            if(rest-nums[row]>-1){
                pw=dp[row+1][rest-nms[row]];
            }
            //int p2=reucr(nums,index+1,rest-nums[index]);
            //处理两个结果是否有效
            if(p1 == -1 && p2== -1){
                dp[row][col]=-1;
                //return -1;
            }
            if(p1 == -1){
                dp[row][col]=1+p2;
                //return 1+p2;
            }
            if(p2 == -1){
                dp[row][col]=p1;
                //return p1;
            }
            //p1和p2都有效
            dp[row][col]=std::min(p1,1+p2);
            //return std::min(p1,1+p2);
        }
    }
    return dp[0][aim];
}
```

可以看到，动态规划代码完全可以由递归形式改过来。
所以将递归改为动态规划可以有以下几个步骤：

- 确定哪些变量能够改变递归状态
- 确定上述变量的范围，从而分配dp数组大小(需要通过变量字面值确定)
- 确定dp数组中已知的答案(即base case)
- 通过递归形式，确定dp中一般位置的变量如何通过已知答案得到
- 确定dp中一般位置的位置的值是怎么来的
- 最后直接通过递归形式改

那么最后应该返回dp数组中的哪个值？
> 返回的值就是初始递归时的可变变量的值(即所想求的答案)。例如上述初始递归为(0,aim),那么最后就返回dp[0][aim]的值即可。

## 范围上的递归如何使用dp？

首先来解释一下什么是范围上的递归。一般情况下的递归题目都是从左不断尝试知道右边界或者相反。而范围上的递归则可能是从左边界尝试或是右边界尝试。不再是固定的方向。
下面以一个零和博弈的问题为例。

> 给定一个整型数组arr，代表数值不同的纸牌排成一条线。玩家A和玩家B依次拿走每张纸牌，规定玩家A先拿，玩家B后拿，但是每个玩家每次只能拿走最左或最右的纸牌，玩家 A 和 玩 家 B 都绝顶聪明。请返回最后获胜者的分数。

这是一个在范围上递归的题目。

``` java
//f是一个先手函数，在[L,R]上进行先手，返回先手情况下的最大值
public static int f(int[] arr,int L,int R){
    if(L==R){
        return arr[L];
    }
    //先手完就只能在[L-1,R]上作后手了，后者同理
    return Math.max(arr[L]+s(arr,L+1,R),arr[R]+s(arr,L,R-1));
}

public static int s(int[] arr,int L,int R){
    if(L==R){
        return 0;
    }
    //在[L,R]上后手表示在[L-1,R]上先手，后者同理，但是在真实情况下，后手只能选最小的，因为先手不会留下大的让后手选
    return Math.min(f(arr,L+1,R),f(arr,L,R-1));
}

public static int GetMax(int[] arr){
    if(arr ==null || arr.length=0){
        return 0;
    }
    return Math.max(f(arr,0,arr.length-1),s(arr,0,arr.length-1));
}

```

上述是一个递归版本，下面讲述如何改为动态规划模型。首先这是一个范围模型。两个递归函数互相调用。所以dp表应该有两张。并且L、R的范围都为`[0,arr.length-1]`,所以是一个正方形。以5X5为例。arr为[3,4,100,1,2]。初始模型如下：

先手表：
|  |0 |1 |2 |3 |4 |
|--|--|- |- |- |- |
|0 |0 |0 |0 |0 |0 |
|1 |0 |0 |0 |0 |0 |
|2 |0 |0 |0 |0 |0 |
|3 |0 |0 |0 |0 |0 |
|4 |0 |0 |0 |0 |0 |

后手表：
|  |0 |1 |2 |3 |4 |
|--|--|- |- |- |- |
|0 |0 |0 |0 |0 |0 |
|1 |0 |0 |0 |0 |0 |
|2 |0 |0 |0 |0 |0 |
|3 |0 |0 |0 |0 |0 |
|4 |0 |0 |0 |0 |0 |

首先使用base case填充已经知道的答案。一直，L不可能大于R，所以对角线下方都为0。填充后的dp表如下：

先手表：
|  |0 |1 |2 |3 |4 |
|--|--|- |- |- |- |
|0 |3 |0 |0 |0 |0 |
|1 |0 |4 |0 |0 |0 |
|2 |0 |0 |100 |0 |0 |
|3 |0 |0 |0 |1 |0 |
|4 |0 |0 |0 |0 |2 |

后手表：
|  |0 |1 |2 |3 |4 |
|--|--|- |- |- |- |
|0 |0 |0 |0 |0 |0 |
|1 |0 |0 |0 |0 |0 |
|2 |0 |0 |0 |0 |0 |
|3 |0 |0 |0 |0 |0 |
|4 |0 |0 |0 |0 |0 |

然后填充一般位置的dp表，由递归模型可以看出先手表中普通位置的元素是通过后手表对应位置的左边一个元素和下边一个元素相加而成。后手表的元素原理相同。
所以dp代码就可以很容易写出了。

``` java

public static int GetMax(int[] arr){
    if(arr ==null || arr.length=0){
        return 0;
    }
    int[][] first=new int[arr.length-1][arr.length-1];
    int[][] second=new int[arr.length-1][arr.length-1];

    for(int i=0;i<arr.length-1;++i){
        first[i][i]=arr[i];
    }
    //从第0行第一列开始
    int row=0,col=1;
    while(col<arr.length){
        int i=row;
        int j=col;
        while(i<arr.length && j<arr.length){
            first[i][j]=Math.max(arr[i]+second[i+1][j],arr[j]+second[i][j-1]);
            second[i][i]=Math.min(arr[i+1][j],arr[i][j-1]);
            //沿着对角线移动
            i++;
            j++;
        }
        col++;
    }
    return Math.max(first[0][arr.length-1],second[0][arr.length-1]);
}
```

递归转dp还是比较简单的，照着递归改就行了。主要是这道题是范围上的尝试。需要建立多张dp表。

## 多个可变参数如何使用dp？

多个可变的参数是指dp表的维度超过二维，达到三维甚至四维。下面是一道例题。

> 请同学们自行搜索或者想象一个象棋的棋盘，然后把整个棋盘放入第一象限，棋盘的最左下 角是(0,0)位置。那么整个棋盘就是横坐标上9条线、纵坐标上10条线的一个区域。给你三个 参数，x，y，k，返回如果“马”从(0,0)位置出发，必须走k步，最后落在(x,y)上的方法数 有多少种？

首先总结出递归的版本。马是走日字的。所以总结出在目标位置周围只走一步就可到达目标位置的点，然后继续，直到步数为0，并且回到(0,0)点。

``` java
public static int routes(int x,int y,int k){
    return recur(x,y,k);
}

public static int recur(int x,int y,int step){
    //越界，没有办法到达目标位置
    if(x<0 || x>9 || y<0 ||y>10){
        return 0;
    }
    if(step==0){
        //能够回到源点且步数为0，表示一条成功路径
        return x==0 && y== 0?1:0;
    }
    return recur(x-1,y+2,step-1)+
            recur(x+1,y+2,step-1)+
            recur(x+2,y+1,step-1)+
            recur(x+2,y-1,step-1)+
            recur(x+1,y-2,step-1)+
            recur(x-1,y-2,step-1)+
            recur(x-2,y-1,step-1)+
            recur(x-2,y+1,step-1);
}
```

这道题目中递归函数与三个变量有关，所以dp表应该是一个三维的，但是注意：三维表中的每一层都只与下一层有关，与当前层的任何点都无关。

``` java
public static int route(int x,int y,int k){
    if(x<0 || x>9 || y<0 ||y>10){
        return 0;
    }
    int[][][] dp=new int[9][10][k+1];
    dp[0][0][0]=1;

    for(int level=1;level<=k;++level){
        for(int i=0;i<9;i++){
            for(int j=0;j<10;j++){
                dp[i][j][level]+=getValue(dp,i-1,j+2,step-1);
                dp[i][j][level]+=getValue(dp,i+1,j+2,step-1);
                dp[i][j][level]+=getValue(dp,i+2,j+1,step-1);
                dp[i][j][level]+=getValue(dp,i+2,j-1,step-1);
                dp[i][j][level]+=getValue(dp,i+1,j-2,step-1);
                dp[i][j][level]+=getValue(dp,i-1,j-2,step-1);
                dp[i][j][level]+=getValue(dp,i-2,j-1,step-1);
                dp[i][j][level]+=getValue(dp,i-2,j+1,step-1);
            }
        }
    }

    return dp[x][y][k];
}
public static int getValue(int[][][] dp,int row,int col,int step){
    if(row<0 || row>9 || col<0|| col>10){
        return 0;
    }
    return dp[row][col][step];
}
```

感觉高维的dp和一维、二维的dp没啥区别啊。就是分析出当前层的值只与下一个层有关，并且立方体外的值都默认为0。然后照着递归就可以写出来了。所以最重要的还是得写出递归函数。

## 包含枚举的dp如何进行优化？

在动态规划中，如果在求一个位置上的数时，需要循环求某一列、或一行等等，反正是要用for循环才能求出一个位置的值怎么办？下面是一道使用斜率优化的题目。

> 有一个一维数组，数目中的每一个数表示一个硬币的面值，每种硬币的数量不限，给定一个目标金额，问有多少种方法组织出目标金额？

首先考虑递归版本：

``` java
//首先依次选择每一种面值，迭代增加使用的张数，直到金额超出
public static int maxWays(int[] arr,int target){
    return recur(arr,0,target);
}

public static int recur(int[] arr,int index,int rest){
    if(index==arr.length){
        return rest==0?1:0;
    }
    int ways=0;
    //迭代不同的张数
    for(int count=0;arr[index]*count<=rest;++count){
        ways+=recur(arr,index+1,rest-arr[index]*count);
    }
    return ways;
}

```

查看递归模型，发现与递归相关的变量只有两个，所以dp表应该是一个二维数组，下面尝试写出dp版本。

``` java
public static int maxWays(int[] arr,int target){
    int[][] dp=new int[arr.length+1][target+1];
    dp[arr.length][0]=1;
    for(int index=arr.length-1;index>=0;++index){
        for(int rest=0;rest<=target;++rest){
            int ways=0;
            for(int count=0;count*arr[index]<=rest;++count){
                ways+=dp[index+1][rest-count*arr[index]];
            }
        }
        dp[index][ways]=ways;
    }
    return dp[0][target];
}
```

如果当前arr[index]的值为1，则上述算法的最差复杂度为O(arr.length*target*target),因为要从最右边迭代到最左边才能求出当前节点的值。那么如何进行优化呢？很简单，就是找规律。当前index行的值就是下一行同一列不断向左迭代累加的和。而这一迭代和却可以通过当前位置的左边的某一位置的和加上正下方的值而求得。这种优化的方法称作斜率优化法。没有为什么，就是简单的找规律。那么什么时候需要这种优化？**在求dp表中某一位置的值时需要用循环才能求出**，就可以考虑这种优化方法。

``` java "dp升级版"
public static int maxWays(int[] arr,int target){
    int[][] dp=new int[arr.length+1][target+1];
    dp[arr.length][0]=1;
    for(int index=arr.length-1;index>=0;++index){
        for(int rest=0;rest<=target;++rest){
            int ways=0;
            for(int count=0;count*arr[index]<=rest;++count){
                dp[index][rest]=dp[index+1][rest];
                if(rest-arr[index]>=0){
                    dp[index][rest]+=dp[index][rest-arr[index]];
                }
            }
        }  
    }
    return dp[0][target];
}


```

我们有从左向右的模型，或范围内的尝试模型，那么如何评价尝试的模型？主要有一下两点标准：

- 单个可变参数的维度最好是一个整数，一定不要是别的类型，一定要弄成整数
- 可变参数的个数尽量少(这里指与递归相关的参数)