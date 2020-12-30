---
title: 位元素题目
mathjax: true
data: 2020-03-27 23:37:45
updated:
tags:
- 位运算
categories:
- algorithm
---
### 使用位运算交换两个数


### 使用位运算查找只出现过一次的元素

### 查看只出现过一次两个元素

### 查看只出现过一次的元素，其余都是三个重复


### 不用比较判断正负数

假设为int32，将数`n`右移31位，就可以获得符号位，然后与`1`与，如果是正数，就为0；如果是负数，就为1。那么具体的返回值便又具体的情况确定。比如为正数返回1，为负数返回0。(这样只需再与`1`或就可取反)。

``` c++
//非负数返回1，负数返回0
int sign(int n){
    //先获得符号位再反转
    return (flip(n>>31&1));
}
//使用flip函数时需要保证输入只能为1或0
int flip(int n){
    return n^1;
}
```

有了以上两个操作之后，我们用它来解决一些问题。
求两个数的最大值但是不能使用比较

``` c++
int GetMax(int a,int b){
    int c=a-b;
    //如果c>0,那么scA=1；如果c<0,scA=0
    int scA=sign(c);
    //进行反转
    int scB=flip(scA);
    //至此，scA和scB是互斥的。
    return scA*a+scB*B;
}
```

但是这种方法`a-b`有可能会溢出。下面是一种不溢出的方法。

``` c++
int GetMax(int a,int b){
    int c=a-b;
    int scA=sign(a);
    int scB=sign(b);
    int scC=sign(c);
    //相同为0，不同为1
    int diff=scA^scB;
    int same=flip(diff);
    //如果最大值为a，那么如果 a、b符号相同，a-b一定不会溢出
    //a-b>=0时最大值就为a，那么就需要看c的符号

    //如果a、b符号不同，如果a>0，那么就可以返回a
    int returnA=diff*scA+same*scC;
    //返回a和返回b的条件肯定是互斥的
    int returnB=flip(returnA);
    return returnA*a+returnB*b;
}

```

### 判断一个数是2的幂和4的幂

**判断2的幂**
如果一个数是2的幂，那么在二进制中只有一个1，所以有两种判断方法：

- 将x减-1，即x&(x-1)==0,如果这个等式成立，就是2的幂
- 或者取得x最右侧的1，即取得只包含右侧第一个1的数，再和原来比较，如果相等，就是2的幂

**判断4的幂**

判断x是否只有一个1，如果不是，那肯定不是4的幂。
然后再判断`x&01010101...01b!=0`是否成立，如果成立，那么就是4的幂。因为4的幂中1只可能出现奇数位(从0开始)。

``` c++
bool Is4Power(int n){
    return(n&(n-1)==0)&&(n&0x55555555)!=0;
}
```

### 使用位运算实现四则运算

> 给定两个有符号32位整数a和b，不能使用算术运算符，分别实现a和b的加、减、乘、除运 算。

**加法**

需要注意：
> 异或就是无进位相加，两个数相与的结果就是进位信息，即1代表这个位置如果相加会产生一个进位，再将进位信息左移一位，就是进位结果。

例如：`a+b`就等效于`a+b`的无进位结果加上`a+b`的进位结果。又因为不能使用加上，再利用上面的流程继续算，知道进位为0时，停止。

``` c++
int add(int a,int b){//如果a+b本身会溢出，那出错是正常的
    int sum{a};
    while(b!=0){
        sum= a^b;//无进位相加
        b=(a&b)<<1;//进位相加
        a=sum;//周而复始，知道进位结果为0
    }
    return sum;
}
```

**减法**
`a-b`就是`a+(-b)`，所以减法变加法

``` c++
//相反数就是取反加1
int negNum(int n){
    return add(~n,1);
}

int minus(int a,int b){
    return add(a,negNum(b));
}
```

**乘法**
乘法有点难度奥。以101b*110为例。

- 乘数第一位为0，与被乘数相乘结果为0
- 乘数第二位为1，相乘的结果就是被乘数`101b`向左移动1位与上一轮的结果相加
- 乘数第三位位1，相乘的结果就是被乘数`101b`向左移动移动两位与上一轮结果相加
- 一直重复上述操作直至乘数为0。

``` c++
int mul(int a,int b){
    int results{0};
    while(b!=0){
        if(b&1!=0){
            results=add(result,a);
        }
        results<<=1;
        b=(unsigned int)>>1;
    }
    return 0;
}

```

> 注意c++中没有无符号右移，需要先强转成无符号数再进行移位。

**除法**

除法就是倒过来的乘法，首先将除数先尽可能的左移，但是不能超过被除数，向左移动的位数就是结果中的一个因子。因为做乘法时就要对结果进行相应的左移。

``` c++
int isNeg(int n){
    return n<0;
}

//使用此方法必须保证a、bj均为同符号
int div(int a,int b){
    int a= isNeg(a)?negNum(a):a;
    int b=isNeg(b)?negNum(b):b;
    int result{0};
    for(int i=31;i>-1;minus(i,1)){
        if((a>>i)>b){//这么做是害怕b左移的时候溢出了，如果a先右移比b大，说明是安全的
            result|=(1<<i);
            a=minus(a,b<<i);
        }
    }
    return isNeg(a)^isNeg(b):negNum(result):result;
}

```