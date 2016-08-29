title: ELF Hash函数
author: Cyno
category: Algorithm
tag: Hash函数
date: 2016-07-02
toc: true

---
字符串Hash无论是在ACM竞赛中还是在工程中都有着广泛的应用，所以很有必要掌握好它的用法。主要分为两个部
分：Hash映射和冲突处理。而本文主要来详细讲解Hash映射的方法及应用，下篇文章将会介绍如何处理冲突。
 
对于字符串Hash来说都是把字符串映射为一个整数，这一步是通过Hash函数来进行的。常用的Hash函数具体有：
SDBMHash，RSHash，JSHash，ELFHash，BKDRHash，DJBHash等等。接下来只详细介绍ELFHash函数的原理
及应用。
 
ELFHash函数的代码如下
```cpp
unsigned int ELFhash(char *str)
{
    unsigned int h = 0;
    unsigned int x;
    while(*str)
    {
        h = (h << 4) + *str++;
        x = h & 0xF0000000L;
        if(x)
        {
            h ^= x>>24;
            h &= ~x;
        }
    }
    return h & 0x7FFFFFFF;
}
```

接下来我会详细探讨它的原理。
 
（1）h = (h << 4) + *str++;  把当前的字符的ASCII存入h的低4位。
（2）x = h & 0xF0000000L;    取出h中最高4位，0xF0000000L地代表28~31这4位是1，其余后28位是0。
（3）如果最高4位不为0，那么说明字符多于7个，现在正在存第8个，如果不处理再加下一个字符时，第一个字符会
    被移出，因为1~4位刚刚加入了新字符，所以不能>>28，而是>>24。
（4）h &= ~x;                表示把h的高4位清零。
 
题目：http://acm.hdu.edu.cn/showproblem.php?pid=1800
 
题意：给定一些数字，可能有前导零，求这些数字中出现次数最多的数字的次数。
 
代码：
```cpp
#include <iostream>
#include <string.h>
#include <stdio.h>

using namespace std;
const int N = 1000005;
const int MOD = 100007;

int hash[N], cnt[N];

unsigned int ELFhash(char *str)
{
    unsigned int h = 0;
    unsigned int x;
    while(*str)
    {
        h = (h << 4) + *str++;
        x = h & 0xF0000000L;
        if(x)
        {
            h ^= x>>24;
            h &= ~x;
        }
    }
    return h & 0x7FFFFFFF;
}

int HashHit(char *str)
{
    while(*str == '0') str++;
    int k = ELFhash(str);
    int t = k % MOD;
    while(hash[t] != k && hash[t] != -1)
        t = (t + 10) % MOD;
    if(hash[t] == -1)
    {
        cnt[t] = 1;
        hash[t] = k;
    }
    else cnt[t]++;
    return cnt[t];
}

int main()
{
    int n;
    char str[105];
    while(scanf("%d", &n)!=EOF)
    {
        int ans = 1;
        memset(hash,-1,sizeof(hash));
        while(n--)
        {
            scanf("%s", str);
            ans = max(ans, HashHit(str));
        }
        printf("%d\n", ans);
    }
    return 0;
}
```
问题：为什么Hash表的size总是扩展成一个素数？
 
分析：素数可以有效地减少冲突。具体原因如下
     假设Hash表的大小为size，这是一个合数，即有size = a * n，当有Hash值为HashCode = b * n，则
     HashCode取模之后有
 ![](http://img.blog.csdn.net/20140901162619961)
    
 
     因为是固定不变的，那么HashCode取值就有了种可能，这样显然会增加冲突的概率。
